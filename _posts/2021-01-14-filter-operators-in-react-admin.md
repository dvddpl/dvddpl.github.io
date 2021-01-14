---
title: Filter Operators in React Admin and Sequelize
published: true
description: how to pass the operator you need in your query from a react admin filter component
tags: #react, #mysql, #fullstack, #javascript
---


### TLDR

If you want to pass operators from your filters, just append the operator type to the source property of your React Component, then have the backend parse it and create the where condition out of it.
You can also make it dynamic by writing a custom filter which grabs the value from the filter, and the operator selected by another small component.

  ```
            <NumberInput
                resource="users"
                label="Age"
                source="age|op=lte"
            />

            <SelectArrayInput
                resource="users"
                source="city|op=In"
                choices={cities}
            />
```
----


### The stack
In our of our latest projects we are using [React Admin](https://marmelab.com/react-admin/), a web framework that allows to - reasonably quickly - build very powerful and advanced applications.
We have then an AWS API Gateway and a bunch of Lambdas to handle all the CRUD which happens within our React application.
Everything is stored in [Aurora Serverless](https://aws.amazon.com/rds/aurora/serverless/) and the backend writes to the DB using [Sequelize](https://sequelize.org/), an **Object Relational Mapper (ORM)** that makes it easy to map object syntax to database schemas. 

React Admin comes with a customizable grid and set of filter components that take care of gathering the user input and invoke the API with the user selections, bound to the data properties returned by the endpoints.

React component for the [Filter Inputs](https://marmelab.com/react-admin/List.html#filters-filter-inputs) looks like this
```
 <NumberInput
                resource="users"
                label="User ID"
                source="id"
            />

            <SelectArrayInput
                resource="users"
                source="city"
                choices={cities}
            />
```

Where _resource_ is your endpoint and _source_ is the property you want to filter by. 
An onChange listener on that component generates a payload that is passed to the endpoint: the URL un-encoded payload looks like this:

```
{
filter:  {
   "cities": ["NY", "LA", "WA"],
   "name": "ma",
   "age": 18
  }
}
```

Being able to bind name property to a **Like Search** or an array value to a **IN condition**, is quite straightforward, but we soon hit some issues with code flexibility and readability.

### The context 

At some point, we needed to add some more advanced filtering of the data we were loading from the database. 
- Search those elements that are **NOT** X.
- Search those elements that are **greater than** Y 
etc

Writing such Where conditions ( or ON conditions with Joint through different tables is not a problem ) in MySQL is quite simple and so it is also with Sequelize.

```
SELECT city, name, age FROM Users WHERE id IN (NY, LA, WA)

SELECT city, name, age FROM Users WHERE name like %ma%.  # will return anything like Matt, Mark, Annamarie, Samantha, Thelma, Naima. 

SELECT city, name, age FROM Users WHERE age < 18 
```

In Sequelize would look like this:

```

    Users.findAll({
        where: {
            id: {
                [Op.in]: [NY, LA, WA],
            }
        })

    Users.findAll({
        where: {
            name: {
                [Op.substring]: "ma"
            }
        })

    Users.findAll({
        where: {
            age: {
                [Op.lte]: 18
            }
        })
```
Of course, those can be combined with OR or And Conditions altogether.


### The problem

How can we handle the Operator within the React Component?

What I had in mind was something like this:

![NumberFilter with operator](https://dev-to-uploads.s3.amazonaws.com/i/7m7eszih5q87icmhrtqf.png)

which I found in [AG-Grid Simple filters](https://www.ag-grid.com/documentation/javascript/filter-provided-simple/). but React Admin did not seem to provide such a component, nor allow the configuration of the payload in a way that contains the operator information, along these lines:

```
"groupOp": "AND",
  "rules": [
    {
      "field": "age",
      "op": "lowerThan",
      "data": 18
    },
    {
      "field": "city",
      "op": "in",
      "data": ["NY", "LA"]
    }
  ],
```

Sequelize comes with a full list of [Operators](https://sequelize.org/master/variable/index.html#static-variable-Op) but the only possibility in React Admin for 
[a generic text search] (https://marmelab.com/react-admin/List.html#filtering-the-list) was to use this approach

``` 
filter={"q":"lorem "}  
```
Where _q_ has then to be mapped in your backend to a specific property and a specific like (startsWith, endsWith, contains), etc.

Handy, but not so handy...

In fact, **there is no standard on how an API does queries with specific operators** and ReactAdmin has no knowledge about it, therefore its filters don't handle such feature. 

The builders of React Admin also state that on [StackOverflow](https://stackoverflow.com/a/60213387) but also provide a hint on how to handle that: 

> Composing the source so that it contains the operator

```filter: { status_id_gt: 2 }```

### The solution 

Not ideal but quite enough to get me started. (I didn't want to rely on the underscore to separate the source, the property and the operator since in too many circumstances we had columns or property already containing underscore...)

I went for a specific separator ( _|op=_ ) which allows the backend to  safely split the property from the operator, then I can append any constant from the available Sequelize Operators:

```
const filters = {
        'name|op=substring': 'ma',
        'age|op=lt': [18],
        'cities': {
            'id|op=in': ['NY', 'LA']
        },
        'status|op=notIn': ["pending", "deleted"]
    }

```
This will be parse by a simple method:

```
const {Op} = require('sequelize')

const parseOperator = (key, value, separator = '|op=') => {
    if (key.includes(separator)) {
        const [prop, op] = key.split(separator)
        if (!Object.prototype.hasOwnProperty.call(Op, op)) {
            throw new Error(`Invalid Filter Condition/Operator (${op})`)
        }
        return {
            property: prop,
            operator: Op[op],
            value
        }
    }
// this is handling the default - which is an Equal if value is it's a string or number, or an IN if we received an Array.
    return {
        property: key,
        operator: Array.isArray(value) ? Op.in : Op.eq,
        value
    }
}
```
then for every entry in the Filter object I received from the Application

```
Object.entries(filter).reduce((acc, current) => {
            const [key, value] = current
            const {property, operator, value: by} = parseOperator(key, value)
            acc.where[property] = {
                [operator]: by
            }
            return acc
        }, {
            where: {}
        }
    )
```

Pass that object to your usual `Model.findAll()` method and you will have the right Sequelize statement with the right operators.

### Where to go from here

I still have in mind the full implementation with a component like the one above - where the user can select the operator they want to use - but for now, that was not the requirement we had, so I stuck to a basic implementation with "hardcoded" operators but which allow a greater deal of flexibility.

Whenever we will need this functionality, we will just need to wrap our input filter component into a custom one so that we can allow the user to select the type of operator, and then compose the string which will be parsed by the backend.
```
            <NumberInput
                resource="users"
                label="Age"
                source="age|op={$SELECTED_OPERATOR}"
            />

            <SelectArrayInput
                resource="users"
                source="city|op={$SELECTED_OPERATOR}"
                choices={cities}
            />
```
Hope it helps