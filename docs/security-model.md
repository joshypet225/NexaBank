# Security Model

## Security objectives

Protect customer/transaction data, workloads, identities, cryptographic
keys, administration paths, audit evidence, backups, and external
integration paths.

## Defense in depth

``` text
Internet
  |
 WAF
  |
Load Balancer
  |
Application Security
  |
Network Firewall
  |
DAL
  |
Database
```

## Identity and privileged access

IAM provides authentication and authorization. PAM controls privileged
access. Bastion infrastructure provides controlled administrative
access.

## Network security

The default policy is **deny by default**. Only explicitly required
paths are permitted. Internet-facing workloads do not receive direct
database access.

## Cryptographic protection

Sensitive data is encrypted at rest and in transit. The reference model
uses a bank-controlled HSM/root-of-trust model with operational KMS
capabilities where appropriate.

## Key recovery

A primary-site failure must not make encrypted recovery data permanently
inaccessible. DR and backup environments therefore require controlled
key availability through secure wrapping/rewrapping, strong
authorization, HSM-backed protection, rotation, and auditing.

## Auditability

Security-relevant events are collected and forwarded to protected audit
storage. Audit evidence should be protected against unauthorized
modification.

## External integration

External workloads use workload identity, federated trust where
supported, least privilege, mTLS, and controlled egress. A private
connection does not automatically imply trust.

## Principles

-   Least privilege
-   Default deny
-   Separation of duties
-   Defense in depth
-   Explicit trust
-   Strong authentication
-   Encryption
-   Immutable evidence
-   Continuous monitoring
