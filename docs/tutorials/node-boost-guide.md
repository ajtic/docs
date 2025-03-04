---
title: 'Node.js query caching with PlanetScale Boost'
subtitle: 'Learn how to use PlanetScale Boost in a Node.js application.'
date: '2022-11-15'
---

{% callout %}
PlanetScale Boost is in limited beta. [Reserve your spot on the waitlist](/features/boost) today.
{% /callout %}

This guide will walk you through two methods to connect to your database using the [PlanetScale Boost query caching feature](/docs/concepts/query-caching-with-planetscale-boost) when building an application with Node.js. It will start with a simple code snippet that will be used to always execute a query through the cache server, then expand into using separate connections.

## Enable PlanetScale Boost on a MySQL connection

Below is sample code that will establish a connection to a PlanetScale database and perform a query.

```js
require('dotenv').config()
const mysql = require('mysql2/promise')

;(async () => {
  const connection = await mysql.createConnection(process.env.DATABASE_URL)
  let [rows] = await connection.query('select * from users')
})()
```

In order to enable a connection to use PlanetScale Boost, the `@@boost_cached_queries` session variable needs to be set with the following code:

```js
await connection.execute('SET @@boost_cached_queries = true')
```

Any code written after that session variable has been set will proxy requests through the Boost server. A fully updated code snippet would look like this:

```js
require('dotenv').config()
const mysql = require('mysql2/promise')

;(async () => {
  const connection = await mysql.createConnection(process.env.DATABASE_URL)
  connection.execute('SET @@boost_cached_queries = true')
  let [rows] = await connection.query('SELECT * FROM users')
})()
```

With this configuration, all queries will be run through the boosted connection, regardless of if they're actually using PlanetScale Boost. This may make it difficult to tell which queries are really being boosted. For this reason, we recommend using two separate connections: one for regular queries and one for boosted queries.

## Create a separate connection

Our recommended strategy for working with PlanetScale Boost is to use two separate connections so you can be explicit when executing your queries. To do this, you'll need two separate `connection` objects using the `mysql2` package and set the session variable on only one of them:

```js
require('dotenv').config()
const mysql = require('mysql2/promise')

;(async () => {
  // Create the standard connection
  const connection = await mysql.createConnection(process.env.DATABASE_URL)
  let [rows] = await connection.query('SELECT * FROM users')

  // Create a Boost-enabled connection
  const boostedConnection = await mysql.createConnection(process.env.DATABASE_URL)
  await boostedConnection.execute('SET @@boost_cached_queries = true')
  let [rowsFromCache] = await connection.query('SELECT * FROM users')
})()
```
