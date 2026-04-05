# 18. Security Design

## Shared Responsibility Model

| Google secures | You secure |
|---------------|-----------|
| Hardware Â· Physical infrastructure | IAM Â· Firewall rules |
| Hypervisor Â· Network fabric | Data encryption choices |
| Verified boot Â· DDoS at network edge | Application code Â· Access controls |

## Core Security Principles

| Principle | What it means |
|-----------|--------------|
| **Least Privilege** | Grant only what's needed â€” applies to users, service accounts, and processes |
| **Separation of Duties** | No single person can create + delete + audit; developers â‰  deployers |
| **Defense in Depth** | Multiple independent security layers â€” never rely on just one |
| **Regular Audits** | Use Audit Logs to detect attacks and compliance violations |

## Security Tools Reference

| Tool | What it does |
|------|-------------|
| **IAM** | Auth + authz for humans and service accounts |
| **Identity-Aware Proxy (IAP)** | App access control without a VPN â€” verifies identity at the application layer |
| **Security Command Center** | Centralized vulnerability scanning and threat detection |
| **Cloud Armor** | DDoS protection + WAF for external load balancers |
| **VPC Service Controls** | Security perimeter around GCP APIs â€” prevents data exfiltration |
| **Organization Policies** | Governance guardrails at org / folder / project level |
| **Private Google Access** | Lets VMs with no public IP reach GCP APIs |
