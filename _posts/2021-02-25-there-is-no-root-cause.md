---
title: What do you mean with 'There is no root cause'?
published: true
description: What is Chaos Engineering? How do you debug failures in your system? How do you track down the root cause of those failures?
tags: chaoseengineering architecture debugging testing
background: https://dev-to-uploads.s3.amazonaws.com/i/piw4tdybb05ttzb0sbfx.png
---

### What is Chaos Engineering? 
How do you debug failures in your system? How do you track down the root cause of those failures?

In one of our last projects, I started using [Failure-Lambda module](https://github.com/gunnargrosch/failure-lambda) from Gunnar Grosch to be able to inject failure in our system.  Not yet in production, but it helped a lot during the test and staging phase to detect and handle possible errors that we overlooked. (If you want to know more about Chaos Engineering and Failure Lambda [check out this post](https://dev.to/dvddpl/make-your-lambda-fail-use-chaos-engineering-to-improve-the-resiliency-of-your-serverless-application-34h))
So I was very eager to see his speech about Chaos Engineering at the [Hamburg Serverless Days](https://hamburg.serverlessdays.io/speakers/gunnar-grosch/). 

On the page explaining the talk, I noticed this picture, and my attention was drawn by **his T-shirt**.

![There is no root cause](https://dev-to-uploads.s3.amazonaws.com/i/txroo307fxiw1en5s92f.jpg)

### There is No Root cause

> What does he mean?  Why there is no root cause? How can there be NO root cause?

I was always an advocate of the **Rule of the 5 Whys**.
Whenever there is a bug, a failure, **don't stop at the symptom**, don't even stop at the first possible cause. 
Go deep, Go further in your investigation. Ask questions. Again and Again.
Like a kid. _Why? Ok, but why? Oh, and why?_ 

![why](https://media.giphy.com/media/xTiIzRkLahCajWKj1C/giphy.gif)

Until you find an answer, the root cause ( but normally, unlike kids, we should stop at about 5 whys)

And now, (I had to google and do some research), it turns out that THERE IS NO ROOT CAUSE?

Well, actually, it makes perfect sense.  
We, simple humans and engineers, want to find one, and one only root cause, for all our problems.  So that we can fix that and the problem disappears.
It would be nice, wouldn't it?

### One cause to solve all problems

But in life, and in complex systems, be it nature, markets, or distributed systems, there is not _one single root cause_, but always instead **many multiple forces that interact together to get you a particular effect.**

> Each necessary, but only jointly sufficient

There is no root cause for happiness (or unhappiness), there is no root cause for success, there is no root cause for an economic crisis, for migrations.  
Though there is indeed a root cause for a specific bug in your code, there might not be one for a failure in a complex serverless application. There are so many layers, so many interdependencies, that the failure was caused not by one specific reason, rather by multiple contributing factors. 

Like in the **swiss cheese model**, where you increase security by having different layers, but you just reduce the chance of holes overlapping. 

That does not mean, we should stop looking for holes or bugs, it means, and this is usually what is being done in **PostMortems**, or being asked by managers, **don't just focus on one root cause**, especially if that is just **Human Error**. 
 
### Striving for a solution vs giving up

There is no root cause, does not mean passivity, does not mean, well, if there is not ONE CAUSE I cannot find a solution. it means that the solution is not so simple, that the problem must be considered by multiple perspectives or attacked on many fronts.

![not so simple](https://media.giphy.com/media/3o7TKwUaQ1pjiAqidy/giphy.gif)

> For every complex problem, there is an answer that is clear, simple, and **wrong**.
H. L. Mencken

Don´t give up. Don´t stop asking why, but don´t obsess in finding one cause and the magic wand to make it disappear.


Some useful links:

[There is no root cause](https://safetyrisk.net/there-is-no-root-cause/)
[No root causes in complexity](https://www.jenal.org/there-are-no-root-causes-in-complexity/)

and an always amazing Louis CK video about kids and whys 
[![kids questions - LouisCK](http://img.youtube.com/vi/WZ32BZT9pnk/0.jpg)](https://www.youtube.com/watch?v=WZ32BZT9pnk)


---- 

<span>Photo by <a href="https://unsplash.com/@brett_jordan?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Brett Jordan</a> on <a href="https://unsplash.com/s/photos/chaos?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>