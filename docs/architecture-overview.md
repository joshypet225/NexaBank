# Architecture Overview

## Purpose

NexaBank models a resilient Nigerian financial platform whose
authoritative data and critical processing remain within a controlled
Nigerian sovereign boundary.

## Major domains

### Sovereign data and processing boundary

Contains the primary site, DR site, data services, application
workloads, backup infrastructure, and core security controls.

### Independent control plane

Provides health monitoring, failover orchestration, and audit/log
collection.

### Management plane

Provides IAM, PAM, bastion access, and infrastructure/resource
orchestration.

### External integration boundary

Provides a governed path to selected global hyperscaler capabilities
without direct exposure of the core banking network or database.

## Request path

``` text
Internet
   |
Traffic Manager
   |
WAF
   |
Application Load Balancer
   |
Application Layer
   |
Network Firewall
   |
Data Access Layer
   |
Database
```

## Data path

The primary database is authoritative for writes. Read replicas receive
changes asynchronously. The DR database receives cross-site replication
asynchronously. The immutable backup repository provides an additional
recovery copy.

## Trust boundaries

The architecture separates:

-   Internet and application entry
-   Application and data layers
-   Production and DR
-   Production data and backup
-   Core platform and management plane
-   Sovereign platform and external hyperscaler
-   Normal and privileged identities
-   Operational logs and immutable audit evidence

## Design philosophy

Resilience is achieved through multiple failure domains, replication,
backup, independent monitoring, failover, key availability,
segmentation, controlled dependencies, and recovery testing.
