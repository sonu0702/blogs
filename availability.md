---
title: "Availability in System Design"
date: "2024-08-28"
description: "Availability is defined as the proportion of time a system is up and serving the traffic. It is defined in terms of percentage. Can also be divided into tiers of 2 nines (99%), 3 nines (99.9%), 4 nines (99.99%), 5 nines (99.999%) and 6 nines (99.9999 %)."
tags: ["react", "typescript", "tutorial"]
---

Availability is defined as the proportion of time a system is up and serving the traffic. It is defined in terms of percentage. Can also be divided into tiers of 2 nines (99%), 3 nines (99.9%), 4 nines (99.99%), 5 nines (99.999%) and 6 nines (99.9999 %).

### Ways to improve Availability

1. Redundancy : It is a way of having backup components which can takeover when primary components fail.
    
    **Technique to Add Redundancy**
    
    1. Server Redundancy: Having multiple instance of the same server helps in distributing traffic across servers, ensuring if one fails other can provide service.
    2. Database Redundancy: Creating a replica database that can takeover when primary database fails.
    3. Geographic Redundancy: Distributing resources across multiple geographic locations to solve/mitigate the regional failures
2. Load Balancing: It distributes the incoming traffic across multiple servers to ensure that no single server becomes a bottleneck this improving performance and availability.
    
    **Technique to Add Load Balancing**
    
    1. Hardware Load Balancing: Physical devices that distributes traffic based on preconfigured rules.
    2. Software Load Balancing: Software solutions that manage traffic distribution. Solutions like HAProxy, Nginx, or cloud-based solution like AWS Elastic Load Balancer.
3. Data Replication: It is a way of copying data to multiple locations either asynchronously or in realtime ensuring data is available even one location fails.
    
    **Technique of Data Replication**
    
    1. Synchronous Replication: Data is replicated in real-time to ensure consistency across location.
    2. Asynchronous Replication: Date is replicated with delay, which can be more efficient but may result in slight data inconsistencies. 
4. Failover Mechanism: Failover mechanism automatically witches to redundant system when a failure detected.
    
    **Techniques of Failover Mechanism**
    
    1. Active-Passive failover mechanism: A primary active component is backed by a passive standby component that takes over upon failure.
    2. Active-Active failover mechanism: All components are active and share the load. If one fails, remaining components continue to handle the load seamlessly.
5. Monitoring & Alerts: Continuous health monitoring involves checking the status of the system components to detect failures early and trigger alert for immediate action.
    
    **Techniques for Monitoring & Alerts**
    
    1. Heartbeat Signals: Regular signals sent between components to check their status.
    2. Health Check: Automated scripts or tools that perform regular check on components.
    3. Alerting systems: Tools like PagerDuty or OpsGenie that notify administrators of any issues.

### Best practices for Availability

1. Build for failure: Assume that components can go down at any moment and build the required fall back mechanisms
2. Implement Health Check
3. Use Multiple availability zones: Distribute the system across multiple data centers to prevent localized failures.
4. Practice chaos Engineering: Check reliability by intentionally introducing failures.
5. Implement Circuit Breakers: Prevent cascading failures by quickly cutting off problematic services
6. Use caching wisely: Caching can reduce load on databases.
7. Plan for capacity: Ensure your system can handle both expected and unexpected loads.
