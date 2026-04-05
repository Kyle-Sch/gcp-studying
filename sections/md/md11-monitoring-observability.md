# 11. Monitoring & Observability

## The Four Golden Signals

| Signal | Measures | Why it matters |
|--------|---------|----------------|
| **Latency** | Time to return a result | Changes indicate emerging problems |
| **Traffic** | Volume of requests | Baseline for capacity planning |
| **Saturation** | How full a resource is (CPU, memory, disk) | Predicts imminent failure |
| **Errors** | Rate of failed requests | Direct user impact |

## GCP Observability Products

| Product | Purpose |
|---------|---------|
| **Cloud Monitoring** | Metrics, dashboards, uptime checks, alerting |
| **Cloud Logging** | Collect, route, store, and query logs |
| **Cloud Trace** | Distributed tracing â€” find latency bottlenecks |
| **Cloud Profiler** | Continuous CPU/memory profiling in production |
| **Error Reporting** | Auto-groups errors and sends alerts |

## Cloud Monitoring Facts

- **1,500+ metrics** from 100+ GCP services â€” ingested **automatically**, free
- **Compute Engine**: 25+ unique metrics per VM instance
- **Ops Agent** â€” install on VMs to collect process and third-party app metrics/logs
- **Google Managed Prometheus (GMP)** â€” built-in Prometheus support for GKE
- **Uptime checks** â€” verify endpoint reachability from multiple global locations
