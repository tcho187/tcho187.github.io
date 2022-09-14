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

