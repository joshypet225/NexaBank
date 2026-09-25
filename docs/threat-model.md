# Threat Model

## Internet attack

**Risk:** Malicious requests reach the application.\
**Controls:** WAF, rate limiting, authentication, validation, network
segmentation, monitoring.

## Compromised application workload

**Risk:** An attacker attempts lateral movement toward sensitive
services.\
**Controls:** Least privilege, network firewall, DAL boundary,
restricted database access, workload identity, monitoring.

## Privileged-account compromise

**Risk:** An attacker gains administrative access.\
**Controls:** IAM, PAM, strong authentication, bastion, least privilege,
auditing, separation of duties.

## Ransomware/destructive action

**Risk:** Production and ordinary backups are encrypted or deleted.\
**Controls:** Immutable backup, independent credentials, retention
protection, segmented backup environment, recovery testing.

## Primary-site loss

**Risk:** Production becomes unavailable.\
**Controls:** DR site, asynchronous replication, independent monitoring,
failover orchestration, traffic management.

## Replica lag

**Risk:** A user receives stale data.\
**Controls:** DAL read-after-write routing and replica-lag monitoring.

## Key-service failure

**Risk:** Recovery data cannot be decrypted.\
**Controls:** Independent key availability, bank-controlled HSM model,
secure wrapping/rewrapping, tested key recovery.

## External-cloud compromise

**Risk:** External service becomes a route into the sovereign
environment.\
**Controls:** API gateway, egress policy, workload identity, mTLS, least
privilege, no direct database access, network isolation, circuit
breaking.

## Telemetry exfiltration

**Risk:** Sensitive data appears in third-party logs.\
**Controls:** Data minimization, payload sanitization, metadata-only
telemetry where possible, egress policy.

## Control-plane failure

**Risk:** Monitoring/failover is unavailable during an incident.\
**Controls:** Independent control plane, separate failure domain,
independent monitoring, tested recovery procedures.
