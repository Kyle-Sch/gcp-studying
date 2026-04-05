# 17. Infrastructure Design

## Requirements â†’ Design Flow

```
Qualitative â†’ User Stories ("As a [role], I want to... so that...")
Quantitative â†’ KPIs + SLOs/SLIs (must be S.M.A.R.T.)
```

**Good SLI:** stable signal in normal operation, clearly different during an outage
**Bad SLI:** high variance, overlapping ranges between healthy and unhealthy

## Microservices vs Monolith

| | Monolith | Microservices |
|--|----------|--------------|
| Codebase | Single | One per service |
| Data store | Shared database | Each service owns its own data |
| Deploy | All-at-once | Independently |
| Scale | Entire app | Per service |
| Failure blast radius | Large | Isolated |

**Decompose by:** Feature area Â· Architectural layer Â· Shared functionality (auth, etc.)

## Reliability Patterns

### Avoid Single Points of Failure
- Deploy **N+2** instances (N for load + 1 for upgrades + 1 for failures)
- Spread across **multiple zones** to avoid correlated failures
- Make units **stateless and interchangeable**

### Failure Domains
A group of related items that can fail together:
`machine â†’ rack â†’ zone â†’ region â†’ software version â†’ shared config`

Mitigation: distribute across failure domains; use separate zones/regions

### Cascading Failures
One failure overloads other services, causing them to fail in turn.
Prevention: **circuit breakers**, load shedding, autoscaling, health checks

### Circuit Breaker Pattern
```
Client â†’ [Circuit Breaker Proxy] â†’ Service

If service is unhealthy:
  Circuit breaker OPENS â†’ stops forwarding requests
  Service recovers â†’ circuit breaker CLOSES â†’ traffic resumes gradually
```
> In GKE: **Istio** implements circuit breakers automatically

### Truncated Exponential Backoff
Retry on failure, but wait longer each time + add random jitter:
```
Fail â†’ wait 1s + jitter â†’ retry
Fail â†’ wait 2s + jitter â†’ retry
Fail â†’ wait 4s + jitter â†’ retry
...up to a max limit, then give up
```
Prevents thundering herd / overloading a recovering service

### Lazy Deletion
Mark data as deleted â†’ keep it for a grace period â†’ then purge permanently.
Enables recovery if a user accidentally deletes data.

## Disaster Recovery

| Term | Definition |
|------|-----------|
| **RPO** â€” Recovery Point Objective | Maximum data loss you can tolerate (e.g., "1 hour of data") |
| **RTO** â€” Recovery Time Objective | Maximum acceptable downtime (e.g., "5 minutes") |

**DR Planning Process:**
1. Brainstorm failure scenarios
2. Assign RPO + RTO per scenario
3. Prioritize by business impact
4. Define backup strategy
5. Document + communicate recovery steps
6. **Test at least once per year** (ideally more frequently)

**Backup strategies:**

| Service | Strategy |
|---------|---------|
| Firestore | Daily automated backups â†’ multi-regional Cloud Storage |
| Cloud SQL | Binary logging + failover replica in another zone |
| BigQuery | Re-import from source (if source is retained) |
