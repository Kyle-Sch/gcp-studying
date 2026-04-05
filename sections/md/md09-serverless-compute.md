# 9. Serverless Compute

## Side-by-Side Comparison

| | Cloud Run | Cloud Run Functions | App Engine Standard | App Engine Flexible |
|--|-----------|--------------------|--------------------|---------------------|
| **Unit** | Container | Single function | App | App in container |
| **Trigger** | HTTP / Pub/Sub | Events / HTTP | HTTP | HTTP |
| **Scale to zero** | âœ… Yes | âœ… Yes | âœ… Yes | âŒ No |
| **Runtime** | Any Linux 64-bit | Node, Python, Go, Java, .NET, Ruby, PHP | Fixed runtimes | Any (Docker) |
| **Billing** | Per 100ms request | Per 100ms execution | Per instance-hour | Per instance-hour |
| **Built on** | Knative | â€” | Proprietary sandbox | Compute Engine VMs |

## When to Use Which

```
Full containerized web app?                â†’ Cloud Run
Event-triggered, single-purpose function? â†’ Cloud Run Functions
Simple web app, auto-managed runtime?     â†’ App Engine Standard
Custom runtime, no scale-to-zero needed?  â†’ App Engine Flexible
Full OS control, persistent workloads?    â†’ Compute Engine
Container orchestration at scale?         â†’ GKE
```
