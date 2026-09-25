# External Cloud Integration

## Purpose

Global hyperscaler services can provide specialized AI/ML, analytics, or
application capabilities. NexaBank permits them through a controlled
integration boundary.

## Integration path

``` text
Core Application
      |
      v
API Gateway
      |
      v
Data Classification / Egress Policy
      |
      v
Private Connectivity or Controlled Egress
      |
      v
External Hyperscaler Service
```

The external service must not receive direct database access.

## Identity and transport

Use workload identity and federated trust where supported instead of
long-lived static secrets. Use mTLS for mutual endpoint authentication
and encrypted transport.

## Rate limits and quotas

The API Gateway should enforce request-rate limits, consumer quotas,
burst limits, payload limits, and per-service limits.

These are intentionally documented controls rather than separate
components on the primary diagram.

## Dependency isolation

External services are treated as non-core dependencies. Core banking
operations should continue where an external capability is unavailable
unless it is explicitly part of a critical synchronous transaction.

## Circuit breaker and async workloads

``` text
Application -> API Gateway -> External Service
                              |
                           Failure
                              |
                        Circuit Breaker
```

For non-immediate workloads:

``` text
Application -> Queue -> External Service
                         |
                      Retry / DLQ
```

Retries must be bounded and safe for the operation type.

## External telemetry

Telemetry should be minimized and sanitized. Sensitive customer or
transaction data should not be placed in third-party logs unless
explicitly approved.
