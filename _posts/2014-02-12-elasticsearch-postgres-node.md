---
layout: post
title: Set up autocomplete with Postgres and Elasticsearch in node.js
category: posts
---

Recipe: Autocomplete with postgres, elasticsearch, node.js


Let's build a fancy autocompleting search widget!

Prequisites:
* Familiarity with
  * expressjs
  * PostgreSQL (and hopefully [https://github.com/brianc/node-postgres](node-postgres))

Elasticsearch Basics
--------------------

So let's first set up elasticsearch. Grab the latest from 
http://www.elasticsearch.org/overview/elkdownloads/ and extract your archived package.

Inside of the new elasticsearch folder, run

```
bin/elasticsearch
```

To start the elasticsearch server. The beauty of elasticsearch is 
its api is powered through http so it's easy to get through the 
official documentation examples. There's tons of great cURL examples
you can copy and paste. 

The default port is 9200, so you'll be able to run all of your 
commands against http://localhost:9200/

> Learn to walk before running

..and crawl before walking.

So to search with elasticsearch we gotta start _indexing_ data. Let's 
first take a look at the anatomy of a typical index.

```
http://localhost:9200/cater/restaurants/5
```

Here, `cater` is the name of an **index**. Indices are kind of analogous
to relational databases. Next, `restaurants` is a **type**. Types are like
tables. The identifier at the end is going to specify a **document** which would be a row.

So we could imagine other routes set up in a RESTful manner like the 
following:

```
/cater/restaurants
/cater/restaurants/5
/cater/users/
/cater/users/10
```

Now with that in mind, I suggest reading through the documentation for 
document, search and indice apis. There are A LOT of options and 
configurations per api. The examples in the rest of this post will be 
simple.

Here's an example ripped straight from the docs for indexing a new document. In this case, its a new tweet.

```
$ curl -XPUT 'http://localhost:9200/twitter/tweet/1' -d '{
    "user" : "kimchy",
    "post_date" : "2009-11-15T14:12:12",
    "message" : "trying out Elasticsearch"
}'
```

Notice the **index** is twitter, and the **type** is tweet. This is a 
HTTP PUT request to create a new **document** with an id 1 and will return 

```
{
    "_index" : "twitter",
    "_type" : "tweet",
    "_id" : "1",
    "_version" : 1,
    "created" : true
}
```

Set up 
------

All right, so how can we integrate this into our own application?

A rather simple implentation would be to

  * Create a script to query all existing tweets and index them
  * Index new tweets as they are created

An alternative is to use "rivers" in elasticsearch. Rivers are 
plugins to connect to your data store and automatically index streaming
data. So it will 1) index existing data 2) index new updates 3) support 
failover.

For postgres, you will need to install

 * JDBC River plugin - https://github.com/jprante/elasticsearch-river-jdbc
 * Postgres JDBC Driver - http://jdbc.postgresql.org/download.html

Make sure to get the plugin compatible with your version of elasticsearch. Same with the driver and postgres.

Here's a node script to set up a river for index **cater** and type
**restaurant**. The **document** contains several fields, but let's 
just index id and name. Note that we're using request for firing 
http requests. Make sure `./bin/elasticsearch` is running and run this.

```
var request = require('request');
var config = require('../../config');

var options = {
  uri: 'http://localhost:9200/_river/cater/_meta'
, method: 'PUT'
, timeout: 7000
, json: {
    type: 'jdbc'
  , jdbc: {
      url: 'jdbc:postgresql://localhost:9200/cater'
    , sql: 'select id, name from restaurants'
    , index: 'cater'
    , type: 'restaurant'
    , schedule: '0 0/5 * * * ?' // every 5 mins
    }
  }
};

request(options, function(err, res, body) {
  if (err) {
    return console.log(err);
  }
  console.log('Created /_river/cater');
  console.log('Response:', body);
});
```

It will use the sql query for indexing data into /cater. The magic sauce 
is in the `schedule` cron string which triggers updates every 5 minutes!

-------------------

TODO: writing up the front end side of this