# 21. Exam Traps to Avoid

| # | Trap | Remember |
|---|------|---------|
| 1 | VPC scope | VPC = **global**. Subnets = **regional** |
| 2 | Preemptible vs Spot | Preemptible = **24 hr max**. Spot = **no max**. Same price |
| 3 | APIs per project | Enabled/disabled **per project**, not per folder or org |
| 4 | IAM role order | Basic â†’ Predefined â†’ **Custom** (broadest to finest) |
| 5 | Project ID | Immutable **after** creation, but changeable **during** creation |
| 6 | Cloud Storage type | Object storage â€” NOT a file system, NOT a database |
| 7 | Archive cost | Lowest storage cost, but **highest retrieval cost** |
| 8 | Data Access logs | **Off by default** (except BigQuery). Admin Activity = always on |
| 9 | Interconnect SLA | Only **Dedicated Interconnect** has an SLA |
| 10 | GKE nodes | GKE nodes are **Compute Engine VMs** |
| 11 | GKE Autopilot | Autopilot = Google manages nodes. Standard = you manage nodes |
| 12 | Scale-to-zero | Cloud Run âœ…. App Engine Standard âœ…. App Engine Flexible âŒ |
| 13 | SLO vs SLA | SLO = internal. SLA = contractual. SLO should be **stricter** |
| 14 | Windows SLOs | Can **hide burst failures** â€” request-based is more precise |
| 15 | Log Analytics upgrade | **Permanent** â€” cannot be undone |
| 16 | Policy Denied logs | Cannot be disabled (but can be excluded from storage) |
| 17 | Cloud Shell disk | Persistent **5 GB** home directory |
| 18 | Terraform state secrets | State stores secrets in **plaintext** â€” never put secrets in config |
| 19 | `terraform validate` vs `vet` | `validate` = syntax check only. `vet` = policy compliance |
| 20 | SLO must be SMART | "100% availability" is **never** a valid SLO |
