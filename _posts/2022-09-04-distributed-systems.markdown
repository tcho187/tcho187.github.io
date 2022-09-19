---
layout: post
title: Distributed Systems
tags: [bert, jekyll, blog, stocks]
image: '/images/posts/pexels-pineapple-supply-co-139259.jpg'
---


There are two categories of the central parts that help
distributed systems function:
1. The various parts that compose a distributed system.
2. The network that separates the various parts of a distributed system.


3 benefits
1. Performance
2. Scalability
3. Availability

Redundancy is used to achieve higher availability.


### Fallacies of distributed systems
1. The network is reliable
2. Latency is zero
3. Bandwidth is infinite
4. The network is secure
5. Transport cost is zero
6. The global clock fallacy


### Why distributed systems are hard to design

1. Network asynchrony
2. Partial Failures
3. Concurrency


### Multi Master Replication

Favors higher availability and performance over data consistency.
Write requests are concurrently handled therefore conflicts emerge.

conflict resolution
1. exposing the conflict resolution to the client. Have the client pick the right version.
2. Last write-wins conflict resolution. (wonky behavior)
3. causality tracking

### Quorums

ex. in a system of 3 replicas, write needs 2 nodes and read needs 2 nodes.
read will read the latest value because at least 1 of the nodes in the
read quorum will be included in the latest write quorum.

In V total replicas
THe following properties
1. V<sub>r</sub> + V<sub>w</sub> > V
2. V<sub>w</sub> > V/2


### CAP theorem

#### Consistency
Every successful read request receives the result of the most
recent write request.

#### Availability
Every request receives a non-error response, without any guarantees
on whether it reflects the most recent write request

#### Partial Tolerance
System can continue to operate despite an arbitrary number of
messages being dropped by the network between nodes
due to network partition.

Theorem - a distributed system can be either consistent or
available in the presence of network partition.

ie. the write request won't update node B where user will read.


Trade off between latency and consistency

When no network partition is present,
to guarantee data consistency, system will have to delay write operations
until the data has been propagated across the system taking a latency hit


synchronous replication favors consistency over latency

asynchronous replication reduce latency at the cost of consistency.


### Consistency Model

#### Linearizability

Operations appear instantaneous to the external client.

When we use a synchronous replication, we make the system linearizable.

Benefit: It helps us treat complex distributed systems as much simpler
single-node datastores and reason our applications more efficiently.


#### Sequential consistency

Operations are allowed to take effect before their invocation or after
their completion.

It provides no real time guarantees.

Social network application. We do not care what the ordering of posts
between some of our friends. We still expect posts from a single friend
to be displayed in the right order.


#### Causal consistency





#### skipped a bunch


#### Network abstractions: Remote Procedure Calls

#### Spectrum of Failure Models

1. Fail stop - node stops but other nodes can still communicate to it.
2. Crash - node stops silently and other nodes can't detect the node.
3. Omission Failures - node fails to send or receive messages.
4. Temporal Failures - nodes generates correct results but is too late.
bad algorithm/ bad design strategy / loss of synchronization between processor clocks
5. Byzantine Failures - random behavior


#### Availablity

percentage of time that some service is accessible to clients

A = (total time - amount of time service was down)/total_time * 100


#### Reliability

probability that the service will perform its function for a specific time


two metrics

mean time between failures (mtbf)
mean time to repair (mttr)

mtbf = (total_time - sum_of_downtime)/total number of failures
mttr = (total maintenance time/total number of repairs)

we want higher mtbf and lower mttr

SLO - service level objectives

Situations
* low A high R
* low A low R
* high A high R (desirable)
* high A low R


#### Scalability

ability of a system to handle an increasing amount of workload without
compromising performance.

Two types

* Request workload
* Data/storage workload

there's dimensions of scalability

* Size Scalability - can we add users and resources to it?
* Administrative Scalability - capacity for a growing number of organizations or users
* to share a single distributed system with ease.
* Geographical Scalability - easily the pogram can cater to other regions while maintaining
* acceptable performance constraints. In other words, the system can
* readily service a broad geographical region as well as smaller one


Different approaches

* Vertical Scaling

adding cpu or ram
dollar cost is usually high
limit is our servers


* Horizontal Scaling

increasing number of machines in the network. We use commodity nodes
for this purpose because of their attractive dollar cost benefits.


Maintainability

1. Operability - ease with which we can ensure the system's smooth operational running normal circumstances/
2. Lucidity - simplicity of code.
3. Modifiability - capability of the system to integrate modified, new, and unforeseen features without the hassle.

probability that the service will restore its function within a specified time of fault occurence.


MTTR as a metric


#### Fault Tolerance

system's ability to execute persistently even if one or more of its
components fail.


## Caching

Placing a cache directly on a request layer node enables the local storage of response data.

If we have multiple nodes then it's possible to have cache in each node, but if the load balancer
distrbutes requests across the nodes, then same request will go to different nodes, thus increasing
cache misses.


### Content Delivery Network (CDN)

cache good for sites with static media.

If the system isn't big enough yet, create a separate subdomain static.yourservice.com using
a lightweight HTTP server like Nginx and cut over the DNS from your servers to a CDN later.


### Cache Invalidation

Cache should match the database so whenever data in the database changes, cache should also change.

Write-through cache - data is written into the cache and the database simultaneously.
minimizes the risk of data loss but has higher latency

Write-around cache - data is written directly to the permanent storage bypassing the cache.
disadvantage is read request for recently written data will create a "cache miss" and must be
read from the slower back-end storage and experience higher latency.


Write-back cache -data is written only to the cache. The write to the permannent storage is done
after specified intervals or under certain conditions. results in low latency and high throughput
for write-intensive applications. comes with the risk of data loss in case of crash or other
adverse event because the only copy of the written data is the cache.


### Cache eviction policies
1. First In First Out - the cache evicts the first block accessed first without any regards to how often or how many times it was accessed before.
2. Last In First Out - the cache evicts the block accessed most recently first without any regard to how often or how many times it was accessed before.
3. Least Recently Used - discards the least recently used items first.
4. Most Recently Used - discards the most recently used items first.
5. Least Frequently Used - discards the least frequently used items first.
6. Most Frequently Used - discards the most frequently used items first.
7. Random replacement - Randomly selects a candidate item and discards it to make space when necessary


### Databases

File storage is first thought

Limitations of file storage
1. We can't offer concurrent management to separate users accessing the storage files from different locations
2. We can't grant different access rights to different users.
3. System can't scale.
4. Search content for different users in a short time is difficult.

Advantages of database

1. Managing large data
2. Data consistency (accurate data)
3. Easy updation
4. Security
5. Data Integrity
6. Availability
7. Scalability


### Relational DB
Tables have keys

Instances are rows

Attributes of each instance are columns

ACID

### Data Replication

We need these characteristics from our data store

1. Availability under faults (failure of some disk, nodes, and network and power outages)
2. Scalability (with increasing reads, writes, and other operations)
3. Performance (low latency and high throughput for the clients)

Replication is key to all 3

Questions to ask for relplication
1. How do we keep multiple copies of data consistent
2. How do we deal with failed replica node
3. Should we replicate synchronously or asynchronously
4. How do we deal with replication lag
5. How do we handle concurrent write
6. What consistency model needs to be exposed to the end programmer

#### Data replication models

Single leader or primary-secondary replication

Multi-leader replication

Peer to peer or leadership replication


Primary-secondary

is good for read heavy. 1 node is responsible for processing any writes to data. It also sends all the writes to the secondary nodes.

Read resilient because secondary nodes can handle read requests in case of primary node failures.

Replication with this approach and asynchronous replication comes with inconsistencies.

Clients reading can see inconsistent data.

There's 3 methods for primary-secondary

1. Statement-based replication
   1. all statements are saved like insert, delete, update and sent to secondary to perform. Bad because of nondeterministic functions like now()
   2. dependent writes may come in the wrong order
2. Write-ahead log (WAL) replication
   1. The primary node saves the query in a log file before executing. The logs are then sent to the secondary node. Used in PostreSQL and Oracle.
3. Logical (row-based) log replication
   1. All secondary nodes replicate the actual data changes.


Multi-leader

They're multiple primary nodes that process the writes and send them to all other primary and secondary nodes to replicate.

Works great for offline

Since all the primary nodes concurrently deal with the write requests, they may modify the same data
which can create the conflict between them.

How to avoid conflicts

If the application can verify that all writes for a given record go via the same leader then can be avoided.

Last-write wins


