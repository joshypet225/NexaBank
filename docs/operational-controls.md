# Operational Controls

## Rate limiting

Protect APIs and integrations from excessive request volume. Define
limits per client, API, workload, tenant, and external dependency.

## Quotas

Constrain cumulative consumption over a defined period, such as requests
per minute/day or external AI/API calls.

## Timeouts

Every remote call should have an explicit timeout. No request should
wait indefinitely for a dependency.

## Retries

Use bounded retries and exponential backoff with jitter where
appropriate. Do not blindly retry non-idempotent financial operations.

## Circuit breakers

Prevent repeated calls to an unhealthy dependency.

## Bulkheads

Use resource pools or limits so one workload cannot exhaust shared
capacity.

## Queues and dead-letter queues

Use queues for workloads that do not require synchronous completion.
Messages that repeatedly fail should move to a DLQ rather than retry
forever.

## Database connection pooling

The DAL controls connection reuse and maximum database connections.

## Replication monitoring

Track replica lag, last successful replication, replication errors, log
position, and DR recovery-point age.

## Backup monitoring

Track backup success/failure, retention lock, immutability status,
restore tests, and backup age.

## Key-management monitoring

Track key rotation, HSM/KMS availability, authorization failures, and
key-recovery events.

## Change management

Infrastructure, security policies, firewall rules, IAM policies, and
recovery procedures should be version-controlled and change-controlled.
