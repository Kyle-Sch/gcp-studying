# 13. Alerting & SLOs

## Alert Policy Components

- **Condition** â€” what triggers it (threshold / absence / rate of change / uptime failure)
- **Notification channels** â€” email, SMS, PagerDuty, Slack, Pub/Sub, webhook
- **Documentation** â€” runbook link or description shown in the alert

## SLI / SLO / SLA / Error Budget

| Term | What it is | Who sets it |
|------|-----------|------------|
| **SLI** | Metric measuring one aspect of reliability = `good events Ã· all events` | Engineering |
| **SLO** | SLI + target (e.g., 99.9%) â€” **internal** reliability goal | Product + Engineering |
| **SLA** | Contractual promise to customers â€” breach = compensation | Business / Legal |
| **Error Budget** | `100% - SLO` â€” how much failure is acceptable | Shared |

> **SLO must be stricter than SLA.** Alert thresholds should be stricter than SLO.

## SLO Must Be S.M.A.R.T.

**Specific** Â· **Measurable** Â· **Achievable** Â· **Relevant** Â· **Time-bound**

> "100% availability" is never a valid SLO â€” nothing achieves 100%.

## SLO Calculation Types

| Type | Formula | Risk |
|------|---------|------|
| **Request-based** | good requests Ã· total requests | More precise |
| **Windows-based** | good windows Ã· total windows | Can **hide burst failures** |

## Error Budget in Action

```
SLO = 99.9%  â†’  Error Budget = 0.1%  â†’  ~43 minutes downtime/month

Budget healthy  â†’  ship features fast
Budget exhausted  â†’  slow releases, focus on reliability
```

## Burn Rate Alerts
- Alert fires when error budget is being **consumed faster than planned**
- Uses a lookback window (e.g., 60 min) + a burn rate threshold multiplier
