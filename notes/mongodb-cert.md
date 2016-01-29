# Intro to Mongodb

## What is MongoDB

At inception MongoDB was a database that tried to maximize the tradeoff between functionality of relational databases and the pure scalability and performance of key-value stores.

Achieving the desired _scalability_ and _performance_ they got rid of the two things which hurt this the most in relational databases: _joins_ and _transactions_.

To mitigate the loss of _joins_ and _transactions_ they store data in _documents_ to still deliver a high functionality database. Hence, MongoDB is a _Document-store_.

In use MongoDB is a really developer-centric database. The documents stored in MongoDB resembles that of JSON mapping elegantly to the objects of most programming languages around. In addition MongoDB does not require a schema. Also the query language is natively implemented in any programming language it is used.

## Why you should consider MongoDB

We believe this is an important database to you guys because of the simplicity. This is one of many new databases around that really enables developers to be more efficient in their work. Meaning you get to create more amazing stuff in less time.


## What is MongoDB

MongoDB is a document-oriented non-relational database.

Non-relational means that it does not store data in tables as in a relational database.

Document-oriented means that it stores data in JSON documents.

```js
{
    "name": "",
    "gender": "male",
    "address": {
        "street": "",
        "country": ""
    },
    likes: [
        "sports",
        "coding",
        "music",
        "food"
    ]
}
```

### Schemaless

### Scalability and performance vs depth of functionality
Scalable and performance: Memcached and other key-value stores

Depth of functionality: Relational database

MongoDB tries keep as much of the depth of functionality as the RDBMS without losing scalability.
The scalability is kept by not supporting Joins and transactions (transactions between mulitple documents).

## Operations

MongoDBs CRUD operations exist as methods/functions in programming language APIs, not as seperate language.

MongoDB itself offers a relatively simple wire protocol that includes an operation code and arguments to that operation transmitted as a sequence of bytes over the network.

### Update
By default replaces entire object except the _id

```bash
> db.apps.find({name: "yo"});
[{
    _id: ObjectId(),
    name: "yo",
    creator: "Yo apps"
    rating: 84,
    tags: ["social"]
}]
> db.apps.update({name : "yo"}, {creator: "Yo Apps Co"});
> db.apps.find({name: "yo"});
[]
> db.apps.find({creator: "Yo Apps Co"});
[{
    _id: ObjectId(),
    creator: "Yo Apps Co"
}]
```

Use $set, $addToSet, $unset, $push, $pop instead

```bash
> db.apps.find({name: "yo"});
[{
    _id: ObjectId(),
    name: "yo",
    creator: "Yo apps"
    rating: 84,
    tags: ["social"]
}]
> db.apps.update({name: "yo"}, {$set: {creator: "Yo Apps Co"}});
> db.apps.find({name: "yo"})
[{
    _id: ObjectId(),
    name: "yo",
    creator: "Yo Apps Co"
    rating: 84,
    tags: ["social"]
}]
```

### Multi update
Default unlike sql updates only one document.

To update multiple
```bash
> db.apps.update({}, { $set: {inAppStore: false}}, { multi: true })
```

### Sort Skip Limit
That is done in that sequence.

### FindAndModify in the driver
When you need the value of something like a counter you just updated. FindAndModify is atomic.

### On concurrency
You will never see a document half updated, hence they are atomic. However performing a multi-update/remove/write are not.

If you remove say 10 documents a concurrent operation might read a state where only half of these are removed.

## Schema Design
In relational you store data in most cases agnostic to the use case. 3rd normal form is the norm.

In MongoDB however you should rather design after your use case and data access pattern. Application driven schema.

### Goals of normalization
- Free the database from update anomalies
- Minimize redesign costs
- Avoid bias towards one access pattern

### Living without constraints


### Living without transactions
1. restructure
2. implement in software
3. tolerate

### One to one relations
- reference in either document
- embed

Consider:
- Size of each
- Do you need both at the same time
- Atomicity of data

### One to many relations
city : person

- embed person in city
    - what if too many people
    - Perfect for _one to few_
- embed city in person
    - city defined many times
- true linking
    ```js
    people:
    {
        name: "Ada",
        city: "NYC"
    }

    city:
    {
        _id: "NYC"
        area: 9001
    }
    ```

### Many to many relations
- Few to Few
    - ok in mongodb
    - reference in one or both documents
    - embed. Iffy. Could be of performance reasons.
- Many to Few

### Representing trees
Products and categories

Smart to add category ancestors.
```bash
given:
{
    _id: 42,
    category: "sports",
    parent: 10,
    ancestors: [10, 14, 15]
}

find all children of sports:
> db.categories.find({ancestors: 42});
```

### When to embed (when to denormalize)
Embed things you typically will need at the same time.

Benefits to embedding
- Improved read performance
- One round trip to the DB


## Performance
Collection scans is the death of your performance, certainly for large collections. Use indices in such situations.

### Indexes
#### Multikey indexes
The possibility of indexing fields that are arrays.

#### Unique indexes
```
> db.collection.ensureIndex({field: 1}, { unique: true})
```

#### Sparse indexes
If using unique indices and more than one document does not have a value for the indexed value.

When sorting on a sparse index, entries without value will then not be in the result set.

#### Index creation
Foreground
- fast
- block write

Background
- slow
- does not block writers

#### Index selectivity
Be as selective as you can.

For instance with a logging example:
```
{
    "opcode": "run / get / save",
    "timestamp": Date,
}
```
This index should be {timestamp, opcode} since there are few opcodes. Time ranges will cut much more, hence more effective.

#### Geospatial indexes
Fields must be type `location: [x,y]`.

```
> db.doc.ensureIndex({location: '2d'})
```

Operators like $near, typically used with limit. $near returns results by ascending distance from origin.


## Profiler

Queries over 100ms default output by mongod.

Profiler has three levels
0 - Off
1 - Log slow queries
2 - All

When you are developing, setting profiler to __level 3 all__ can be convinient for debugging purposes.

Profiling data is stored in the collection db.system.profile.
