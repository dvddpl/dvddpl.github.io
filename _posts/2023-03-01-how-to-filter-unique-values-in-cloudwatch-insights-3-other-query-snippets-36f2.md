---
#articleId: 1375409

title: "How to Filter Unique values in CloudWatch Insights  (+ 3 other query snippets)"
published: true
description: "some useful snippets to investigate your issue using CloudWatch Insights queries"
tags: debugging aws techlead beginner
# cover_image: https://direct_url_to_image.jpg
# Use a ratio of 100:42 for best results.
# published_at: 2023-02-22 14:42 +0000
canonical_url: https://dev.to/aws-builders/how-to-filter-unique-values-in-cloudwatch-insights-3-other-query-snippets-36f2
---

##Distinct and Stats

Recently I was investigating a bug where Lambda could not load an image from an S3 Bucket.
I knew we catching the S3 Bucket error and logging a custom ImageLoadFailed exception.

I wanted to figure out what were those images failing ( likely a typo in the name used by the client, or the image referenced by the client was really missing? )and went to [CloudWatch Insights](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html).

```
fields error.message
| filter error.message like /ImageLoadFailed/
```

This query return quite a long list of results, but most of them were related to the same image ( and that prevented me to properly have a feeling of _how many_ images were broken/missing - expecially if reducing (_limit_) the number of the results.

> ImageLoadFailed: a/pic1.svg
ImageLoadFailed: a/pic1.svg
ImageLoadFailed: b/pic2.svg
ImageLoadFailed: a/pic1.svg
ImageLoadFailed: b/pic2.svg
ImageLoadFailed: a/pic1.svg
ImageLoadFailed: b/pic2.svg
ImageLoadFailed: b/pic2.svg
ImageLoadFailed: c/pic3.svg
ImageLoadFailed: a/pic1.svg
ImageLoadFailed: b/pic2.svg
ImageLoadFailed: a/pic1.svg

I tried using  _count_distinct_, which was helpful to understand how many images were affected, but then I would not know which!

```
fields error.message, @timestamp
| filter error.message like /ImageLoadFailed/
| stats count_distinct(error.message)
```

> only display the count (3)

Thanks to a [comment in stackoverflow](https://stackoverflow.com/a/62808500) I was able to aggregate all similar error.messages and just pick the latest and then display it.

```
stats latest(@timestamp) as @latestTimestamp by error.message
| filter error.message like /ImageLoadFailed/
| display error.message
```

> ImageLoadFailed: a/pic1.svg
ImageLoadFailed: b/pic2.svg
ImageLoadFailed: c/pic3.svg

Tadaa! 

## Parse

Parse is very useful if you have nested JSON in your comments or if for some reason you are not using structured logging.
This was the case recently when I was debugging an application written in GO, where the errors were just strings.

> Field	Value
@ingestionTime	1677655126331
@log myaccount:my-app-prod
@logStream listener/web/12e34aee0fd245f1234678c3f903e159f
@message time="2023-03-01T07:18:41Z" **level=error msg=callFailed aParam=something errorMsg="ops! something wrong happen here!" method=GET path="/v1/config/{aParam}/{id}/{param2}" id=1234 param2=somethingElse**
@timestamp 1677655121496

Yes, unfortunately the error was not an object therefore I could not just access and print/query specific values. Using _parse_ and playing a bit with _wildcards_ I was able to query and print out some meaningful informations

```
fields @message
| filter @message like /level=error/
| parse @message "level=error msg=* errorMsg=\"*\" *" as msg, error, params
| display msg, error, params
| limit 50
```

This will print  `callFailed |  ops! something wrong happen here! | all the rest in the string`


## Filter out clutter
Sometimes while investigating an issue in CloudWatch Insights queries return a lot of rows logged from the Lambda, and most of them are not really relevant to your logs, because are by default logged by Lambda itself.
That's why I add this filter to exclude from my results all the Start / End / Report logs.
By removing all the clutter I can then focus in checking our own logs and start building my specific query.

```
| filter @type not in ['START','END', 'REPORT']

```

## Count by frequency

```
fields @timestamp, @message
| filter @message like 'SomeRequestSubmitted'
| sort @timestamp desc
| stats count() as requests by  bin(1d) 
```
This will return the number of SomeRequestSubmitted being logged in a day (of course you can change the aggregation by 5 min or 6 hour or any value you like) and it is very useful to investigate or monitor the frequency of a specific issue.
If you really need to monitor the occurrence and possibly have alarms if it goes over or below a specific threshold, I would suggest [publishing Custom Metrics and Dimensions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/publishingMetrics.html) and creating Alarms based on those.
If you are already logging, and you can't - or don't want to - change the code and implement custom metrics, this CloudWatch Insight query is absolutely valid ( and you can directly add it to a Dashboard and from that create an Alarm anyway!)


---

If you want to know more about how to write more complex Queries, have a look at the [official documentation[](
https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax.html).

Here you can find [some more examples(https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/CWL_QuerySyntax-examples.html)


Hope it helps

Please share in the comments your snippets, if you have some other useful tips!
