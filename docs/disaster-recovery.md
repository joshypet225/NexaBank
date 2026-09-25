# Disaster Recovery

## Objective

Recover critical banking capabilities following primary-environment
failure.

**Target RPO:** \< 5 minutes\
**Target RTO:** \< 5 minutes

These are design objectives and must be demonstrated through testing
before becoming operational commitments.

## Recovery architecture

``` text
PRIMARY
  |
  | Asynchronous replication
  v
DR DATABASE
  |
DR APPLICATION
  |
Traffic Manager
```

An independent control plane monitors both environments.

## Conceptual failover sequence

1.  Independent monitoring detects primary-site failure.
2.  The control plane evaluates health and failover conditions.
3.  DR readiness is validated.
4.  Required database state is confirmed.
5.  Key-management capability is confirmed.
6.  DR application capacity is activated or confirmed.
7.  Traffic management redirects users to DR.
8.  Application/database health checks validate recovery.
9.  Operators receive recovery events and audit evidence.

## RPO

RPO depends on replication lag and the last durable recovery point.
Monitor replication position, lag, last successful replication, and
recovery-point age.

## RTO

RTO depends on DR capacity, configuration recovery, network convergence,
traffic routing, identity availability, key availability, application
startup, and health validation.

## Immutable backup recovery

If primary and DR are both affected by corruption, ransomware, or
destructive administration, the immutable backup repository becomes the
recovery source.

## Testing

Perform tabletop exercises, application failover tests, database
recovery tests, key-recovery tests, backup restoration tests, full DR
exercises, and external-dependency failure tests.

> The recovery mechanism must not depend exclusively on the failed
> environment.
