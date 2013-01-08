---
title: "Multi Data Center Replication: Architecture"
project: riakee
version: 1.0.0+
document: cookbook
toc: true
audience: intermediate
keywords: [mdc, repl, bnw]
---

## How Riak EE 1.3 Replication Works

In multi-datacenter replication, one cluster acts as the "source cluster". The source cluster handles replication requests from one or more "sink clusters" (generally located in datacenters in other regions or countries). If the datacenter with the source cluster goes down, a sink cluster can take over as the primary cluster. In this sense, Riak's multi-datacenter capabilities are "masterless." 

In multi-datacenter replication, there are two primary modes of operation: full-sync and real-time. In full-sync mode, a complete synchronization occurs between source and sink cluster(s). In real-time mode, continual, incremental synchronization occurs - replication is triggered by new updates. Full-sync can be performed upon initial connection of a sink cluster. Full-sync is also triggered if the TCP connection between source and sink cluster is severed and then recovered.

Full-sync and real-time modes are described in detail below. 

## Concepts

### Cluster Manager
The cluster manager is responsible for ...

### Sources

### Sinks

### Fullsync Coordinator

### Leadership



## Full-Sync Replication

## Real-time Replication
-