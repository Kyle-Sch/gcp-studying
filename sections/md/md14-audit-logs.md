# 14. Audit Logs

## The Four Audit Log Types

| Log Type | Records | Always On? | Retention | Cost |
|----------|---------|-----------|-----------|------|
| **Admin Activity** | Config/metadata changes (create VM, change IAM) | âœ… Always | 400 days | Free |
| **System Event** | Google-initiated changes (non-human admin actions) | âœ… Always | 400 days | Free |
| **Data Access** | Reads/writes to user data and configs | âŒ Off by default (except BigQuery) | 30 days | Charged |
| **Policy Denied** | Access denied by a security policy | âœ… Always | 30 days | Charged |

> To view **Admin Activity / System Event** logs: requires `Logging/Logs Viewer` or `Project/Viewer`
> To view **Data Access** logs: requires `Logging/Private Logs Viewer` or `Project/Owner`

## Access Transparency Logs
- Records actions by **Google employees** on your data
- *Different* from Audit Logs, which record **your organization's** actions
- Requires an appropriate support package
