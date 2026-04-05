# 12. Cloud Logging

## How Logs Flow

```
Log Sources
(GCP services Â· GKE Â· Compute Engine via Ops Agent Â· your apps)
        â†“
    Log Router  â†  inclusion / exclusion filters
        â†“
   Log Sinks
   â”œâ”€â”€ Cloud Logging Buckets  (default storage)
   â”œâ”€â”€ BigQuery               (analytics)
   â”œâ”€â”€ Pub/Sub                (streaming to third parties)
   â””â”€â”€ Cloud Storage          (long-term archival)
```

## Log Types

| Type | Description |
|------|-------------|
| **Platform logs** | Written by GCP services (e.g., VPC Flow Logs) |
| **Security logs** | Audit logs + Access Transparency |
| **User-written logs** | From your apps via the Logging API, Ops Agent, or stdout |
| **Multi/Hybrid Cloud** | From non-GCP environments |

## Key Features

| Feature | Detail |
|---------|--------|
| **Log-based metrics** | Create counter or distribution metrics from log filters |
| **Log Explorer** | Troubleshooting UI â€” stream logs, histogram, filter |
| **Log Analytics** | BigQuery-powered SQL queries on log data |

> **Log Analytics bucket upgrade is permanent** â€” you cannot downgrade once enabled.

## Log Retention Defaults

| Bucket | Retention | Cost |
|--------|-----------|------|
| `_Required` (Admin Activity + System Event) | **400 days** | Free â€” cannot delete |
| `_Default` | **30 days** | Included |
| Custom buckets | Configurable | Per-GB |
