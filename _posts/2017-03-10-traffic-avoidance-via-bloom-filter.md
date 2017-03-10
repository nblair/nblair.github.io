---
layout: post
title: Traffic Avoidance Using Bloom Filters
tags: 
- java
- rest
- http
- integration
- bloom filter
---

In this post I'll describe a unique integration project and a means for improving overall throughput with traffic avoidance. 

The project inspiring this post had a lower bound of five hundred inputs per second (1.8 million per hour) and and upper bound of several thousand inputs per second (10 million+ per hour).

## Scenario

Imagine you have a typical service endpoint that provides a REST API. You've assuredly got a resource URL that looks like:

```
GET /resource/{id}
```

In this scenario, assume a very high difference in cardinality between the range of possible id inputs, and the id values you have actually stored in the service. For example, for every 1000 id values, you only have 1 stored.

Now imagine a client that wants to integrate with this service. This client is exposed to a range of inputs on the high end of the cardinality range and wants to issue GET requests to your resource to see what data you have for each.

## Brute force

If we just write a simple HTTP client for this scenario, and blindly send GET requests for each id observed, what can we expect to happen?

Remember for every 1000 requests, you are going to see 999 `404` responses and one single `200` response.

How quickly do you need to make those requests, and what is your average network latency?

If your average network latency is 1 millisecond, you can complete 1000 requests in 1 second. (This isn't realistically possible, not even on localhost can you get this latency for an HTTP request that has to hit persistence).

Say each HTTP round trip takes 20 milliseconds. This would be extremely fast, intra-datacenter traffic. It now takes you 50 seconds to get all 1000 requests completed.

1000 requests though isn't really a problem worth talking about. What if we had a client with 1 million ids to check?

1 million requests with a latency of 20 milliseconds now takes you 50,000 seconds - **or almost 14 hours** (single threaded\*). And out of that 1 million requests, you got a measly 1000 successful requests and 999,000 `404`s.

That's *a lot* of network noise for little data. 

## Problem

The problem here is that latency isn't ever going away. Caching isn't going to do anything to help here; the differential in cardinality (99+%) will be directly reflected in cache miss ratio. In fact it may make the scenario worse.

We need some way for the client to be a little smarter and avoid making so many HTTP requests.

## Batching?

What about adding support in the endpoint for a "multi-get," e.g. allow the client to request a collection of ids, returning a map-like structure of matching results?

On the endpoint, our API would have either a `POST /resource` requiring a JSON body containing an array of ids, or maybe `GET /resource/{id}/.../{id}`. You'll want to constrain how many ids you can accept, with all sorts of constraints to think about (URL length, number of values in an `in` clause for persistence). You still will have really high miss rate, so lots of traffic and database work for low successful result rate.

On the client side, this can be pretty complicated to deal with. Say your client is event driven - now it has to save state, collect up a bunch of ids until we hit some threshold to trigger an HTTP request? What happens to the processing needed after the data is obtained? How do you defer/block further processing until we get a response back? Callbacks? More Events?

Batching our requests would cut down on the number of HTTP requests, but the side effects of implementing it - *particularly on the client side* - appear to be very complex.

## Bloom filter

A bloom filter is a data structure with two core operations:

```java
/**
 * Put an object in the structure
 */
void put(T object); 
/**
 * Is the object maybe in the structure? return true
 * Is the object definitely NOT in the structure? return false
 */ 
boolean mightContain(T object);
```

`mightContain` can help us here, and tell us that the id is:

* definitely not present, and we shouldn't bother making an HTTP request, or,
* might be present and we should try the HTTP request.

If the service had a way to distribute a bloom filter populated with the id values that we have persisted, a client could check the filter first before making a get:

```java
if (bloomFilter.mightContain(id)) {
  return httpClient.get(id);
} else {
  return null;
}
```

This seems more promising: bloom filters are relatively easy to work with, it has the potential to achievethe desired effect of cutting down the number of HTTP requests the client makes.

## Building a bloom filter

[Google's Guava](https://github.com/google/guava) provides an easy to use [bloom filter implementation](http://google.github.io/guava/releases/snapshot/api/docs/com/google/common/hash/BloomFilter.html), that even includes [writeTo](http://google.github.io/guava/releases/snapshot/api/docs/com/google/common/hash/BloomFilter.html#writeTo-java.io.OutputStream-) and [readFrom](http://google.github.io/guava/releases/snapshot/api/docs/com/google/common/hash/BloomFilter.html#readFrom-java.io.InputStream-com.google.common.hash.Funnel-) methods to allow us to serialize our filter on the wire.

Guava's bloom filter implementation also has 2 parameters: `expectedInsertions`(int) representing roughly how big of a data set you plan to contain in the filter, and `falsePositivePercentage`(double) representing what percentage of false positives for `mightContain(T)` you are willing to tolerate. The lower `falsePositivePercentage` is:

* the fewer HTTP requests you'll have to make, but
* the larger your bloom filter will be.

Within your service endpoint, you are going to need a way to pull all ids from your persistence periodically. [In a previous post]({% post_url 2017-02-16-cassandra-full-table-scan %}), I presented a technique for doing this with [Apache Cassandra](http://cassandra.apache.org/).

## Experiment

I've implemented a [Dropwizard](http://dropwizard.io) endpoint in my [code-examples project](https://github.com/nblair/code-examples) along with a client that demonstrates the concepts:

* [implement the FullTableScan mixin to produce all ids from Cassandra](https://github.com/nblair/code-examples/blob/master/datastax-java-driver-examples/src/main/java/examples/datastax/DataStaxVideoDao.java#L112),
* [create and manage a BloomFilter instance of those ids](https://github.com/nblair/code-examples/blob/master/endpoint/src/main/java/examples/resources/VideoBloomFilterManager.java),
* [distribute that BloomFilter via HTTP](https://github.com/nblair/code-examples/blob/master/endpoint/src/main/java/examples/resources/VideoResource.java#L74),
* and [consume the filter in the REST client](https://github.com/nblair/code-examples/blob/master/client/src/main/java/examples/client/VideoApiClient.java#L74).

In Cassandra, I've stored ~620,000 records. With that dataset, a bloom filter containing UUID-like Strings had size of:

* ~450KB with 3% false positive percentage
* ~600KB with 1% false positive percentage

The bloom filter takes about 30 seconds to generate (running on a consumer grade desktop with a spinning disk that's a few years old).

I've created an [integration test to demonstrate the impact of traffic avoidance via the bloom filter can have](https://github.com/nblair/code-examples/blob/master/client/src/integrationTest/java/examples/client/BloomFilterDemonstrationIntegrationTest.java).

Running this test produces the following:

```
[main] INFO BloomFilterDemonstrationIntegrationTest - client with filter skipped 46853 gets, made 3147 unsuccessful gets (filter false positives), 1 successful get, in 3.858 s
[main] INFO BloomFilterDemonstrationIntegrationTest - client without filter executed 50001 total gets, with 1 successful get, in 36.55 s

```

With the bloom filter present, we only sent HTTP traffic for ~3000 of the 50,000 IDs - meaning we avoided 94% of the HTTP traffic! 

If we run the test again with a 1% false positive percentage on the bloom filter, we avoid 97% of the traffic.

## Summary

Translating the experimental results back to our original problem:

**In a scenario with a high miss rate, using a bloom filter allows us to avoid issuing ~95% of our client side reads. With the configuration shown, our client only issues 30,000 to 60,000 HTTP requests per million resource ids we observe on the client side.** 

I estimate we can store about 1,000,000 ids (UUID-like Strings) per megabyte (MB) of bloom filter at 1% false positives; that number goes to 1,300,000 at 3%.

One side effect to understand with this strategy is that it may be possible for the following sequence:

1. Client downloads bloom filter, id X not present.
2. Resource with id X created (some nonzero time later)

`mightContain(X)` is likely to return false. It could return true depending on where X is in the id range and how X is hashed by the filter, but we can't count on that.

Using this strategy comes with the drawback that the freshness of the data observable on the client is directly related to the last time the filter was built. Adjust the frequency of filter construction/dissemination to match your data liveliness requirements. 

## Addendum

\* Sure, we can parallelize this. 14 threads, down to 1 hour; 56 threads, down to 15 minutes. Do you have the capacity on both sides to deal with all that CPU? Database I/O for empty reads? 

