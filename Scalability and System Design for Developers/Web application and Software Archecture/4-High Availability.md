# 4.High Availability

## What is high availability?
**High availability, also known as HA, is the ability of the system to stay online despite having failures at the infrastructural level in real-time.**

High availability ensures the uptime of the service much more than the normal time. It improves the reliability of the system and ensures minimum downtime. The sole mission of highly available systems is to stay online and stay connected.

In the industry, HA is often expressed as a percentage. For instance, when the system is 99.99999% highly available, it simply means 99.99999% of the total hosting time the service will be up. You might often see this in the Service Level Agreements (SLA) of cloud platforms.

**The smooth functioning of the mission-critical systems relies on continual connectivity with their network/servers. To meet the high availability requirements, systems are designed to be fault-tolerant and their components are made redundant.**

## Reasons For System Failures

- Software crashes
- Hardware failures: overloaded CPU and RAM, hard disk failures, nodes going down, and network outages
- Human errors: It includes flawed configurations and what not.
- Planned downtime: Besides the unplanned crashes, there are planned down times that involve routine maintenance operations, patching software, hardware upgrades, etc.

## Achieving High Availability
- **Fault Tolerance**: Fault tolerance is a system’s ability to stay up despite taking hits. To achieve high availability at the application level, the entire massive service is architecturally broken down into **smaller loosely coupled services called micro services.**
    - pros of mocroservice: Easier management, Easier development, Ease of adding new features, Ease of maintenance, High availability 
    ![microservice architecture]()

- **Redundancy (Active-passive HA mode)**: Redundancy is duplicating the components or instances and keeping extras on standby to take over in case the active instances go down. It is the fail-safe, backup mechanism. There are systems like GPS, aircrafts, and communication satellites that have zero downtime. Systems should be well monitored in real-time to detect any bottlenecks or single point of failures. Also, the systems become intelligent enough to add or remove instances on the fly as per the requirements
    ![ha redundancy]()

- **Replication (Active-active HA mode)**: Replication means having a number of similar nodes running the workload together. There are no standby or passive instances. When a single or a few nodes go down, the remaining nodes bear the load of the service. Think of this as load balancing. **Businesses often use multi-cloud platforms to deploy their workloads which ensures further availability**
    ![HA replication]()

- **High Availability Clustering** (fail-over cluster):  contains a set of nodes running in conjunction with each other that ensures high availability of the service. The nodes in the cluster are connected by a private network called the **heartbeat network that continuously monitors the health and the status of each node in the cluster.**  HA clusters use several techniques such as disk mirroring/Redundant Array of Independent Disks (RAID), redundant network connections, redundant electrical power etc. **The network connections are made redundant.** So, if the primary network goes down the backup network takes over. **Multiple HA clusters run together in one geographical zone** ensuring minimum downtime and continual service.
    ![High Available Cluster]()
