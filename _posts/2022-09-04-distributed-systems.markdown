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


## Key value Store

### Ensure scalability and replication

#### Add scalability


So we store key value data in storage nodes. What happens when we need to add or remove storage nodes?
It means we need to partition data over the nodes to distribute the load across all nodes.

The flow is as follows

1. Request comes in
2. We hash the request
3. We find the remainder of the hash by the number of available nodes
4. The remainder is the node value
5. We send the request to that node

Solutions

### Consistent Hashing

Imagine a ring of hashes from 0 to n-1 hashes. We take a node ID, hash it, and map it to a ring.

Each request is completed by the next node that it finds by moving in the clockwise direction in the ring.


When a new node is added, the immediate next node shares it's data.

Primary benefit is as node join and leaves, it ensures minimal number of keys need to move.


In practice, the request load is not balanced. The node that receives disproportionately large share of data storage and retrieval requests.
These are referred to as hotspots.

Solution to the hotspot

Use virtual nodes

We'll use virtual ndoes to ensure a distributed load across all nodes. We'll apply multiple hash functions onto the same key.

Basically 3 hashes will equal to 3 positions in the ring for each node.
So the load of requests is more uniform. If the node has more hardware capacity, then we can add more virtual nodes.

Advantages

1. If a node fails or in maintenance, the workload is uniformly distributed over other nodes.
2. For each new accessibly node, the other nodes receive nearly equal nodes when it comes back online or is added to the system.
3. It's up to each node to decide how many virtual nodes it's responsible for.

### Data Replication

#### Primary secondary approach

1 of the storage areas is primary and the others are secondary

* The primary serves write requests
* The secondary serves read requests
* After writing, there's a lag for replication
* If the primary goes down, we can't write into storage

#### Peer to peer approach

All storage areas are primary. In practice, 3 or 5 is common. We'll replicate the data on multiple hosts
to achieve durability and high availability. Each data item will be replicated at n hosts.
N is a parameter configured per instance of the key-value store.


We'll call a node the coordinator. It's responsible for replicating the keys to n -1  successors on the ring.
These lists of successor virtual nodes are called preference lists.
To avoid putting replicas on the same physical nodes, the preference list can skip
those virtual nodes whose physical node is already on the list.

### Versioning Data and Achieving Configurability

We need causality to maintain by using vector clocks. Vector clock is
a list of (node, counter) pairs. There's a single vector clock for every version
of an object. If two objects have different vector clocks, we can tell if they are
causally related or not.

Example

1. Write handled by A E1([A,1])
2. Write handled by A E2([A,2])
3. Network partition
4. Write handled by B E3([A,2], [B,1])
5. Write handled by C E4([A,2], [C,1])
6. Network repaired
7. Conflict so context is ([A,3], [B,1], [C,1])
8. Write handled by A E5([A,4])

Vector clock limitations

Size of the vector may grow a lot. It's unlikely to happen in practice
because writes are handled by one of the top n nodes in a preference list.

But we can limit the size with a clock truncation strategy.

Store a timestamp with each set and vector pairs are purged when the number
exceeds a predetermined threshold.

### Get and Put

2 ways for a client to select a node
1. We route the request to a generic load balancer
2. We use a partition aware client library that routes requests directly to the appropriate coordinator nodes.

Take r and w
r is the minimum number of nodes that need to be part of a successful read operation.
w is the minimum number of nodes that need to be part of a successful write operation.

We need r and w to be at least one node is common between them.

r + w > n

larger value of r we focus on availability and compromise latency.

The get operation is decided by the slowest of the r replicas

The coordinator produces the vector clock for the new version ands writes
the new version locally upon receiving a put request. The coordinator sends
n highest ranking nodes with the updated version. We consider a successful write request
if at least w-1 nodes respond.

Requests for a get operation are made to the n highest ranking reachable nodes. They wait for r answers.
Coordinators return all dataset versions that they think are unrelated.
The conflicting versions are merged, and the resulting key's value is rewritten to override
the previous version.



### Enable Fault Tolerance and Failure Detection


#### Temporary Failures

Hinted Handoff - The first n healthy nodes from the preference list
handle all read and write operations. If the intended node is unavailable, then the next node in the list is sent the request.
After processing the request, the second node includes a hint as to which node was the intended receiver.
Once the original node is back, the second node sends back the request information, so it can update its data.


### permanent Failure

Merkle tree TBD


## CDN

SKIP

## Sequencer


SKIP

## Distributed Monitoring

1. Server side errors - HTTP response codes
2. Client-side errors - 400 HTTP response codes



