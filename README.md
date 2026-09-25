# NexaBank --- Sovereign, Resilient Financial Cloud Reference Architecture

> A security-first reference architecture for a Nigerian financial
> institution, focused on data sovereignty, business continuity,
> disaster recovery, security, auditability, and controlled global-cloud
> interoperability.

## Executive Summary

NexaBank is a conceptual reference architecture for a Nigerian financial
institution that modernizes its banking platform while retaining strong
control over sensitive data, security boundaries, business continuity,
disaster recovery, and external cloud integrations.

The authoritative data and core processing plane remain inside a
Nigerian sovereign data and processing boundary. The design uses
separate primary and disaster-recovery sites, an independent control
plane, centralized IAM/PAM, bank-controlled key protection, immutable
backups, security monitoring, and a controlled integration boundary for
global hyperscaler services.

This is a reference architecture, not a production deployment or a claim
of regulatory certification.


## Architecture

![Nexabank Architecture].(/architecture/nexabank-reference-architecture.png)

## Architecture Goals

-   Keep authoritative core banking data and processing within the
    Nigerian sovereign boundary.
-   Provide a separate DR site and immutable backup environment.
-   Target RPO and RTO objectives below five minutes.
-   Scale reads without forcing writes to wait for all replicas.
-   Preserve read-your-writes behavior through DAL-level routing.
-   Centralize identity and privileged access.
-   Protect sensitive cryptographic material through a bank-controlled
    HSM model.
-   Maintain independent health monitoring and failover orchestration.
-   Permit approved external AI/ML and application services through a
    controlled integration boundary.
-   Prevent external services from becoming the system of record for
    regulated core banking data.

## High-Level Architecture

``` text
Internet
   |
Traffic Manager
   |
+---------------- Nigerian Sovereign Data & Processing Boundary --------------+
|                                                                             |
|   PRIMARY SITE                         DR SITE                              |
|   WAF                                  WAF                                  |
|    |                                    |                                   |
|   Load Balancer                        Load Balancer                        |
|    |                                    |                                   |
|   Application Layer                    Application Layer                    |
|    |                                    |                                   |
|   Network Firewall                     Network Firewall                     |
|    |                                    |                                   |
|   Data Access Layer                    Data Access Layer                    |
|    |                                    |                                   |
|   Primary DB                           DR DB                                |
|    |                                                                        |
|   Async Read Replicas                                                       |
|                                                                             |
|   Immutable Backup      Independent Control Plane      Management Plane     |
+-----------------------------------------------------------------------------+
                              |
                     Controlled Integration
                              |
                     Global Hyperscaler
```

## Primary and DR Sites

Both sites contain WAF, application load balancing, elastic application
capacity, network security, a Data Access Layer, and database services.

The DR site is a separate failure domain. It is intended to support
recovery after a primary-site failure and must be monitored and tested
independently.

## Database Architecture

### Primary to read replicas

Read replicas use **asynchronous replication**.

The primary database acknowledges a committed write after durable
primary persistence rather than waiting for every read replica.

``` text
                WRITE
                  |
                  v
             +---------+
             | Primary |
             |   DB    |
             +----+----+
                  |
          Async replication
             /                     v           v
       Read Replica  Read Replica
```

This improves write latency and permits read scaling.

### Read-after-write consistency

Because replicas can lag, the Data Access Layer must not blindly route
every read to a replica.

After a successful write, the DAL can retain the relevant
transaction/replication position and route an immediate dependent read
to the primary until a suitable replica has caught up.

``` text
Write -> Primary -> Commit position
                         |
                         v
                   DAL consistency
                    /           \            
         replica caught up?   replica not caught up?
                 |                   |
                YES                  NO
                 |                   |
                 v                   v
             Replica              Primary
```

This provides application-level read-your-writes behavior without
requiring synchronous replication for every read replica.

### Cross-site replication

Primary-to-DR replication is asynchronous.

The immutable backup repository is populated asynchronously as a
separate recovery mechanism.

## Data Access Layer

The DAL provides:

-   Connection pooling
-   Read/write routing
-   Read-replica selection
-   Read-after-write consistency handling
-   Query timeouts
-   Retry controls
-   Database access logging
-   Query-execution exception logging

## Security Architecture

The architecture separates Internet-facing, application, data,
management, control, and backup responsibilities.

Core principles include:

-   Default deny
-   Explicit allow
-   Least privilege
-   Network segmentation
-   Controlled administrative access
-   Centralized IAM
-   Privileged Access Management
-   Strong authentication
-   Security logging
-   Immutable audit evidence

IAM is a cross-cutting identity capability rather than a component
belonging only to the network layer.

## Encryption and Key Management

The reference design uses a layered key-management model:

``` text
Bank-controlled HSM
        |
        v
Highly sensitive key material
        |
        v
Cloud/CSP KMS
        |
        v
Encryption / key wrapping
        |
        +-- Primary
        +-- DR
        +-- Backup
```

Recovery must not depend exclusively on the failed primary site. Key
availability for DR and backup therefore requires controlled
wrapping/rewrapping, strong authorization, HSM-backed protection, key
rotation, and auditability.

The exact implementation must be validated against the selected HSM/KMS
products.

## Immutable Backup

The secondary backup site contains an immutable backup repository
intended to protect recovery data from accidental deletion, malicious
deletion, ransomware, administrative compromise, and destructive
application errors.

A WORM-style retention model may be used where supported.

## Independent Control Plane

The control plane contains:

-   Independent Health Monitoring
-   Failover Orchestrator
-   Audit/Log Collector

The control plane observes both primary and DR environments and can
initiate approved recovery actions.

This reduces dependency on the production stack for failure detection
and recovery.

## Audit and Observability

Security, application, infrastructure, database, and control-plane
events are collected through monitoring/SIEM components and forwarded to
an immutable audit repository.

Relevant events include:

-   WAF security events
-   Access logs
-   Application operation logs
-   Traffic logs
-   Database access/audit logs
-   Query execution exceptions
-   Security events
-   Control-plane events

## Global Hyperscaler Integration

Global cloud services are treated as external capabilities rather than
the authoritative banking data plane.

The controlled integration path is:

``` text
Application/API Gateway
          |
          v
Data Classification & Egress Policy
          |
          v
Private Connectivity /
Controlled Internet Egress
          |
          v
Global Hyperscaler Services
```

Potential external capabilities include AI/ML, analytics, and approved
application/API services.

### Identity and transport security

The integration uses:

-   Workload identity
-   Federated identity trust
-   mTLS
-   Least-privilege external roles

The external service does not receive broad access to the Nigerian
network, databases, IAM, PAM, HSM/KMS control plane, backup
infrastructure, or failover orchestration.

### Data crossing the boundary

Data is classified before egress.

``` text
 Data Classification
        |
        v
   Egress Policy
     /        \   
   ALLOW      DENY
    |          |
    v          v
Transform    Block + Alert
if required
```

Regulated or sensitive data is not transferred merely because it can be
encrypted. Cross-border processing must satisfy applicable legal,
regulatory, contractual, and organizational requirements.

### External dependency resilience

Non-critical external capabilities can use:

-   Async queue
-   Controlled retry
-   Exponential backoff
-   Circuit breaker
-   Dead-letter queue
-   Alerting

Core banking/payment processing must not become dependent on continuous
availability of a non-core external hyperscaler service.

## RPO and RTO

**Target RPO:** \< 5 minutes

**Target RTO:** \< 5 minutes

These are architecture objectives, not demonstrated production
measurements.

Achieving them requires automated or well-rehearsed failover,
pre-provisioned DR capacity, fast traffic convergence, database recovery
readiness, key availability at DR, and regular recovery testing.

## Regulatory and Policy Context

The architecture is informed by Nigerian financial-sector IT standards,
the Nigeria Data Protection Act, and the National Cloud Policy 2025.

The CBN financial-services IT standards programme addresses enterprise
architecture, information management, IT security, service management,
data-centre infrastructure, business continuity, risk management, and
related capabilities.

The NDPC states that the Nigeria Data Protection Act applies to
organisations operating in Nigeria and addresses cross-border transfers
of personal data. The architecture therefore treats external data
transfer as a controlled policy decision.

Nigeria's National Cloud Policy 2025 establishes a cloud-first direction
while emphasizing secure and sovereign cloud adoption, local data
residency, data classification, shared responsibility, and controlled
participation by global hyperscalers. Its primary mandate is directed at
Federal Public Institutions, so this project uses its sovereignty and
governance principles as architectural reference points rather than
claiming that every provision directly binds a commercial bank.

## Failure Scenarios

  -----------------------------------------------------------------------
  Failure                             Expected architectural response
  ----------------------------------- -----------------------------------
  Application instance failure        Load balancer routes traffic to
                                      healthy instances

  Primary site failure                Independent monitoring and failover
                                      orchestration activate DR

  Read replica lag                    DAL routes consistency-sensitive
                                      reads to primary

  Database/site failure               Recover through DR and/or approved
                                      backup procedure

  External hyperscaler outage         Circuit breaker and queue isolate
                                      the dependency

  Ransomware/destructive event        Recover from immutable backup

  Primary key-service failure         DR uses independently available
                                      controlled key material
  -----------------------------------------------------------------------

## Documentation

-   [Architecture Overview](docs/architecture-overview.md)
-   [Security Model](docs/security-model.md)
-   [Disaster Recovery](docs/disaster-recovery.md)
-   [Data Sovereignty](docs/data-sovereignty.md)
-   [External Cloud Integration](docs/external-cloud-integration.md)
-   [Architecture Decisions](docs/architecture-decisions.md)
-   [Operational Controls](docs/operational-controls.md)
-   [Threat Model](docs/threat-model.md)

## Assumptions

-   Primary and DR facilities have adequate physical and network
    resilience.
-   The selected database supports asynchronous replication and suitable
    consistency metadata.
-   HSM/KMS products support the required key lifecycle.
-   Traffic management can converge within the desired recovery target.
-   DR infrastructure is pre-provisioned or rapidly provisionable.
-   Security monitoring remains sufficiently independent to detect
    site-level failures.
-   External providers can satisfy required contractual, security,
    privacy, and data-residency controls.
-   Recovery procedures are tested regularly.

## Limitations

This is a conceptual reference architecture. It does not by itself
prove:

-   Regulatory compliance
-   That the stated RPO/RTO has been achieved
-   That every cloud provider supports every depicted capability
-   That a particular database implements the proposed consistency
    behavior
-   That a cross-border transfer is legally permissible
-   That an external provider will never retain telemetry or metadata

Those claims require implementation evidence, product validation,
contracts, testing, regulatory interpretation, and independent
assurance.

## Repository Structure

``` text
nexabank/
├── README.md
├── architecture/
│   ├── nexabank-reference-architecture.drawio
│   ├── nexabank-reference-architecture.png
│   └── architecture-decisions.md
├── docs/
│   ├── architecture-overview.md
│   ├── security-model.md
│   ├── disaster-recovery.md
│   ├── data-sovereignty.md
│   └── external-cloud-integration.md
└── LICENSE
```

## Portfolio Positioning

This project demonstrates:

-   Cloud architecture
-   DevOps and infrastructure thinking
-   Disaster recovery
-   Distributed database design
-   Data sovereignty
-   IAM/PAM
-   Cryptographic key management
-   Network segmentation
-   Observability and SIEM
-   Immutable backup
-   External cloud integration
-   Failure-domain design
-   Regulatory-aware architecture

## References

-   Central Bank of Nigeria --- Financial Services IT Standards:
    https://www.cbn.gov.ng/ITStandards/
-   CBN --- Overview and Summary of IT Standards:
    https://www.cbn.gov.ng/itstandards/Overview.html
-   CBN --- IT Standards Governance:
    https://www.cbn.gov.ng/itstandards/Governance.html
-   CBN --- Expected Impacts and Benefits:
    https://www.cbn.gov.ng/itstandards/Expected.html
-   Nigeria Data Protection Commission --- FAQs:
    https://ndpc.gov.ng/faqs/
-   NITDA --- National Cloud Policy 2025:
    https://nitda.gov.ng/wp-content/uploads/2025/10/National-Cloud-Policy-2025-Oct2-2025.pdf

------------------------------------------------------------------------

**Architecture status:** Reference Architecture v1.0

**Implementation status:** Conceptual / not yet implemented

**Target RPO:** \< 5 minutes

**Target RTO:** \< 5 minutes

**Compliance status:** Regulatory and policy considerations
incorporated; formal compliance requires implementation-specific
assessment and evidence.
