---
title: "Scalability in System Design"
date: "2024-08-28"
description: "Scalability is the property of system to evolve as the work load grows.
"
tags: ["webdev", "systemdesign", "devjournal"]
---
Scalability is the property of system to evolve as the work load grows.

Types of work load

1. Users
2. New features
3. Data volume
4. Complexity in code
5. Geographical Reach

Ways to scale your system

1. Vertical scaling (Scale up) : adding more RAM / fast ram, it is effective but has limit to how effective it can be.
2. Horizontal scaling (Scale out) : adding additional machines to distribute work on these new added machines to reduce work load on a single machine.
3. Partitioning: Split the data across multiple nodes to distribute workload and avoid bottlenecks.
4. Load Balance: Having a lot of machines running is ineffective if the traffic is on single machine. Here comes load balances to distribute the traffic across machines.
5. Auto Scaling: Helps to automatically spin new machines when burst of traffic apprears.
6. Caching: Go to mechanism to improve latency of APIs. It is effective in reducing load on databases.
7. Content Delivery Network (CDNs): Used to serve static pages, images, videos results in faster load time.
8. Asynchronous Communication: Defer long-running tasks / non-critical tasks to background queues or message brokers. This ensures your main application remains responsive to users.
9. Microservices Architecture: Break down your service to small independent services so that these services can scaled independently.
10. Multi-region Deployment: Deploy the application in multiple data centers or cloud regions to reduce latency and improve redundancy.
