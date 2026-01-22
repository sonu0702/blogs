---
title: "CAP Theorem in System Design"
date: "2024-08-29"
description: "It is a fundamental framework for understanding trade-offs when designing distributed systems."
tags: ["systemdesign", "webdev", "learning"]
---

It is a fundamental framework for understanding trade-offs when designing distributed systems.

Consistency (C): A distributed system is said to be highly consistent when every read receives the most recent write or an error.

Availability (A): A distributed system is said to be highly Available when every request (Read or Write) receives a non-error response, without guarantee that it contains the most recent write

Partition Tolerance (PT): A highly partition tolerant system continues to operate despite an arbitrary number of messages drop(delay) between nodes.

**The CAP trade-off: Choosing 2 of 3**: In the presence of network partition a distributed system must choose between Consistency or and Availability.

CP (Consistency and Partition Tolerance): During a partition, the system may reject some requests to maintain consistency.

AP (Availability and Partition Tolerance): During a partition, different nodes may return different values for the same data.

CA (Consistency and Availability): In the absence of partitions, a system can be both consistent and available. However the network partition are inevitable.

### Practical strategies

1. Eventual consistency: This gives a good balance where an immediate consistency is not necessary like CDNs and DNS, eventually these will be updated with latest data.
2. Strong consistency: Financial applications can work with eventual consistency they need a strong consistency.
3. Tuneable consistency: Allows systems to adjust their consistency levels based on specific needs. Systems like Cassandra allow configuring the level of consistency on a per-query basis.
4. Quorum-Based Approaches: Use voting system among a group of nodes to ensure a certain level of consistency.

