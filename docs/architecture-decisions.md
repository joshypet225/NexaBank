# Architecture Decision Records

## ADR-001 --- Sovereign core data plane

**Decision:** Keep authoritative banking data and critical processing
within the Nigerian sovereign boundary.\
**Reason:** Stronger control over residency, security, recovery, and
governance.

## ADR-002 --- Asynchronous read replication

**Decision:** Use asynchronous primary-to-read-replica replication.\
**Reason:** Enables read scaling without forcing primary writes to wait
for every replica.\
**Trade-off:** Replicas can lag.\
**Mitigation:** DAL read-after-write routing and lag monitoring.

## ADR-003 --- Asynchronous DR replication

**Decision:** Use asynchronous primary-to-DR replication.\
**Reason:** Practical cross-site recovery with a bounded recovery-point
window.\
**Trade-off:** Transactions not yet replicated can be lost during
catastrophic failure.\
**Mitigation:** Continuous replication, lag monitoring, and an explicit
RPO.

## ADR-004 --- Independent control plane

**Decision:** Health monitoring and failover orchestration operate
independently of the application stacks.\
**Reason:** Recovery controls must survive application/site failure.

## ADR-005 --- Bank-controlled cryptographic root

**Decision:** Include a bank-controlled HSM/root-of-trust in the key
model.\
**Reason:** Preserves institutional control over highly sensitive key
material.

## ADR-006 --- Immutable backup

**Decision:** Maintain a separate immutable backup repository.\
**Reason:** Protects recovery data from destructive administration,
ransomware, and corruption.

## ADR-007 --- Controlled hyperscaler integration

**Decision:** External services are accessed through an explicit
integration and data-policy boundary.\
**Reason:** Enables specialized cloud capabilities without making the
external provider the system of record.

## ADR-008 --- External dependency isolation

**Decision:** Treat external hyperscaler services as non-core
dependencies.\
**Reason:** Limits the blast radius of external outages or compromise.

## ADR-009 --- Rate limits and quotas as documented controls

**Decision:** Keep rate limiting, quotas, retries, and circuit breakers
in operational documentation rather than cluttering the primary
diagram.\
**Reason:** Preserves diagram readability.

## ADR-010 --- Read-after-write is separate from RPO

**Decision:** Handle read-after-write consistency through DAL
routing/replication-position awareness.\
**Reason:** RPO measures acceptable disaster data loss; it does not
guarantee that an immediate read sees a newly committed write.
