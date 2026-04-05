# GCP Certification Study Guide
### 70 Practice Questions · All Topics Covered

---

## Table of Contents
1. [Cloud Fundamentals](#1-cloud-fundamentals)
2. [Resource Hierarchy](#2-resource-hierarchy)
3. [IAM & Access](#3-iam--access)
4. [Compute Engine](#4-compute-engine)
5. [VPC Networking](#5-vpc-networking)
6. [Cloud Storage](#6-cloud-storage)
7. [Databases](#7-databases)
8. [Containers & GKE](#8-containers--gke)
9. [Serverless Compute](#9-serverless-compute)
10. [AI & Generative AI](#10-ai--generative-ai)
11. [Monitoring & Observability](#11-monitoring--observability)
12. [Cloud Logging](#12-cloud-logging)
13. [Alerting & SLOs](#13-alerting--slos)
14. [Audit Logs](#14-audit-logs)
15. [SRE Practices](#15-sre-practices)
16. [Terraform on GCP](#16-terraform-on-gcp)
17. [Infrastructure Design](#17-infrastructure-design)
18. [Security Design](#18-security-design)
19. [Gemini Cloud Assist](#19-gemini-cloud-assist)
20. [Quick Decision Trees](#20-quick-decision-trees)
21. [Exam Traps to Avoid](#21-exam-traps-to-avoid)
22. [Practice Questions 1–70](#22-practice-questions)

---
---

# 1. Cloud Fundamentals

## The 5 Traits of Cloud Computing (NIST)

| # | Trait | Meaning |
|---|-------|---------|
| 1 | **On-demand self-service** | Get resources without calling anyone |
| 2 | **Broad network access** | Access from anywhere via internet |
| 3 | **Resource pooling** | Provider shares resources across users |
| 4 | **Rapid elasticity** | Scale up or down instantly |
| 5 | **Measured service** | Pay only for what you use |

## Service Models

| Model | You manage | GCP example |
|-------|-----------|-------------|
| **IaaS** | OS, runtime, app | Compute Engine |
| **PaaS** | App + data only | App Engine, Cloud Run |
| **SaaS** | Nothing | Google Workspace |

## Cloud History (Waves)
- **Wave 1** — Colocation: rent physical data center space
- **Wave 2** — Virtualized data centers: VMs, still user-managed
- **Wave 3** — Container-based: fully automated, elastic ← *Google Cloud*

---
---

# 2. Resource Hierarchy

```
Organization Node
    └── Folders  (optional, nestable)
         └── Projects  ← API & billing unit
              └── Resources  (VMs, buckets, DBs…)
```

## Key Rules

- Policies **inherit downward** — a folder policy applies to all projects inside it
- A **more permissive parent** policy overrides a more restrictive child policy
- Each resource belongs to **exactly one project**
- APIs are enabled/disabled **per project** (not per folder or org)

## Project Identifiers

| Attribute | Globally Unique? | Who sets it? | Can change after creation? |
|-----------|-----------------|--------------|---------------------------|
| **Project ID** | Yes | Google (but you can edit during creation) | **No — immutable** |
| **Project name** | No | You | Yes |
| **Project number** | Yes | Google | No |

## Organization Node

- Required to use Folders
- Auto-created if you're a **Google Workspace** customer
- Otherwise: create one using **Cloud Identity**
- Special roles: `Organization Policy Administrator`, `Project Creator`

---
---

# 3. IAM & Access

## IAM Policy = Who + Role + Resource

- **Who (Principal):** Google Account · Google Group · Service Account · Cloud Identity domain
- **Role:** a collection of permissions
- **Resource:** what the role applies to

## Role Types — Broadest to Finest

| Type | Scope | Example |
|------|-------|---------|
| **Basic (Primitive)** | All resources in project | Owner, Editor, Viewer |
| **Predefined** | Specific service | `roles/storage.objectViewer` |
| **Custom** | You define exactly what's allowed | Bespoke project roles |

> Hierarchy: **Basic → Predefined → Custom**
> Always prefer Predefined or Custom — Basic roles are too broad.

## Service Accounts

- Identity for machines/workloads (not humans)
- Email format: `name@project-id.iam.gserviceaccount.com`
- Grant IAM roles to service accounts just like users
- Restrict the **default service account** — it has broad permissions by default

## Ways to Interact with GCP

| Tool | What it's for |
|------|--------------|
| **Cloud Console** | GUI — SSH in browser, billing, resource management |
| `gcloud` CLI | Main command-line tool |
| `bq` | BigQuery command-line |
| **Cloud Shell** | Browser-based Debian VM · 5 GB persistent home · always authenticated |
| **Client Libraries** | Java, Python, PHP, C#, Go, Node.js, Ruby, C++ |
| **Cloud Mobile App** | Start/stop VMs · manage Cloud SQL · view logs · billing alerts |

---
---

# 4. Compute Engine

## Pricing Discounts

| Discount Type | How it works |
|---------------|-------------|
| **Sustained-use** | Auto-applied when VM runs >25% of a month |
| **Committed-use** | 1 or 3 year commitment → up to 57% off |
| **Preemptible VM** | Up to 90% cheaper · max **24-hour runtime** · GCP can terminate anytime |
| **Spot VM** | Same price as preemptible · **no max runtime** · still terminable |

> **Key difference:** Preemptible = 24 hr max. Spot = no max runtime. Same price.

## VM Storage Options

| Storage Type | Description |
|-------------|-------------|
| **Zonal Persistent Disk** | Default — reliable block storage |
| **Regional Persistent Disk** | Replicated across 2 zones |
| **Local SSD** | High performance but **transient** — data lost when VM stops |
| **Cloud Storage** | Object storage — affordable, not a disk |
| **Filestore** | High-performance NFS file storage |

## Scaling
- Use **Managed Instance Groups (MIGs)** + Autoscaling
- Scale **out** (add more VMs) is preferred over scale **up** (bigger VMs)

---
---

# 5. VPC Networking

## Scope Rules

| Resource | Scope |
|----------|-------|
| **VPC** | **Global** — one VPC can span all regions |
| **Subnets** | **Regional** — but can span zones within a region |
| VMs in different zones | Can share the same subnet ✓ |

## Connecting VPCs

| Method | When to use |
|--------|------------|
| **VPC Peering** | Connect two VPCs to exchange traffic — no IAM control |
| **Shared VPC** | Share a host project's VPC with service projects — full IAM control |

## Load Balancer Types

| Balancer | Layer | Scope | Best for |
|----------|-------|-------|---------|
| Global External Application LB | L7 HTTP/S | Global | Web apps, SSL termination, multi-region failover |
| Regional External Application LB | L7 HTTP/S | Regional | Regional web apps |
| Regional Internal Application LB | L7 HTTP/S | Internal | Internal microservices |
| Network LB — Proxy | L4 | Regional | TCP/SSL proxy |
| Network LB — Passthrough | L4 | Regional | UDP / raw TCP / preserve client IP |

> Global LB = single anycast IP + automatic multi-region failover + **no pre-warming needed**

## Connecting to Google's Network

| Option | SLA? | Notes |
|--------|------|-------|
| **Dedicated Interconnect** | ✅ Yes | Private physical circuit direct to Google |
| **Partner Interconnect** | ✅ Yes | Via service provider |
| **Direct Peering** | ❌ No | Unmanaged BGP |
| **Carrier Peering** | ❌ No | Via ISP |
| **Cloud VPN** | — | Encrypted over public internet |

> Only **Dedicated Interconnect** guarantees an SLA.

## Other Networking Services

| Service | Purpose |
|---------|---------|
| **Cloud DNS** | Authoritative DNS, 100% uptime SLA |
| **Cloud CDN** | Cache at Google POPs — uses global LB |
| **Cloud NAT** | Outbound internet for VMs with no public IP |
| **Firewall rules** | Stateful, distributed — applied via tags or IP ranges |

---
---

# 6. Cloud Storage

## Storage Classes

| Class | Access Pattern | Min Duration | Cost |
|-------|---------------|--------------|------|
| **Standard** | Frequent / hot data | None | Highest storage |
| **Nearline** | ~Once per month | 30 days | Lower storage, retrieval fee |
| **Coldline** | ~Once per 90 days | 90 days | Lower storage, higher retrieval fee |
| **Archive** | Less than once per year | 365 days | Lowest storage, highest retrieval fee |

- **Autoclass** — automatically moves objects to the right class based on access patterns
- All classes share the same API, tools, and security

## Key Features

| Feature | Detail |
|---------|--------|
| **Immutability** | Objects can't be edited — new version created on every change |
| **Versioning** | Keep full history of overwrites/deletes; restore anytime |
| **Lifecycle policies** | Auto-delete or transition objects (e.g., delete after 365 days) |
| **Encryption** | Server-side at rest — free. HTTPS/TLS in transit — always on |
| **Access control** | IAM (preferred) + ACLs for object-level control |
| **Max object size** | 5 TB |

> Cloud Storage = **object storage only** — not a file system, not a database, not a data warehouse.

---
---

# 7. Databases

## Choose the Right Database

| Service | Type | Best for | Max size |
|---------|------|---------|---------|
| **Cloud SQL** | Relational (MySQL, PostgreSQL, SQL Server) | Web apps, OLTP, existing SQL | 64 TB |
| **Cloud Spanner** | Relational + horizontally scalable | Global OLTP, petabyte SQL | Petabytes |
| **Firestore** | NoSQL document | Mobile/web, real-time sync, offline | Terabytes |
| **Bigtable** | NoSQL wide-column | IoT, AdTech, heavy R/W, no joins | Petabytes |
| **BigQuery** | Data warehouse | Analytics, SQL reporting | Petabytes |
| **Cloud Storage** | Object store | Blobs, images, video, backups | Petabytes |

## Quick Selection Rules

```
Need SQL + >64 TB?             → Spanner
Need SQL + <64 TB?             → Cloud SQL
Mobile/web app, real-time?     → Firestore
Heavy analytics, no SQL joins? → Bigtable
Reporting / data warehouse?    → BigQuery
Storing files / blobs?         → Cloud Storage
```

---
---

# 8. Containers & GKE

## Containers vs VMs

| | VM | Container |
|--|----|-----------|
| Virtualizes | Hardware | OS kernel |
| Startup time | Minutes | Seconds / milliseconds |
| OS per unit | Full OS | Shared kernel |
| Portability | Low | High — "code once, run anywhere" |

## Kubernetes Core Concepts

| Concept | What it is |
|---------|-----------|
| **Cluster** | Set of nodes running containers |
| **Node** | A VM (in GKE: a Compute Engine instance) |
| **Pod** | Smallest unit — 1+ containers sharing an IP + storage |
| **Deployment** | Manages a group of identical pods; keeps them running |
| **Service** | Stable IP/DNS for a set of pods (pod IPs change; Services don't) |

## GKE Modes

| Mode | Who manages nodes | Best for |
|------|------------------|---------|
| **Autopilot** | Google | Production — strong security, less ops overhead |
| **Standard** | You | When you need full node control |

> Use **Autopilot** unless you have a specific reason for Standard.

## Essential kubectl Commands

```bash
kubectl get pods                    # list running pods
kubectl get deployments             # list deployments
kubectl get services                # list services
kubectl scale deployment <name>     # scale replicas
kubectl apply -f config.yaml        # apply declarative config
kubectl expose deployment <name>    # create service + LB
```

---
---

# 9. Serverless Compute

## Side-by-Side Comparison

| | Cloud Run | Cloud Run Functions | App Engine Standard | App Engine Flexible |
|--|-----------|--------------------|--------------------|---------------------|
| **Unit** | Container | Single function | App | App in container |
| **Trigger** | HTTP / Pub/Sub | Events / HTTP | HTTP | HTTP |
| **Scale to zero** | ✅ Yes | ✅ Yes | ✅ Yes | ❌ No |
| **Runtime** | Any Linux 64-bit | Node, Python, Go, Java, .NET, Ruby, PHP | Fixed runtimes | Any (Docker) |
| **Billing** | Per 100ms request | Per 100ms execution | Per instance-hour | Per instance-hour |
| **Built on** | Knative | — | Proprietary sandbox | Compute Engine VMs |

## When to Use Which

```
Full containerized web app?                → Cloud Run
Event-triggered, single-purpose function? → Cloud Run Functions
Simple web app, auto-managed runtime?     → App Engine Standard
Custom runtime, no scale-to-zero needed?  → App Engine Flexible
Full OS control, persistent workloads?    → Compute Engine
Container orchestration at scale?         → GKE
```

---
---

# 10. AI & Generative AI

## Key Terms

| Term | Definition |
|------|-----------|
| **Generative AI** | AI that creates text, images, audio, or data from prompts |
| **LLM** | Large Language Model — pre-trained on massive data, fine-tuned for tasks |
| **Parameters** | The "memory" learned during training (billions to trillions) |
| **Hallucination** | Model produces plausible-sounding but incorrect output |
| **Zero-shot** | Prompt with no examples |
| **Few-shot** | Prompt includes a few examples |
| **Chain-of-thought** | Ask the model to reason step-by-step |

## GCP AI Services

| Service | What it does |
|---------|-------------|
| **Vertex AI** | Unified platform — train, deploy, manage ML models |
| **Generative AI Studio** | UI to prototype and tune generative AI models |
| **Model Garden** | Browse and deploy first/third-party models |
| **AutoML** | Train models on custom data without writing code |
| **Pre-trained APIs** | Vision, Speech, NLP, Translation — no training needed |
| **Gemini in Cloud Console** | AI assistant built into the console |

## Prompt Engineering Best Practices

| Practice | Example |
|----------|---------|
| **Be explicit** | "Summarize in 3 bullet points" not "summarize this" |
| **Define boundaries** | Tell it what to do; provide a fallback for unknowns |
| **Assign a persona** | "You are a GCP cloud architect..." |
| **Keep sentences short** | Break complex instructions into separate sentences |
| **Specify output format** | "Return as a JSON array" / "Use a markdown table" |

---
---

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
| **Cloud Trace** | Distributed tracing — find latency bottlenecks |
| **Cloud Profiler** | Continuous CPU/memory profiling in production |
| **Error Reporting** | Auto-groups errors and sends alerts |

## Cloud Monitoring Facts

- **1,500+ metrics** from 100+ GCP services — ingested **automatically**, free
- **Compute Engine**: 25+ unique metrics per VM instance
- **Ops Agent** — install on VMs to collect process and third-party app metrics/logs
- **Google Managed Prometheus (GMP)** — built-in Prometheus support for GKE
- **Uptime checks** — verify endpoint reachability from multiple global locations

---
---

# 12. Cloud Logging

## How Logs Flow

```
Log Sources
(GCP services · GKE · Compute Engine via Ops Agent · your apps)
        ↓
    Log Router  ←  inclusion / exclusion filters
        ↓
   Log Sinks
   ├── Cloud Logging Buckets  (default storage)
   ├── BigQuery               (analytics)
   ├── Pub/Sub                (streaming to third parties)
   └── Cloud Storage          (long-term archival)
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
| **Log Explorer** | Troubleshooting UI — stream logs, histogram, filter |
| **Log Analytics** | BigQuery-powered SQL queries on log data |

> **Log Analytics bucket upgrade is permanent** — you cannot downgrade once enabled.

## Log Retention Defaults

| Bucket | Retention | Cost |
|--------|-----------|------|
| `_Required` (Admin Activity + System Event) | **400 days** | Free — cannot delete |
| `_Default` | **30 days** | Included |
| Custom buckets | Configurable | Per-GB |

---
---

# 13. Alerting & SLOs

## Alert Policy Components

- **Condition** — what triggers it (threshold / absence / rate of change / uptime failure)
- **Notification channels** — email, SMS, PagerDuty, Slack, Pub/Sub, webhook
- **Documentation** — runbook link or description shown in the alert

## SLI / SLO / SLA / Error Budget

| Term | What it is | Who sets it |
|------|-----------|------------|
| **SLI** | Metric measuring one aspect of reliability = `good events ÷ all events` | Engineering |
| **SLO** | SLI + target (e.g., 99.9%) — **internal** reliability goal | Product + Engineering |
| **SLA** | Contractual promise to customers — breach = compensation | Business / Legal |
| **Error Budget** | `100% - SLO` — how much failure is acceptable | Shared |

> **SLO must be stricter than SLA.** Alert thresholds should be stricter than SLO.

## SLO Must Be S.M.A.R.T.

**Specific** · **Measurable** · **Achievable** · **Relevant** · **Time-bound**

> "100% availability" is never a valid SLO — nothing achieves 100%.

## SLO Calculation Types

| Type | Formula | Risk |
|------|---------|------|
| **Request-based** | good requests ÷ total requests | More precise |
| **Windows-based** | good windows ÷ total windows | Can **hide burst failures** |

## Error Budget in Action

```
SLO = 99.9%  →  Error Budget = 0.1%  →  ~43 minutes downtime/month

Budget healthy  →  ship features fast
Budget exhausted  →  slow releases, focus on reliability
```

## Burn Rate Alerts
- Alert fires when error budget is being **consumed faster than planned**
- Uses a lookback window (e.g., 60 min) + a burn rate threshold multiplier

---
---

# 14. Audit Logs

## The Four Audit Log Types

| Log Type | Records | Always On? | Retention | Cost |
|----------|---------|-----------|-----------|------|
| **Admin Activity** | Config/metadata changes (create VM, change IAM) | ✅ Always | 400 days | Free |
| **System Event** | Google-initiated changes (non-human admin actions) | ✅ Always | 400 days | Free |
| **Data Access** | Reads/writes to user data and configs | ❌ Off by default (except BigQuery) | 30 days | Charged |
| **Policy Denied** | Access denied by a security policy | ✅ Always | 30 days | Charged |

> To view **Admin Activity / System Event** logs: requires `Logging/Logs Viewer` or `Project/Viewer`
> To view **Data Access** logs: requires `Logging/Private Logs Viewer` or `Project/Owner`

## Access Transparency Logs
- Records actions by **Google employees** on your data
- *Different* from Audit Logs, which record **your organization's** actions
- Requires an appropriate support package

---
---

# 15. SRE Practices

## DevOps vs SRE

| | DevOps | SRE |
|--|--------|-----|
| What it is | Philosophy / culture | Concrete implementation of DevOps |
| Created by | Industry | Google (Benjamin Treynor Sloss, 2003) |
| Key output | Cultural practices | Error budgets, SLOs, toil reduction |

## Five DevOps Pillars → SRE Practices

| Pillar | SRE Implementation |
|--------|--------------------|
| Reduce organizational silos | Shared SLOs + error budgets; shared production ownership |
| Accept failure as normal | **Blameless postmortems**; psychological safety |
| Implement gradual change | **CI/CD** + **canarying** |
| Leverage tooling & automation | Eliminate **toil**; automate everything automatable |
| Measure everything | SLIs, SLOs, error budgets, OKRs, toil tracking |

## Blameless Postmortem

A written record of an incident containing:
1. Incident details + timeline
2. Actions taken to mitigate/resolve
3. Impact assessment
4. Trigger + all root causes (use the **5 Whys** technique)
5. Follow-up actions to prevent recurrence

> **Blameless** = focuses on systemic causes, not individual blame

## What is Toil?

Toil is work that is **ALL of these**:
- Manual
- Repetitive
- Automatable
- Tactical (not strategic)
- Has no enduring value
- Scales linearly with service growth

> SRE rule: keep toil **below 50%** of an SRE's time. The rest is engineering work.

## Canarying

Deploy a change to a **small, representative subset** of users first:
1. Canary group must be large enough to be representative
2. Small enough that if it breaks, the whole service isn't harmed
3. Simple enough to monitor and roll back quickly

## CI/CD

| | Definition |
|--|-----------|
| **CI** (Continuous Integration) | Build, integrate, and test code in dev environment continuously |
| **CD** (Continuous Delivery) | Deploy to production frequently, at the cadence the business chooses |

## OKRs
- Google's goal-setting framework: **Objectives + Key Results**
- Graded **0.0 to 1.0** — optimal score is **60–70%** (stretch goals, not performance reviews)
- Graded publicly; cross-functional teams share OKRs

---
---

# 16. Terraform on GCP

## The Terraform Workflow

```
1. Scope      — identify what resources are needed
2. Author     — write .tf configuration files
3. Initialize — terraform init  (downloads provider plugins)
4. Plan       — terraform plan  (preview changes — no modifications made)
5. Validate   — gcloud beta terraform vet  (optional policy check)
6. Apply      — terraform apply  (create / update infrastructure)
7. Destroy    — terraform destroy  (tear everything down)
```

## HCL Syntax Basics

```hcl
resource "google_compute_network" "my_vpc" {
  name                    = "my-network"
  auto_create_subnetworks = false
}

# Reference another resource's attribute:
# google_compute_network.my_vpc.id
```

## Key Constructs

| Block | Purpose |
|-------|---------|
| `resource` | Declare an infrastructure object |
| `variable` | Input — parameterize the config |
| `output` | Export a resource attribute after apply |
| `module` | Reusable group of resources |
| `data` | Read an existing resource NOT managed by this config |
| `depends_on` | Declare explicit dependency |

## Dependencies

| Type | How it works |
|------|-------------|
| **Implicit** | Terraform detects it automatically when resource A references resource B's attribute |
| **Explicit** | You declare it with `depends_on` when the relationship isn't visible in the config |

## Modules

- A **module** = any directory containing `.tf` files
- **Root module** = where you run `terraform plan` / `apply`
- **Child modules** = reusable components called from root
- Implements the **DRY** (Don't Repeat Yourself) principle
- Pin versions with the `version` argument in module blocks
- **Cloud Foundation Toolkit (CFT)** — Google's published GCP modules on the Terraform Registry

## Terraform State

| | Local State | Remote State (GCS bucket) |
|-|-------------|--------------------------|
| Good for | Solo developer | Teams |
| Locking | ❌ None | ✅ Native locking |
| Security | Sensitive data in plaintext | Encrypted + IAM controlled |
| Sharing | ❌ Manual | ✅ Automatic |

## State Best Practices

- Always use **remote state** for teams — store in a Cloud Storage bucket
- **Never store secrets** in state files (they're stored in plaintext)
- **Never edit** the state file manually — use `terraform state` commands
- Add `terraform.tfstate*` to `.gitignore`
- Restrict state bucket access to build systems + privileged admins only

## Terraform Validator

| Command | Purpose |
|---------|---------|
| `gcloud beta terraform vet` | Check plan against org **policy constraints** before apply |
| `terraform validate` | Check **syntax only** — does not enforce policies |

---
---

# 17. Infrastructure Design

## Requirements → Design Flow

```
Qualitative → User Stories ("As a [role], I want to... so that...")
Quantitative → KPIs + SLOs/SLIs (must be S.M.A.R.T.)
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

**Decompose by:** Feature area · Architectural layer · Shared functionality (auth, etc.)

## Reliability Patterns

### Avoid Single Points of Failure
- Deploy **N+2** instances (N for load + 1 for upgrades + 1 for failures)
- Spread across **multiple zones** to avoid correlated failures
- Make units **stateless and interchangeable**

### Failure Domains
A group of related items that can fail together:
`machine → rack → zone → region → software version → shared config`

Mitigation: distribute across failure domains; use separate zones/regions

### Cascading Failures
One failure overloads other services, causing them to fail in turn.
Prevention: **circuit breakers**, load shedding, autoscaling, health checks

### Circuit Breaker Pattern
```
Client → [Circuit Breaker Proxy] → Service

If service is unhealthy:
  Circuit breaker OPENS → stops forwarding requests
  Service recovers → circuit breaker CLOSES → traffic resumes gradually
```
> In GKE: **Istio** implements circuit breakers automatically

### Truncated Exponential Backoff
Retry on failure, but wait longer each time + add random jitter:
```
Fail → wait 1s + jitter → retry
Fail → wait 2s + jitter → retry
Fail → wait 4s + jitter → retry
...up to a max limit, then give up
```
Prevents thundering herd / overloading a recovering service

### Lazy Deletion
Mark data as deleted → keep it for a grace period → then purge permanently.
Enables recovery if a user accidentally deletes data.

## Disaster Recovery

| Term | Definition |
|------|-----------|
| **RPO** — Recovery Point Objective | Maximum data loss you can tolerate (e.g., "1 hour of data") |
| **RTO** — Recovery Time Objective | Maximum acceptable downtime (e.g., "5 minutes") |

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
| Firestore | Daily automated backups → multi-regional Cloud Storage |
| Cloud SQL | Binary logging + failover replica in another zone |
| BigQuery | Re-import from source (if source is retained) |

---
---

# 18. Security Design

## Shared Responsibility Model

| Google secures | You secure |
|---------------|-----------|
| Hardware · Physical infrastructure | IAM · Firewall rules |
| Hypervisor · Network fabric | Data encryption choices |
| Verified boot · DDoS at network edge | Application code · Access controls |

## Core Security Principles

| Principle | What it means |
|-----------|--------------|
| **Least Privilege** | Grant only what's needed — applies to users, service accounts, and processes |
| **Separation of Duties** | No single person can create + delete + audit; developers ≠ deployers |
| **Defense in Depth** | Multiple independent security layers — never rely on just one |
| **Regular Audits** | Use Audit Logs to detect attacks and compliance violations |

## Security Tools Reference

| Tool | What it does |
|------|-------------|
| **IAM** | Auth + authz for humans and service accounts |
| **Identity-Aware Proxy (IAP)** | App access control without a VPN — verifies identity at the application layer |
| **Security Command Center** | Centralized vulnerability scanning and threat detection |
| **Cloud Armor** | DDoS protection + WAF for external load balancers |
| **VPC Service Controls** | Security perimeter around GCP APIs — prevents data exfiltration |
| **Organization Policies** | Governance guardrails at org / folder / project level |
| **Private Google Access** | Lets VMs with no public IP reach GCP APIs |

---
---

# 19. Gemini Cloud Assist

## Capabilities

| Mode | What it does |
|------|-------------|
| **Design & Build** | Generate `gcloud` commands and Terraform code from natural language |
| **Diagnose & Resolve** | Root-cause analysis via **Cloud Assist Investigations** |
| **Optimize Cost & Usage** | FinOps Hub · database cost recommendations |

## Cloud Assist Investigations

- AI-powered **root-cause analysis (RCA)** tool
- Primary metric it reduces: **MTTR** (Mean Time to Recovery)
- Trigger from: Logs Explorer · GKE Health page · Cloud Monitoring alerts · Gemini chat panel
- Flow: `Issue → Observations → Hypotheses → Root Cause → Recommended Fix → Support case`

---
---

# 20. Quick Decision Trees

## Compute

```
Full OS control / persistent workload?   →  Compute Engine
Container orchestration at scale?        →  GKE
Serverless container (HTTP/Pub/Sub)?     →  Cloud Run
Event-driven single function?            →  Cloud Run Functions
Simple web app, managed runtime?         →  App Engine Standard
Custom runtime, no scale-to-zero?        →  App Engine Flexible
```

## Storage

```
Blobs, images, video, backups?           →  Cloud Storage
SQL, up to 64 TB?                        →  Cloud SQL
SQL, global scale, petabytes?            →  Cloud Spanner
Mobile/web app, real-time NoSQL?         →  Firestore
IoT / AdTech / heavy reads + writes?     →  Bigtable
SQL analytics / data warehouse?          →  BigQuery
```

## Connecting to Google's Network

```
Need SLA + dedicated private circuit?    →  Dedicated Interconnect
Via ISP / lower bandwidth + SLA?         →  Partner Interconnect
BGP peering, no SLA required?            →  Direct / Carrier Peering
Encrypted tunnel over public internet?   →  Cloud VPN
```

---
---

# 21. Exam Traps to Avoid

| # | Trap | Remember |
|---|------|---------|
| 1 | VPC scope | VPC = **global**. Subnets = **regional** |
| 2 | Preemptible vs Spot | Preemptible = **24 hr max**. Spot = **no max**. Same price |
| 3 | APIs per project | Enabled/disabled **per project**, not per folder or org |
| 4 | IAM role order | Basic → Predefined → **Custom** (broadest to finest) |
| 5 | Project ID | Immutable **after** creation, but changeable **during** creation |
| 6 | Cloud Storage type | Object storage — NOT a file system, NOT a database |
| 7 | Archive cost | Lowest storage cost, but **highest retrieval cost** |
| 8 | Data Access logs | **Off by default** (except BigQuery). Admin Activity = always on |
| 9 | Interconnect SLA | Only **Dedicated Interconnect** has an SLA |
| 10 | GKE nodes | GKE nodes are **Compute Engine VMs** |
| 11 | GKE Autopilot | Autopilot = Google manages nodes. Standard = you manage nodes |
| 12 | Scale-to-zero | Cloud Run ✅. App Engine Standard ✅. App Engine Flexible ❌ |
| 13 | SLO vs SLA | SLO = internal. SLA = contractual. SLO should be **stricter** |
| 14 | Windows SLOs | Can **hide burst failures** — request-based is more precise |
| 15 | Log Analytics upgrade | **Permanent** — cannot be undone |
| 16 | Policy Denied logs | Cannot be disabled (but can be excluded from storage) |
| 17 | Cloud Shell disk | Persistent **5 GB** home directory |
| 18 | Terraform state secrets | State stores secrets in **plaintext** — never put secrets in config |
| 19 | `terraform validate` vs `vet` | `validate` = syntax check only. `vet` = policy compliance |
| 20 | SLO must be SMART | "100% availability" is **never** a valid SLO |

---
---

# 22. Practice Questions

## Cloud Fundamentals & IAM (Q1–10)

**Q1.** Which NIST cloud trait means you can provision resources without human assistance?
> **On-demand self-service**

**Q2.** In the GCP resource hierarchy, policies are inherited in which direction?
> **Downward** — parent policies apply to all children

**Q3.** Which project attribute is globally unique, assigned by Google, but can be changed *during* creation only?
> **Project ID**

**Q4.** Services and APIs are enabled on a per-________ basis.
> **Project**

**Q5.** What is the correct order of IAM roles from broadest to finest-grained?
> **Basic → Predefined → Custom**

**Q6.** A company wants each department to manage its own GCP resources independently. What hierarchy feature enables this?
> **Folders**

**Q7.** A non-Google-Workspace company needs to create an Organization node. What do they use?
> **Cloud Identity**

**Q8.** What is the main reason to choose a Preemptible VM?
> **Reduce cost** — up to 90% cheaper

**Q9.** What distinguishes a Spot VM from a Preemptible VM?
> **Spot VMs have no maximum runtime** (Preemptible = 24 hr max)

**Q10.** You need block storage replicated across two zones. Which option?
> **Regional Persistent Disk**

---

## Networking & Storage (Q11–22)

**Q11.** What is the scope of a VPC network?
> **Global**

**Q12.** What is the scope of a VPC subnet?
> **Regional** (spans all zones within a region)

**Q13.** Which load balancer provides a single global IP with automatic multi-region failover for HTTPS?
> **Global External Application Load Balancer**

**Q14.** Which interconnect option has an SLA?
> **Dedicated Interconnect**

**Q15.** How do private VMs access the internet without a public IP?
> **Cloud NAT**

**Q16.** Which Cloud Storage class is best for data accessed less than once a year?
> **Archive**

**Q17.** Which Cloud Storage class is best for data accessed about once a month?
> **Nearline**

**Q18.** What does enabling object versioning allow?
> Keep a history of all overwrites/deletes and **restore objects to earlier versions**

**Q19.** What type of storage is Cloud Storage?
> **Object storage** — not file storage, not block storage, not a database

**Q20.** Which database supports horizontal SQL scaling to petabytes?
> **Cloud Spanner**

**Q21.** Which database is best for IoT data with heavy read/write events and no SQL join requirements?
> **Bigtable**

**Q22.** Which database is best for a mobile app needing real-time sync and offline support?
> **Firestore**

---

## Containers, Serverless & AI (Q23–35)

**Q23.** What is a Kubernetes Pod?
> One or more containers deployed together, sharing a network IP and storage volume

**Q24.** Where do GKE cluster nodes come from?
> **Compute Engine VMs**

**Q25.** In GKE, which mode has Google manage the node infrastructure?
> **Autopilot**

**Q26.** Why use a Kubernetes Service instead of Pod IPs directly?
> Services provide a **stable IP and DNS name** — Pod IPs change; Services don't

**Q27.** What open-source platform is Cloud Run built on?
> **Knative**

**Q28.** What is Cloud Run's billing granularity?
> **Nearest 100 milliseconds** of active request handling

**Q29.** Which scenario fits Cloud Run better than Cloud Run Functions?
> **A full web application** with ongoing HTTP traffic — Functions = single event-triggered tasks

**Q30.** How does App Engine Standard differ from Flexible?
> Standard can **scale to zero** — Flexible runs on Compute Engine VMs and cannot

**Q31.** What is a hallucination in a generative AI model?
> The model produces **plausible-sounding but incorrect or fabricated output**

**Q32.** What is the best practice when a prompt model doesn't know the answer?
> Include a **fallback response** in the prompt (e.g., "If unsure, say 'I don't have that information'")

**Q33.** What are the three prompt engineering techniques involving examples?
> **Zero-shot** (no examples) · **Few-shot** (a few examples) · **Chain-of-thought** (reason step-by-step)

**Q34.** Gemini Cloud Assist "Diagnose & Resolve" uses which tool?
> **Cloud Assist Investigations**

**Q35.** What metric does Cloud Assist Investigations primarily help reduce?
> **MTTR — Mean Time to Recovery**

---

## Monitoring, Logging & Alerts (Q36–50)

**Q36.** What are the four golden signals?
> **Latency · Traffic · Saturation · Errors**

**Q37.** Which Cloud Monitoring feature verifies endpoint reachability from multiple global locations?
> **Uptime checks**

**Q38.** What does the Ops Agent do?
> Collects **process and third-party application metrics and logs** from Compute Engine VMs

**Q39.** What is a log sink?
> A **destination for routed logs** — Logging bucket, BigQuery, Pub/Sub, or Cloud Storage

**Q40.** You want to count a specific error in logs and trigger an alert. What do you create first?
> A **log-based metric** → then an alerting policy on that metric

**Q41.** Upgrading a Cloud Logging bucket to use Log Analytics is:
> **Permanent — it cannot be undone**

**Q42.** Which audit log records "user creates a VM" or "user changes IAM policy"?
> **Admin Activity audit log**

**Q43.** Data Access audit logs are enabled by default only for which service?
> **BigQuery**

**Q44.** Which audit log records Google-initiated (non-human) changes?
> **System Event audit log**

**Q45.** Which audit log records a security policy blocking access?
> **Policy Denied audit log**

**Q46.** How long are Admin Activity audit logs retained?
> **400 days** — free of charge

**Q47.** SLO = 99.9% over 30 days. What is the error budget in minutes?
> **~43 minutes** per month

**Q48.** When should a team slow feature releases according to SRE principles?
> When the **error budget is exhausted**

**Q49.** What kind of failure can a windows-based SLO hide?
> **Burst failures** that occur and recover within a single measurement window

**Q50.** You want to query log data with SQL and join it to other BigQuery tables. What do you enable?
> **Log Analytics** on the Cloud Logging bucket (creates a BigQuery read-only view)

---

## SRE, Terraform & Infrastructure (Q51–70)

**Q51.** What is the key difference between SRE and DevOps?
> DevOps is a **philosophy**. SRE is a **concrete implementation** of that philosophy.

**Q52.** What makes a postmortem "blameless"?
> It focuses on **systemic root causes** — never on individual blame

**Q53.** Name three characteristics that define toil.
> **Manual · Repetitive · Automatable** (also: tactical, no enduring value, scales linearly)

**Q54.** What does `terraform init` do?
> **Downloads provider plugins** and initializes the working directory

**Q55.** Which Terraform command previews changes without making them?
> `terraform plan`

**Q56.** What is an implicit dependency in Terraform?
> When resource A references resource B's attribute — **Terraform auto-detects the ordering**

**Q57.** Why store Terraform state remotely for teams?
> Remote state provides **locking**, **shared access**, and **encryption**

**Q58.** What does `gcloud beta terraform vet` do?
> Validates a Terraform plan against **organization policy constraints** before apply

**Q59.** What is the DRY principle in Terraform?
> "Don't Repeat Yourself" — extract reusable code into **modules**, call with different variables per environment

**Q60.** What is RPO?
> **Recovery Point Objective** — maximum acceptable data loss (e.g., "1 hour of data")

**Q61.** What is RTO?
> **Recovery Time Objective** — maximum acceptable time to restore the service

**Q62.** Which pattern stops retries from overloading a struggling service?
> **Circuit breaker** — proxy stops forwarding requests until the service recovers

**Q63.** What is truncated exponential backoff?
> Retry with **progressively longer waits + random jitter**, up to a maximum limit

**Q64.** What does N+2 deployment mean?
> Deploy N instances for load **+ 1 for upgrades + 1 for failures** to stay available during maintenance

**Q65.** Which GCP service provides DDoS protection?
> **Cloud Armor**

**Q66.** What does Identity-Aware Proxy (IAP) do?
> Controls app access by **verifying user identity without requiring a VPN**

**Q67.** What creates a security perimeter around GCP APIs to prevent data exfiltration?
> **VPC Service Controls**

**Q68.** What is canarying?
> Deploying a change to a **small representative subset of users**, evaluating it, then rolling out or rolling back

**Q69.** What is the root module in Terraform?
> The **working directory from which you run Terraform commands** — where `.tf` files are loaded

**Q70.** What is the Cloud Foundation Toolkit (CFT)?
> Google's collection of **pre-built Terraform modules** for GCP, published to the Terraform Registry

---

*Sources: GCP Fundamentals (M1–M8) · Cloud Operations Suite (M1–M5) · SRE Foundations (M1–M6) · Terraform for GCP (M1–M5) · Infrastructure Design & Architecture (M1–M9) · Gemini Cloud Assist*
