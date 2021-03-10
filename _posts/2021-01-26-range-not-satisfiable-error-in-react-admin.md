---
title: React Admin and Range Not Satisfiable Error
published: true
description: a nasty bug about http headers after simply updating a npm dependency
tags: debugging, react, api, node
---

TL;DR

If after updating your ReactAdmin (and therefore your ra-data-simple-rest to version 3.10.4) you are wondering why your API requests fail due to Status Code 416, you might have to update your backend to return `x-total-count` header instead of the old `content-range` and specify in your frontend data provider that the new header is being used:


``` 
simpleRestProvider('http://path.to.my.api/', fetchUtils.fetchJson, 'X-Total-Count')
```

![416 Range not Satisfiable Error](/assets/images/416error.png)

----  
Recently we updated the dependencies on a project which we didn't touch for about about 6 months.  Of course, after running `npm outdated` we found A LOT.

The update though, wasn't so difficult, besides one very weird error which we encountered only at runtime. 
Every single request made by our Application, built using [React Admin, a very powerful and handy frontend framework](https://marmelab.com/react-admin/), was crashing because of **416 Range Not Satisfiable Error**.

The same request was perfectly fine when executed via Postman.

We knew immediately it must have something to do with some **HTTP Headers** React-Admin uses to handle pagination, but we could not figure out why.  
We were sending the requested headers [as described in the docs](https://marmelab.com/react-admin/DataProviders.html#usage) and we were properly exposing them from our backend. 

Basically, the frontend sends a query param specifying the range/pagination:  `users?range=[25,49]` would mean we are requesting the second page of results in chunks of 25 elements, and the backend has to return the response including the Content-Range header with that pagination info:

```    {
headers: 
    {
    'Access-Control-Expose-Headers': 'Content-Range',
      'Content-Range': 'users 25-49/315'
    }
  }
```

According to the [Official React Admin documentation](https://marmelab.com/react-admin/Readme.html) and [Upgrade and the Changelog](https://github.com/marmelab/react-admin/blob/master/UPGRADE.md) in Github, nothing pointed to changes required in the way we should make requests nor changes in the backend.

We debugged a lot, and tried out lots of things, until - **it should have been the very first thing to do actually** - we went to [the commits history of the React-Admin Simple-Rest-Data-Provider](https://github.com/marmelab/react-admin/commit/4a60ffa26b5d63c129e7dc8f6c0d7223a9aaea9c#diff-486dddab295c10b1022194007b982aa4554c0a7bf0cd62dc747d991119a0b44e) and we noticed that something was indeed changed in regards with the headers being sent out with the requests.  

![ra-data-provider-commit](/assets/images/ra-datea-provider-commit.png)

Still, changes did not really explain why the requests were suddenly failing.
Still, that was apparently the cause of the issue, since by forcibly removing, just before being sent by the httpclient, that new `range` header after it was added by the data provider, the problem disappeared and our Content-Range header was properly shown and used by ReactAdmin.  

Of course, that could _not_ have been our fix, so I kept reading and googling until [I found out that the original Content-Range](https://github.com/marmelab/react-admin/tree/master/packages/ra-data-simple-rest#note-about-content-range) approach for pagination used by ReactAdmin was somehow **hacky** and it is best to use X-Total-Count header.

In our backend, we removed the Header ContentRange entirely and used `x-total-count` instead, in our frontend we simply added the type of Count Header we were going to use to the parameter in the dataProvider/httpClient instance, and the problem was gone.


```
// in the backend
response.headers['X-Total-Count'] = results.total 
response.statusCode= 200,
response.body= JSON.stringify(results.data)


// in the frontend
simpleRestProvider('http://path.to.my.api/', fetchUtils.fetchJson, 'X-Total-Count')
```

I am happy having fixed this, still, it is not really clear to me what was wrong with the old Content-Range header, which according to the documentation, was still valid, but from our experience somehow conflicted with the new Range header added in `ra-data-simple-rest@3.10.4`.

I created a [stack overflow question](https://stackoverflow.com/questions/65901479/after-update-of-react-admin-simple-rest-data-provider-all-requests-return-416-ra), this issue blocked us for quite some time and might come in handy for someone, and I hope the maintainers of ReactAdmin can shed some light if we were doing something wrong in our usage or if we really bumped into a bug which nobody experienced before, and maybe improve the documentation.  Besides this episode, ReactAdmin documentation is usually very clear and extensive and this Framework is really useful!


Hope it helps