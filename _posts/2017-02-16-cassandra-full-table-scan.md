---
layout: post
title: Perform a full table scan with Apache Cassandra
tags: 
- java
- cassandra
- datastax
- bad ideas
---

In this post I'll talk about a technique for performing the equivalent of the following query with [Apache Cassandra](http://cassandra.apache.org/):

```sql
select * from mytable;
```

Now, in general, **this is a bad idea**. Apache Cassandra is an amazing data store, allowing you to persist billions or trillions of rows in a single table, all while still guaranteeing constant* time performance. But if you try to execute this query blindly it generally won't work; the command may never return, and likely, crush your cluster in the interim.

If you need to scan through a large dataset like this, you should consider using something like [Apache Spark](http://spark.apache.org/). Spark has [tight integration with Cassandra](https://github.com/datastax/spark-cassandra-connector) and can be deployed alongside your Cassandra nodes for efficiency and performance.

In this post, I'm not going to try and duplicate what you can do with Spark. This post will however, provide you a mixin interface you can use with just the DataStax Java Driver to scan the full table in a fairly sensible way that will complete.

## Technique

This post describes the use of the [token function](https://docs.datastax.com/en/cql/3.1/cql/cql_using/paging_c.html):

[Scanning the entire cassandra column family with CQL](http://www.myhowto.org/bigdata/2013/11/04/scanning-the-entire-cassandra-column-family-with-cql/)

The token function allows us to interact with the partitioning done by Cassandra. To satisfy our goal of observing every row, we can perform a series of limited sub-queries by token ranges. 

These sub-queries look like:

```sql
select id/*primary key*/, ..., token(id) 
  from mytable 
  where token(id) >= -9223372036854775808
  limit 10000;
```

We take the `token(id)` value from the last row in the result set and run the query again, using that value `+ 1`, until we get no more results. 
The results will always be returned in ascending order by token - that's just how Cassandra's partitioning works. 

Each of these sub-queries then can (most often) get be satisfied from a single partition/node. 

## Code

The code for the mixin can be found at:

[https://github.com/nblair/code-examples/blob/master/datastax-java-driver-examples/src/main/java/examples/datastax/FullTableScan.java](https://github.com/nblair/code-examples/blob/master/datastax-java-driver-examples/src/main/java/examples/datastax/FullTableScan.java)

There are 3 methods you will have to implement with this interface:

```java
  /**
   *
   * @return the name of the table (column family) to query, must not be null
   */
  String table();

  /**
   *
   * @return a list containing the names of the columns that compose the partition key; must not be empty
   */
  List<String> partitionKeys();

  /**
   *
   * @param row a single row in the result set
   * @return a corresponding object that can be hydrated from the row, must not be null
   */
  T mapRow(Row row);
```

Additional behavior:

* If your mapRow method needs to inspect additional columns other than what are provided by partitionKeys, override `List<String> columns()`.
* The tableScan method will default to using the current keyspace for the provided `Session`. You can override `String keyspace()` to change this behavior.
* The tableScan will limit the token sub-queries to 10,000 rows by default; override `int limit()` to change this. Use with caution.
* The default `CONSISTENCY` for the query is `ONE`, which is the lowest consistency but highest availability. Trade off for performance; trying to do this with high consistency with large data sets is probably a worse idea, use with caution.

The [containing module for this class](https://github.com/nblair/code-examples/tree/master/datastax-java-driver-examples) contains [a reference use case](https://github.com/nblair/code-examples/blob/master/datastax-java-driver-examples/src/main/java/examples/datastax/DataStaxVideoDao.java) and [an integration test](https://github.com/nblair/code-examples/blob/master/datastax-java-driver-examples/src/integrationTest/java/examples/datastax/DataStaxVideoDaoIntegrationTest.java) to experiment. 

## References

Other references:

* [Issue in full table scan in cassandra (Stack Overflow)](http://stackoverflow.com/questions/29836965/issue-in-full-table-scan-in-cassandra)

## Addendum

\* Inserts yes; Selects yes if you include the partition key in your where clause.