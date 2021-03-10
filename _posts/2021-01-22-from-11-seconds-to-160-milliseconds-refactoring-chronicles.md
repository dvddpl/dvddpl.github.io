---
title: From 11 seconds to 160 milliseconds 🚀 - Refactoring Chronicles  
published: true
description: Learn how MySQL Queries and Async Await can affect performance
tags: javascript mysql performance debugging
#cover_image: ./images/marc-olivier-jodoin-NqOInJ-ttqM-unsplash.jpg
background: /assets/images/marc-olivier-jodoin-NqOInJ-ttqM-unsplash.jpg

---

TL;DR

If your endpoints are slow while fetching data from DB, check how you are dealing with **multiple asynchronous requests** and how to optimize the queries:

- use Promise.all instead of awaiting everything
- use eager loading when it makes sense

-----

Recently one of our endpoints started to occasionally timeout.
It is an API Gateway + Lambda + Aurora Serverless which is invoked by an **ETL** from another department: infrequent usage, unpredictable loads, though never huge - sometimes the data retrieved could be just a just a bunch of DB rows sometimes some hundreds. 

So why was the Lambda timing out?

Depending on the filter passed to the API **the query was taking longer than the 10 seconds originally set as Lambda Timeout.** 
Of course increasing the Timeout was not the solution.  ( and at first we did exactly that, until sometimes **we hit the APIGateway timeout hard limit of 29 seconds**. 

It was clear we should investigate the issue. 

We use [Sequelize](https://sequelize.org/) ( a very powerful **ORM**) to connect and run queries.  

The query is relatively complex. Our Model has multiple associations  (some _1:1_ some _1:m_ and even some _m:m_ relationships) and the query must retrieve the entire data from all of them, if the filter conditions match.

To put it simply, imagine we have a User Table, a user can have many Pictures, many Contact information, a list of Tags that describe it and something more.  
All this additional information usually comes from a different Table. 


The query look like this:

```
const loadUsers = async (filter) => {
    const users = await Users.findAll(filter)
    return Promise.all(users.map(lazyLoad))
}

const lazyLoad = async user => {
    const pictures = await user.getPictures()
    const tags = await user.getTags()
    const contacts = await user.getContacts()
    const moreData = await user.getMoreData()
// some data manipulation here to build a complexObject with all the data - not relevant
    return complexUserWithAllData
}
```

Nothing fancy.  A query to load the data, and 4 other separate queries to lazy load the data from the associations (other table with data related to the Users)

Of course the amount of information in the Database grew over time,  so the number of columns and the related tables. 
Also the query was modified over time to fit all the data we were being requested from the ETL.

So there is definitely a problem of performance that gradually piled up as soon as we added complexity to the query.


Can you spot the problem?


### Async await can be your friend and can be your enemy

Async Await is awesome,  it allows to keep your code nice and clean. Understand and debug what is happening without _callbacks hell_ nor with lots of _.then_ indentations.

But often we don't need to **await** like that.

The requests made by the lazy load are not dependent on each other so they could actually be made **all at once, in parallel.** 

We just need to wait until all of those 4 requests are completed, not to wait until each of those is completed before triggering the next!

changing the above into 
```
const lazyLoad = async user => {
    const [pictures, tags, contacts, moreData] = await Promise.all([
        user.getPictures(), 
        user.getTags(), 
        user.getContacts(), 
        user.getMoreData()
    ])
// some data manipulation here to build a complexObject with all the data - not relevant
    return complexUserWithAllData
}
```

Would immediately **boost performance** and cut down the request time up to 1/4  (basically to the longest one of those four - instead of the sum of all them)

Apply that gain **for every single row** that we previously loaded ( yes that lazyLoad was done inside a loop for every row of the database that was returned by the filter! ) and those nasty timeouts are probably gone forever.

But that analysis point me to another consideration. 

> Why the heck are we lazy loading in the first place?

### Don't be so Lazy!

Sequelize is very good at handling and fetching all the relationship your data model might have and allows you to granularly specify what you are retrieving within your queries.

from the [Docs](https://sequelize.org/master/manual/assocs.html#fetching-associations---eager-loading-vs-lazy-loading):

> Lazy Loading refers to the technique of fetching the associated data **only when you really want it**; Eager Loading, on the other hand, refers to the technique of fetching **everything at once**, since the beginning, **with a larger query**.

Of course, if my endpoint needs to provide only the bare minimum information of each User, like Id and Name, I don't need to eagerly load its Pictures, its Contacts, and so on.
If my API has to return its Contacts instead,  I can query the Users and eagerly load the Contacts but not all the rest.


As soon as we were going to refactor the lazyLoad method to use Promise.all, it was clear that **it was quite meaningless to lazy load data which we need immediately...**

That's why we dropped entirely the lazy load method, and we wrote a specific query with - only - the eager load we need:

```

const loadUsers = async (filter) => {
const options = {
        where: filter,
        include: [
            {
                association: 'pictures',
                attributes: ['id', 'thumb', 'url'],
                through: {
                    attributes: [] //  avoid the junction table to be sent
                }
            },
            {
                association: 'contacts',
                through: {
                    attributes: [] //  avoid the junction table to be sent
                }
            },
            {
                association: 'tags',
                attributes: ['name', 'id']
                //  since tag association is of type BelongsTo  there is no juncion table do not specify Through option  (there is no junction table)
            },
            {
                association: 'moreData',
                through: {
                    attributes: [] //  avoid the junction table to be sent
                }
            }
        ]
    }
    const users = await Users.findAll(options)
    return users // after whatever manipulation we need 
}

```

Basically together with your filter and other sort/limit options you can specify the nested data that you want to load, and what exactly you want to load. 
Instead of 1 simple Query to load the Users and 4 simple extra queries with **JOIN** sto load the data from the nested tables,  we will have one bigger, slightly more complex query with all the **LEFT OUTER JOINn** and the **ON** required.


### Some Sequelize Extra tips

When you debug and write tests to check your DB queries,  always use debugging options like this to have everything printed in the console from Seqiuelize:

```
 logging: (...msg) => console.log(msg),
 logQueryParameters: true
 benchmark: false,
```

It will print something like this for every request sent to the DB:

```
[
  'Executed (default): SELECT `Contact`.`id`, `Contact`.`name`, `ContactsByUser`.`contactId` AS `ContactsByUser.contactId`, `ContactsByUser`.`userId` AS `ContactsByUser.userId` 
  FROM `Contacts` AS `Contact` INNER JOIN `ContactsByUser` AS `ContactsByUser` ON `Contacts`.`id` = `ContactsByUser`.`userId` AND `ContactsByUser`.`userId` = 6605;',
  77,    ///  this is the duration of the Query in millisecs !!!
  {
    plain: false,
    raw: false,
    originalAttributes: [ 'id', 'name' ],
    hasJoin: true,
    model: Contact,
    includeNames: [ 'ContactsByUser' ],
    includeMap: { ContactsByUser: [Object] },
    attributes: [ 'id', 'name' ],
    tableNames: [ 'ContactsByUser', 'Contact' ],
    keysEscaped: true
    // ... much more info
  }
]
```

It is a very fundamental way to **understand how Sequelize work**, how to write better SQL queries and debug your model and your query.


Often if a relationship is of type ManyToMany (m:n) your database will have a so-called **Junction Table** that connects two other tables  like Users and Contacts ( where primary keys of those are listed and _connected_ in the UserContacts table).

In such case you might not need Sequelize to retrieve the - redundant- data of the junction table, and you can tell it not to by setting the `through` option.  
In other cases you just want some columns of the nested tables, you can specify the attributes for every included association.

Those query options can get quite tricky so I really suggest you read more about [Sequelize associations](https://sequelize.org/master/manual/eager-loading.html) and [Query Parameters](https://sequelize.org/master/class/lib/model.js~Model.html#static-method-findAll)


In our code this relatively simple refactor, made the code much cleaner and more flexible, while boosting the performance and avoiding timeouts.

As a general good practice when coding and reviewing, I suggest :
- not just focus on the problem at hand, but always **try to understand the big picture**
- **always ask why** something is done is a certain why (it might be a good reason or a silly mistake, or a valid but outdated reason.
- **read the docs**!

Hope it helps


<span>Photo by <a href="https://unsplash.com/@marcojodoin?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Marc-Olivier Jodoin</a> on <a href="https://unsplash.com/s/photos/speed?utm_source=unsplash&amp;utm_medium=referral&amp;utm_content=creditCopyText">Unsplash</a></span>