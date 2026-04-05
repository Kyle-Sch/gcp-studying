# 4. Compute Engine

## Pricing Discounts

| Discount Type | How it works |
|---------------|-------------|
| **Sustained-use** | Auto-applied when VM runs >25% of a month |
| **Committed-use** | 1 or 3 year commitment â†’ up to 57% off |
| **Preemptible VM** | Up to 90% cheaper Â· max **24-hour runtime** Â· GCP can terminate anytime |
| **Spot VM** | Same price as preemptible Â· **no max runtime** Â· still terminable |

> **Key difference:** Preemptible = 24 hr max. Spot = no max runtime. Same price.

## VM Storage Options

| Storage Type | Description |
|-------------|-------------|
| **Zonal Persistent Disk** | Default â€” reliable block storage |
| **Regional Persistent Disk** | Replicated across 2 zones |
| **Local SSD** | High performance but **transient** â€” data lost when VM stops |
| **Cloud Storage** | Object storage â€” affordable, not a disk |
| **Filestore** | High-performance NFS file storage |

## Scaling
- Use **Managed Instance Groups (MIGs)** + Autoscaling
- Scale **out** (add more VMs) is preferred over scale **up** (bigger VMs)
