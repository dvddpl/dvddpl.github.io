---
#articleId: 1484383

title: "There is no right or wrong, as long as it works - Actually, how you use DynamoDB makes a lot of difference!"
published: true
description: "let's have a look at how a proof of concept pushed to production went bad and how we can improve the performances of a backend querying multiple DynamoDB tables while at the same time reducing costs"
tags: serverless dynamodb bestpractices techlead
background: /assets/liz9mc0aq0i176g5blh4.png
# Use a ratio of 100:42 for best results.
# published_at: 2023-05-29 07:59 +0000
canonical_url: https://dev.to/aws-builders/there-is-no-right-or-wrong-as-long-as-it-works-actually-how-you-use-dynamodb-makes-a-lot-of-difference-2668
---

Some time ago a friend of mine asked me for advice about a serverless app he was working on.
He had always worked as a frontend developer but due to some layoffs in his company, he had to fix/add features to the backend too.

The backend consisted of a simple ApiGateway + Lambda + Dynamo and specifically he was dealing with an issue aggregating data from multiple tables and editing records.

He was struggling in understanding _how_ things were done and most importantly _why_ they were done that way, his experienced developer's 6th sense warned him about something fishy, but when you start with something new, be it a language or framework, and you happen to be working on an existing codebase, that has been running on production for at least a couple of years, you tend to assume that what you see is the way to go and you tend to learn from those examples and the patterns you find.

That's why he approached me. 
Just by looking at some metrics in AWS Cloudwatch and at the AWS Cost Explorer, **it was pretty evident that something was _off_ in the way the database had been modelled, or at least in how it was queried**: compared to the number of requests to the backend, the number of Read and Write Capacity Units consumed was very very high!

# Nothing is wrong here!
 
Before we dive into the coding part, let me tell you a story:

I am a passionate climber and recently, wanting to push a bit beyond my current skills I decided to hire a trainer.  
After one of the first climbs she asked me how it went, if I noticed something in the way I approached and concluded the route.

> How did it go?
Not really great, I made some mistakes while passing the _crux_[^1].

> But you reached the top! That's what matters, wasn't that your goal?
Naaa, I really got _the beta_[^3]  wrong. 

> But you managed it, so why do you think you did it _wrong_?
Because I was slow, my movements were not elegant at all and I felt really _pumped_[^2]  long before the top.

> Alright, but that it's not a matter of _right or wrong_.  You reached the top, so you did good. You haven't climbed _wrong_, you just climbed **inefficiently**. 
You had not properly visualised your movements before you started, you tried different moves along the route, used your body weight and positioned your hands and foot in a way that costed you time and therefore energy.
If your goal is to climb faster, longer or harder you need to **pay more attention at how efficient you move and at how you utilise your resources** ( vision, breathing, explosive energy, stamina, mental strength, recovery time and so on).

<blockquote class="instagram-media" data-instgrm-permalink="https://www.instagram.com/p/CqJQY2TDm-m/" data-instgrm-version="14"></blockquote>

# It's working, who cares about _how_!

That immediately reminded me at the many times when during meetings at work ( be it discussions about the possibility of a refactor, a code review or just the estimate of a new feature) someone dropped the classic:

> **nobody cares about the code**, as long as it satisfies the users needs, works and it shipped on time.

Who cares if it's NodeJs or Python, if it uses DynamoDB or PostgreSQL, if it's Clean Code or a Big ball of Mud, if it has tests or if it's an undocumented untestable mess - as long as it works, it's OK!

As long as it works.

Of course no one cares _as long_ as it works, because no one will notice how things are done until a big bug happens on production or the Cloud bill starts growing wild, or any new feature starts taking ages to be implemented and introduces exponentially more bugs. 

**Technical Debt creeps in** sprint after sprint and its poor code quality or ~~wrong~~, ~~bad~~, sorry inefficient/not-sustainable/unscalable architectural choices show their nefarious only after weeks or months or even years. 

Will it become a problem? we don't know, but we might know if we **reason about our goals and about purpose**.

> Is this piece of code a temporary feature?
Is this app just a proof of concept?
Do I need to hit the market as fast as possible, will it need to scale ?  
How much will it cost? 
Am I taking into account the possibility that if everything goes well I will need some future buffer to fix the shortcuts I am taking?

In the case of my friend's code base, the backend was written by a contractor engaged to write a proof of concept. The code was then simply pushed to production and new functionality was added by inexperienced backend developers (like my friend) following the **poor coding practices** of a _prototype_, not the best practices a production app should have. 

That resulted in an **undocumented untestable mess**, that indeed absolutely did the job, but at the expense of the poor developers that had to maintain it and of the management that paid the AWS bill ( who did not know and does not care, as long the app is making a profit ).

So, what was ~~wrong~~ inefficient, in my friend's code base?
It turned out that

- the **Database model was inappropriate** considering how the app grew since the prototype (using NoSQL with more than 25 tables and different secondary indexes)
- the queries to gather data were sort of _relational_ and were not optimised properly ( being run simply in a sequence) - those queries were returning way more data that was needed, with considerable weight and complexity in aggregating and filtering out data that was not necessary
- since the edit operation always overwrote the entire row, loading the row was necessary in order to just edit a couple of properties
- there was no caching whatsoever

# Deep Dive into DynamoDB 
Let's take a closer look at the advice I gave to my friend to **improve the code base**, **reduce latency** and **cut costs** altogether.

## Favour Single Table Design
I won't go into the details of Single Table Design vs Multi Table design too much, since AWS Hero Alex De Brie wrote extensively about it:
you can read really interesting stuff [here](https://www.alexdebrie.com/posts/dynamodb-single-table/) and [here](https://aws.amazon.com/de/blogs/database/single-table-vs-multi-table-design-in-amazon-dynamodb/) and watch this mindblowing talk by Rick Houlihan.

<iframe width="560" height="315" src="https://www.youtube.com/embed/xfxBhvGpoa0" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

but to put it simply, using a Single Table Design means that you have rows containing different data and you use specific access patterns with Partition and Sort Keys and prefixes and Secondary indexes to load all/only the data you need.
 
Starting out with multiple tables can be simpler: 
- need to store app configuration? here a configTable!
- need to store user data? here, a userDataTable!
- need to add user friends? here a userFriendsTable for you!
- need to add user orders? tadaa! a userOrdersTable ready for you!
- and so on in a very much Relational way of doing things 

This approach is not only **not leveraging NoSQL** and the amazing capabilities of DynamoDB, but will also in the long run ( actually, pretty soon!) reveal quite impractical and requiring to make multiple requests to multiple tables and aggregating/joining data on your side - which bring us to the next point.

## Optimise queries to DynamoDB tables
When the app needed some aggregation of data, the backend was often querying multiple tables at once, then composing an object with all the needed props and returning it to the client.

Continuing the example above, to gather all UserData with friends and orders you need to retrieve the right row from UserData table + the Orders of that UserID ( using a secondary index, because OrderTable has a PK with orderId) and then friends from Friends Table.

The implementation was suboptimal: requests were done in order, one after another, only to aggregate data when everything was finally available like in this pseudo code simplified way :

```
   const userData = loadUser(id)
        .then(loadTheirOrders)
        .then(loadTheirFriends)
```
or
```
const userData = await loadUser(id)
const userFriends = await loadFriends(userData.id)
const userOrders = await loadOrders(userData.id)
const fullUserData = {
    basicData: userData,
    friends: userFriends,
    orders: userOrders
}
```

Much better would have been to **make requests in parallel** (so that you wait only for the one taking the longest, not for the sum of the 3): 

```
const promises = [loadUser(id), loadFriends(userData.id), loadOrders(userData.id)]
const fullUser = await Promise.all(promises)  
// you still need to grab and aggregate the result from all the promises, but hopefully you got what i mean
```

Much much better, run 1 single call to dynamo with a
[BatchGetItemCommand](
https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/clients/client-dynamodb/classes/batchgetitemcommand.html):

```
 const batchGetItemInput = {
        RequestItems: {
            "table_one": {
                Keys: [
                    {
                        YOUR_PRIMARY_KEY: {S: id},
                        YOUR_SORT_KEY: {S: sortValue}
                    }
                    // here you can even load multiple users at the same time
                ]
            },
            "table_two": {
                Keys: [
                    {YOUR_PRIMARY_KEY: {S: id}},
                    // Add more key-value pairs as needed
                ],
            },
            // Add more table names and keys as needed
        },
    }

const batchResponse = await dynamoDocClient.send(new BatchGetItemCommand(batchGetItemInput))
    return response.Responses. 

// you still need to grab your data and combine it as you like it but you get everything loaded at once, with just one request!
```

Batch Commands can be very powerful.  Especially for Transactions and multiple operations at once. I really recommend [checking them out](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/dynamodb-example-table-read-write-batch.html)

Small note: weirdly I was not able to use the BatchCommand without specifying the DynamoDataTypes in the KeyValueAttributes ( something that normally using the DocumentClient is not necessary) 

## Load only what you need

In many cases, the backend code was loading the entire row from a table even though **the data that was necessary was just one property**.

> To read data from a table, you use operations such as GetItem, Query, or Scan. Amazon DynamoDB returns all the item attributes by default. To get only some, rather than all of the attributes, use [a projection expression](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ProjectionExpressions.html).

So, if you just need the score and the language of a user, you don't need to load the entire user data, but just those two attributes,  it is as simple as specifying those properties in the `GetItemCommandInput`
```
ProjectionExpression: "score, language"`,
```

I know, I know,  using **Projection Expression does have not any effect whatsoever on the cost of your request** ( like Filters, they are applied _after_ the data has been retrieved by DynamoDB, therefore there is no difference in terms of RCUs (Read Capacity Units). Nevertheless, there is fewer data sent out over the wire, which can have positive effects on latency ( and on your users' data-plan consumption ) and in my opinion makes manipulating the response easier and the code cleaner. 

## Update only what changed

Assuming you want to change the email address or update the user loyalty points of a user, you would ideally receive from the client just the user_id and the new value and you would then _update_ just those properties in the database.

Similarly to the way the requests were sequentially made to multiple tables and entire data was passed around, editing operations were written in a very simple and un-optimised way.

- Query the entire row
- Edit a couple of properties in the loaded object
- Overwrite the row with the modified object

```
  const parameters = {
        TableName: table,
        Key: {
            UserId: id
        }
    }
    const user = await dynamoDocClient.send(new GetCommand(parameters))

user.emailAddress = newEmailAddress
user.score += 100

const parameters = {
        TableName: table,
        Item: {
            ...user,
        }
    }
const response = await dynamoDocClient.send(new PutCommand(parameters))
```
DynamoDB though, has an UpdateItemCommand that allows us exactly to **update only specific attributes of a record**, without requiring us to query for it - assuming we know its partition key (and eventually the sort key).

```
const parameters = {
        Key: {
            USER_ID: id,
            SK: sk // sort key - if you have one
        },
        TableName: "YOUR_TABLE_NAME",
        ReturnValues: 'ALL_NEW',
        UpdateExpression:
            'ADD #score :v_score',
        ExpressionAttributeNames: {
            '#score': 'score'
        },
        ExpressionAttributeValues: {
            ':v_score': scoreChange,
        }
    }
const response = await dynamoDocClient.send(new UpdateCommand(parameters))
    return response.Attributes
```

Dynamo comes with different handy UpdateExpressions ( `Set` to modify a value, `ADD` to increase/decrease numbers, `REMOVE` to remove attributes entirely, and so on) and most importantly you can even use **[ConditionExpressions](
https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.ConditionExpressions.html#Expressions.ConditionExpressions.SimpleComparisons)** to determine which items should be modified: If the condition expression evaluates to true, the operation succeeds; otherwise, the operation fails.

I wrote a post some time ago about how to dynamically create Dynamic Update Expression from an object of modified properties - read it [here](https://dev.to/dvddpl/dynamodb-dynamic-method-to-insert-or-edit-an-item-5fnh)

### Want to know how that is affecting costs?

> - A strongly consistent read request of an item up to 4 KB requires one read request unit.
- An eventually consistent read request of an item up to 4 KB requires one-half read request unit.
- A transactional read request of an item up to 4 KB requires two read request units.
**If you need to read an item that is larger than 4 KB, DynamoDB needs additional read request units.**   
from the [docs](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.ReadWriteCapacityMode.html#HowItWorks.requests)

After a simple refactoring, we changed the previous update method ( which was every time loading the entire row, to edit just a couple of properties and then overwrite the entire row with the modified object ) into a smaller method whose responsibility was receiving the PK and SK and the props/values that had change and taking advantage of the UpdateItem method of DynamoDB.

In our samples where the rows had just a few attributes and very little data this was the difference:

> get-edit-put: 101.073ms --> 1 WCU and 0.5 RCU
update: 46.894ms -->  1 WCU

**Premature optimisation**? Just **overkill** to shave off a few milliseconds?  At the time of the prototype or when the application was launched to production probably it could have been considered so, but looking at how the app usage has increased and the code has evolved, (**thousands of requests per minute and records requiring 5 RCU each time** ) a really meaningful change.  

I agree, the syntax of Update Expressions is not the best, and it is definitely uglier and more complicated than editing a javascript object:

Just compare 
```
// pseudocode
const object = getItem(id)
obj.name = "new_name"
obj.address= "new_address"
obj.orders = [1,2,3]
writeItem(obj)
// 
```
to:

```
UpdateExpression:
        'set #name = :v_name, #address = :v_address',
    ExpressionAttributeNames: {
        '#name': 'name',
        '#address': 'address'
    },
    ExpressionAttributeValues: {
        ':v_name': "new_name",
        ':v_address': "new_address"
    },

updateItem(params)
```
but UdpateExpressions allow us to express intent more clearly and most importantly leverage the capabilities of DynamoDB.

### Some more findings

Something interesting I found out while writing some samples for this post is **the difference between List and StringSet in the [DynamoDB DataTypes](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/HowItWorks.NamingRulesDataTypes.html#HowItWorks.DataTypes)**.

Since I always used NodeJs and the [DynamoDBDocumentClient](https://docs.aws.amazon.com/AWSJavaScriptSDK/v3/latest/classes/_aws_sdk_lib_dynamodb.dynamodbdocumentclient-1.html) I never had to bother about Marshalling and Unmarshalling and defining the data types: just pass a JSON or a JS object and DynamoDBDocumentClient takes care of everything.

While testing the Update Expression though, I struggled with removing items from an array/list.

Adding is simple, you don't need to know the previous list of orders to be able to push a new one and overwrite the data on the database,  just create an Update Expression with the new order id and dynamo will append it to the list when updating the item. (more in the [docs](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Expressions.UpdateExpressions.html#Expressions.UpdateExpressions.SET.UpdatingListElements))
```
        Expression = "SET #orders = list_append(#orders, :v_orderId)"
        ExpressionAttributeNames: {
            '#orders': 'orders'
        },
        ExpressionAttributeValues: {
            ':v_orderId': [orderChange.id],
        },
    }
```
To remove an item on the other hand, you need to specify the index, which is not something very handy, or something that you might know.
```
   UpdateExpression :"REMOVE #orders[1]",
   ExpressionAttributeNames: {
            '#orders': 'orders'
        }
```

The docs state that **in the case of Sets you can actually use the DELETE operator** but whatever I tried  was failing due to: 

> _Incorrect operand type for operator or function; operator: DELETE, operand type: LIST, typeSet: ALLOWED_FOR_DELETE_OPERAND_

In fact, checking my record in the Dynamo UI Console, my _orders_ were a simple List, not a String Set.
When I tried to create manually from the console a new Attribute as a String Set I noticed that suddenly the console was not giving me the possibility to switch off the View DynamoDB JSON  thus forcibly showing the data types.

![Image description](/assets/pndxrp3jguopwhrlxlud.png)

```
  "moarOrders": {
    "SS": [
      "11111",
      "99999"
    ]
  },
  "orders": {
    "L": [
      {
        "S": "12345"
      },
      {
        "S": "67890"
      }
    ]
  },
```
instead of the simplified 
```
 "orders": [
  "12345",
  "67890"
 ]
```

Apparently then, if using DynamoDBDocumentClient, which has the advantage of saving you the marshalling/unmarshalling, you also loose some of the DynamoDB DataTypes and thus you can't take advantage of specific update expressions (Drop a comment with a snippet if you know how to do that! it would be highly appreciated!)

## Cache what does not change frequently
Some of the data that was loaded from Dynamo and returned to the Client was not user specific, it could be generic app configuration,  lists of available products to buy,  feature flags, stats from the previous day and so on.   But they were loaded from DynamoDB every time, for every user connecting.

Having Cloudfront before your API Gateway could be a valid solution to cache data and reduce the number of requests hitting your endpoint --> lambda --> dynamo.  
Of course Cloudfront is not free, and you need to understand how frequently your data changes, and if it makes sense to shift costs to the CDN, but having data cached would also have benefits on the responsiveness of your app.

If you can't or don't want to use a CDN you can consider other in-memory caches to avoid sending the requests to Dynamo -  the risk of having stale data among Lambdas or different containers is high though, so unless you have a Fat Lambda and you need the same data for multiple endpoints/methods managed by the same handler I would not suggest it).
But there are also other solutions, that come at a cost, like [Momento](https://www.gomomento.com/blog/using-a-cache-to-accelerate-dynamodb-or-replace-it) or [AWS DAX](https://aws.amazon.com/de/dynamodb/dax/)


#Recap

In this post I wanted to highlight the fact that usually it is really not that important how things are done.  But at some point,  we need to consider where we want to go next, we need to reconsider the choices we have made and we might need to face the fact that before moving on, some changes are necessary.

Overall, my advice is:

- take time to think
- start simple and iterate
- take the time to tackle tech debt
- RTFM:  often when dealing with new problems or starting out with new languages we stick with what we know and use old approaches. This is the case with DynamoDB, if you always used relational Databases, or with functional programming if you come from strictly type and object oriented languages, or when adopting a serverless event driven mindset.   **Invest some time in reading the docs and understanding the tool you are using.**
- **avoid judgement and blame** - often, when looking at some legacy code, we tend to ask ourselves:  
> how the heck could that guy end up writing this crap?
  how is it even possible that the architect/tech lead made this silly decision?

the answer is that they did not know what we know now, **in hindsight everything is obvious and simple**, they could not predict if their proof of concept would evolve into a full blown production app nor if that app would have 500 users per day or 10 thousand per minute. 
Always be patient and forgiving about whatever you find in a legacy code base, and whoever was responsible for writing it.

Specifically to DynamoDB these are my suggestions:
- use Single Table Design 
- use Batch whenever possible
- leverage UpdateExpression and Condition Expressions


Hope it helps!


Other related articles you might find interesting:

- [DynamoDB - dynamic method to insert or edit an item](https://dev.to/dvddpl/dynamodb-dynamic-method-to-insert-or-edit-an-item-5fnh)
- [Stop, Think, Code - Iterate](https://dev.to/dvddpl/stop-think-code-iterate-4d56)
- [Make mistakes, and ask questions. It's OK!](https://dev.to/dvddpl/make-mistakes-and-ask-questions-its-ok-gah)
-[Strive for simplicity](https://dev.to/dvddpl/strive-for-simplicity-2131)
- [Keep your Ego away from coding](https://dev.to/dvddpl/strive-for-simplicity-2131)
- [Software Engineering is about trade-offs: make sure you have options! (architecture patterns comparison)](https://dev.to/aws-builders/software-engineering-is-about-trade-offs-make-sure-you-have-options-architecture-patterns-comparison-p00)
- [Working on legacy code-bases can make us better developers, here is why.](https://dev.to/dvddpl/working-on-legacy-code-bases-can-make-us-better-developers-here-is-why-n6g)


[^1]: The _crux_ is the hardest part of a route
[^2]: tight, swollen, burning, and sometimes painful feeling that occurs in our forearms when we're climbing as a result of lactic acid build up.
[^3]: (to climb a route - also referred as _to solve the problem_ - you have to visualise the movements you will make, the beta is https://en.wikipedia.org/wiki/Beta_(climbing)

---
Foto von <a href="https://unsplash.com/@lee2d2?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">lee attwood</a> auf <a href="https://unsplash.com/de/fotos/1eQBP3Iit48?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
  
