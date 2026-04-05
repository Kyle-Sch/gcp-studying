# GCP Certification Study Guide
> Exam-focused — 50 questions | Google Cloud Fundamentals + Operations + SRE + Terraform + Infrastructure Design

---

## 1. Cloud Computing Fundamentals

### Five Traits of Cloud Computing (NIST)
1. On-demand self-service — no human intervention needed
2. Broad network access — access via internet from anywhere
3. Resource pooling — provider shares resources across users
4. Rapid elasticity — scale up/down quickly
5. Measured service — pay only for what you use

### Service Models
| Model | What you manage | GCP Example |
|-------|----------------|-------------|
| IaaS | OS, runtime, app | Compute Engine |
| PaaS | App + data only | App Engine, Cloud Run |
| SaaS | Nothing | Google Workspace |

### Cloud History
- Wave 1: Colocation (rent physical space)
- Wave 2: Virtualized data centers (VMs, user-managed)
- Wave 3: Container-based (fully automated, elastic) — GCP

---

## 2. GCP Resource Hierarchy

```
Organization Node
  └── Folders (optional, nestable)
       └── Projects   ← billing unit, API enablement
            └── Resources (VMs, buckets, DBs…)
```

### Key Rules
- **Policies inherit downward** — org policy applies to all children
- A more permissive parent policy **overrides** a more restrictive child policy
- Resources belong to **exactly one project**
- APIs are enabled/disabled **per project**

### Project Identifiers
| Attribute | Unique? | Who sets it? | Mutable? |
|-----------|---------|--------------|----------|
| Project ID | Globally unique | Google (can change during creation) | Immutable after creation |
| Project name | Not unique | User | Mutable |
| Project number | Globally unique | Google | Immutable |

### Organization Node
- Required to use folders
- Created automatically if you are a **Google Workspace** customer
- Otherwise: use **Cloud Identity** to create one
- Special roles: `Organization Policy Administrator`, `Project Creator`

### IAM Policy Application Levels
- Organization → Folder → Project → Resource (some services)

---

## 3. Identity and Access Management (IAM)

### IAM Policy Structure: Who Can Do What on Which Resource
- **Principal (Who):** Google Account, Google Group, Service Account, Cloud Identity domain
- **Role (Can Do What):** collection of permissions
- **Resource (Which):** the target resource

### Role Types (broadest → finest)
| Type | Description | Example |
|------|-------------|---------|
| **Basic (Primitive)** | Affect ALL resources in project | Owner, Editor, Viewer |
| **Predefined** | Specific to a service | `roles/storage.objectViewer` |
| **Custom** | User-defined, fine-grained | Project-specific permissions |

- Basic roles: **Owner > Editor > Viewer** (Owner includes billing admin)
- **Use predefined or custom over basic** — least privilege principle

### Service Accounts
- Act as an identity for workloads/apps (not a human)
- Identified by email: `sa-name@project-id.iam.gserviceaccount.com`
- Can be granted IAM roles
- Applications running on GCE can use the instance's service account
- **Default service account** created per project for some services — restrict carefully

### Cloud Identity
- Manages users and groups without Google Workspace
- Lets non-Workspace customers create an org node

### Interacting with GCP
| Tool | Use |
|------|-----|
| Cloud Console | GUI, SSH in browser, billing alerts |
| `gcloud` CLI | Main command-line for GCP |
| `bq` | BigQuery command-line |
| Cloud Shell | Browser-based Debian VM with persistent 5 GB home, always authenticated |
| APIs / Cloud Client Libraries | Programmatic control; supports Java, Python, PHP, C#, Go, Node.js, Ruby, C++ |
| Cloud Mobile App | Start/stop VMs, manage Cloud SQL, view logs, billing |

---

## 4. Compute Engine (IaaS)

### Key Facts
- Create VMs via Console, gcloud CLI, or Compute Engine API
- Run **Linux or Windows Server** images (custom images supported)
- Billed by the **second** with 1-minute minimum

### Machine Types
- **Predefined** machine types (general purpose, compute-optimized, memory-optimized)
- **Custom machine types** — specify vCPU + memory; pay for exactly what you need

### Pricing Discounts
| Type | Details |
|------|---------|
| Sustained-use | Auto-applied for VMs running >25% of a month; per additional minute |
| Committed-use | 1 or 3 year commitment; up to 57% discount on vCPU + memory |
| Preemptible VMs | Up to 90% cheaper; max 24-hour runtime; can be terminated anytime |
| Spot VMs | Same price as preemptible; **no max runtime**; still terminable |

> **Exam tip:** Preemptible = max 24 hrs; Spot = no max runtime. Both terminable by GCP.

### Storage Options for VMs
| Type | Description |
|------|-------------|
| Zonal Persistent Disk | Default; reliable block storage |
| Regional Persistent Disk | Replicated across 2 zones |
| Local SSD | High performance, **transient** (data lost on VM stop) |
| Cloud Storage buckets | Object storage, affordable |
| Filestore | High-performance NFS file storage |

### Scaling
- **Managed Instance Groups (MIGs)** + **Autoscaling** — add/remove VMs based on CPU, HTTP load, custom metrics
- Scale **out** (more VMs) preferred over scale **up** (larger VMs) for most workloads

---

## 5. VPC Networking

### VPC Fundamentals
- VPC = **global** resource (spans all regions)
- **Subnets are regional** — can span zones within a region
- Resources in different zones can share the same subnet
- Two modes: **Auto mode** (subnets auto-created per region) / **Custom mode** (you control)

### VPC Communication
| Method | Use Case |
|--------|----------|
| **VPC Peering** | Connect two VPCs, exchange traffic; no IAM control over cross-project access |
| **Shared VPC** | One host project's VPC shared with service projects; uses full IAM control |

### Firewall Rules
- **Stateful**, distributed — no separate firewall VM to manage
- Rules defined by: direction (ingress/egress), protocol/port, source/destination, priority
- Can apply via **network tags** on VM instances (e.g., tag `WEB` → allow port 80/443)
- Default: deny all ingress, allow all egress

### Load Balancing

| Type | Layer | Scope | Use Case |
|------|-------|-------|----------|
| Global External Application LB | L7 (HTTP/S) | Global | Web apps, content routing, SSL termination |
| Regional External Application LB | L7 (HTTP/S) | Regional | Regional web apps |
| Regional Internal Application LB | L7 (HTTP/S) | Internal | Internal microservices |
| Network LB — Proxy (external/internal) | L4 | Regional | TCP/SSL proxy |
| Network LB — Passthrough (external/internal) | L4 | Regional | UDP, raw TCP, preserve client IP |

- Global LB = single front-end IP, routes to nearest healthy backend, **no pre-warming needed**
- SLA available for **Dedicated Interconnect** (not Direct/Carrier Peering)

### DNS, CDN, NAT, Interconnect
| Service | Purpose |
|---------|---------|
| **Cloud DNS** | Authoritative DNS, 100% uptime SLA |
| **Cloud CDN** | Cache content at Google POPs; uses global LB |
| **Cloud NAT** | Outbound internet for private VMs (no public IP needed) |
| **Dedicated Interconnect** | Private physical connection to Google; has SLA |
| **Partner Interconnect** | Via service provider; lower bandwidth option |
| **Direct Peering** | Unmanaged BGP peering; **no SLA** |
| **Carrier Peering** | Via ISP; **no SLA** |
| **Cloud VPN** | IPsec VPN over public internet |

> **Exam tip:** Only Dedicated Interconnect comes with an SLA.

---

## 6. Cloud Storage (Object Storage)

### Storage Classes
| Class | Access Frequency | Min Storage Duration |
|-------|-----------------|---------------------|
| **Standard** | Frequent (hot data) | None |
| **Nearline** | ≤1x/month | 30 days |
| **Coldline** | ≤1x/90 days | 90 days |
| **Archive** | <1x/year | 365 days |

- All classes: unlimited storage, no min object size, geo-redundancy in multi/dual-region, same API/tools
- **Autoclass**: auto-transitions objects to appropriate class based on access pattern

### Key Features
- Objects are **immutable** — create new version on every change
- **Versioning**: keep history; restore or delete old versions
- **Lifecycle policies**: delete objects older than N days, keep N recent versions, transition classes
- **Encryption**: server-side at rest (free), HTTPS/TLS in transit
- Access control: **IAM** (preferred) + **ACLs** for finer-grained object control
- Max object size: **5 TB**
- Data import: `gcloud storage`, Storage Transfer Service (large online), Transfer Appliance (physical, petabytes)

> **Exam tip:** Cloud Storage ≠ file system, ≠ data warehouse (BigQuery), ≠ RDBMS (Cloud SQL/Spanner).

---

## 7. Databases — When to Use What

| Service | Type | Best For | Capacity |
|---------|------|----------|----------|
| **Cloud Storage** | Object | Immutable blobs >10 MB | Petabytes; 5 TB/object |
| **Cloud SQL** | Relational (MySQL, PostgreSQL, SQL Server) | Web apps, OLTP, existing SQL apps | Up to 64 TB |
| **Cloud Spanner** | Relational + horizontal scale | Global OLTP, horizontal scalability | Petabytes |
| **Firestore** | NoSQL document | Mobile/web apps, real-time sync, offline | Terabytes; 1 MB/entity |
| **Bigtable** | NoSQL wide-column | IoT, AdTech, financial analytics, heavy R/W | Petabytes; 10 MB/cell, 100 MB/row |
| **BigQuery** | Data warehouse (analytical) | SQL analytics, reporting, data warehousing | Petabytes |

> **Key comparisons:**
> - Need SQL + scale beyond 64 TB → **Spanner**
> - Real-time mobile/web sync → **Firestore**
> - High-throughput analytics, no SQL joins → **Bigtable**
> - Object blobs → **Cloud Storage**
> - Analytics/reporting → **BigQuery**

---

## 8. Containers & GKE

### Containers vs VMs
| | VM | Container |
|--|----|----|
| Virtualizes | Hardware | OS |
| Startup | Minutes | Seconds/milliseconds |
| OS | Full OS per VM | Shared kernel |
| Portability | Limited | High ("code once, run anywhere") |

### Kubernetes Core Concepts
- **Cluster**: set of nodes (compute instances)
- **Node**: a VM (Compute Engine instance in GKE)
- **Pod**: smallest deployable unit; 1+ containers; shared network IP + storage
- **Deployment**: manages a group of identical pods; keeps them running
- **Service**: stable IP/DNS for a set of pods (pods IPs change; Services are stable)
- **ConfigMap / Secret**: inject config and sensitive data into pods

### Key kubectl Commands
```bash
kubectl get pods           # list pods
kubectl get deployments    # list deployments
kubectl get services       # list services
kubectl scale              # scale a deployment
kubectl apply -f file.yaml # declarative config apply
kubectl expose deployment  # create a service + load balancer
```

### GKE Modes
| Mode | Who manages nodes? | Best for |
|------|-------------------|----------|
| **Autopilot** | Google (fully managed) | Production; strong security; less ops overhead |
| **Standard** | You | Full configuration control |

> Default recommendation: **use Autopilot** unless you need specific node control.

### GKE Features
- Automatic node upgrades, auto-repair, autoscaling (cluster + pods via HPA)
- Integration with Cloud Monitoring, Cloud Logging
- Load balancing via Compute Engine network load balancer
- Node pools — designate subsets for different workloads

---

## 9. Serverless Compute

### Cloud Run
- **Fully managed** serverless containers; built on **Knative**
- Stateless containers; handles HTTP requests or Pub/Sub events
- **Scale to zero** — no charge when not handling requests
- Charged to nearest **100ms** of request handling time
- Max: 4 vCPUs, 8 GB memory per container instance
- Two workflows: container-based or **source-based** (Buildpacks)
- Can run any Linux 64-bit binary (Go, Python, Java, Node.js, PHP, Ruby, C++, Cobol, etc.)

### Cloud Run Functions (formerly Cloud Functions)
- **Lightweight, event-driven** single-purpose functions
- Triggers: Cloud Storage events, Pub/Sub messages, HTTP calls
- Async (Storage/Pub/Sub) or sync (HTTP)
- Languages: Node.js, Python, Go, Java, .NET Core, Ruby, PHP
- Billed to nearest **100ms** while code runs

### App Engine
- PaaS for web apps; **Standard** (sandboxed, fast spin-up, specific runtimes) vs **Flexible** (Docker container, any runtime, slower spin-up)
- **Standard** = scale to zero; **Flexible** = no scale to zero, runs in Compute Engine VMs

### When to Use Which
| Scenario | Service |
|----------|---------|
| Long-running, stateful web app | Cloud Run or App Engine Flexible |
| Event-triggered, short single function | Cloud Run Functions |
| Existing containerized app | Cloud Run |
| Need full OS / custom runtime | Compute Engine |
| Container orchestration at scale | GKE |
| Simple web app, managed platform | App Engine Standard |

> **Exam tip:** Cloud Run = for containers + web requests. Cloud Run Functions = event-driven, single-purpose. App Engine Standard = scale-to-zero PaaS with specific runtime versions.

---

## 10. AI & Generative AI on GCP

### Generative AI Basics
- **GenAI**: AI that generates text, images, audio, or other data from prompts
- **LLM**: Large Language Model — pre-trained on massive datasets, fine-tuned for specific tasks
- **Parameters**: memories/knowledge learned during training (billions to trillions)
- **Hallucination**: model generating incorrect/nonsensical content (doesn't know what it doesn't know)

### Causes of Hallucinations
- Insufficient training data
- Noisy/dirty training data
- Insufficient context in prompt
- Insufficient constraints

### GCP AI Services
| Service | Description |
|---------|-------------|
| **Vertex AI** | Unified ML platform: train, deploy, manage models |
| **Generative AI Studio** | UI to prototype and tune generative AI models |
| **Model Garden** | Discover and deploy first/third-party models |
| **AutoML** | No-code model training on custom data |
| **Pre-trained APIs** | Vision, Speech, NLP, Translation — no training needed |
| **Gemini in Google Cloud** | AI assistant embedded in Cloud Console |

### Prompt Engineering Best Practices
1. **Write detailed, explicit instructions** — vague prompts → poor results
2. **Define boundaries** — tell the model what to do (not just what not to do); include fallback responses
3. **Adopt a persona** — "You are a cloud architect…" improves contextual accuracy
4. **Keep sentences concise** — break long sentences into shorter ones
5. **Zero-shot**: prompt with no examples; **Few-shot**: prompt with examples; **Chain-of-thought**: ask model to reason step by step

### Prompt Components
- **Context/Preamble**: background info or role assignment
- **Input data**: the content the model should act on
- **Output indicator**: specify desired format (bullet list, JSON, markdown, etc.)

---

## 11. Cloud Monitoring & Operations (Observability)

### Four Golden Signals
| Signal | Definition |
|--------|-----------|
| **Latency** | Time to return a result; changes indicate emerging issues |
| **Traffic** | Volume of requests/demand on the system |
| **Saturation** | How "full" a resource is (CPU, memory, disk) |
| **Errors** | Rate of failed requests |

### Google Cloud Observability Products
| Product | Purpose |
|---------|---------|
| **Cloud Monitoring** | Metrics, dashboards, uptime checks, alerting |
| **Cloud Logging** | Collect, store, route, and analyze logs |
| **Cloud Trace** | Distributed request tracing; find latency bottlenecks |
| **Cloud Profiler** | Continuous CPU/memory profiling in production |
| **Error Reporting** | Auto-groups application errors; sends notifications |

### Cloud Monitoring
- **1500+ metrics** across 100+ GCP services collected **automatically** at no cost
- System metrics from GCE: 25+ unique metrics per VM
- Architecturally: Collection layer → Storage (Monitoring API) → Visualization/Analysis

#### Monitoring Architecture Patterns
- **Platform monitoring**: auto-ingested from GCP services (no agent needed)
- **GKE**: integrates with **Google Managed Prometheus (GMP)** for Prometheus-based monitoring
- **Compute Engine**: use **Ops Agent** for process/app metrics and logs
- Hybrid/multi-cloud: ingest via Cloud Monitoring APIs or Prometheus exporters

#### Dashboards & Charts
- **Default dashboards**: auto-created per GCP service
- **Custom dashboards**: combine charts from any metric
- **Uptime checks**: HTTP/HTTPS/TCP/ping checks from multiple global locations; track liveness + latency

#### PromQL
- Used in Cloud Monitoring to query Prometheus metrics (GMP)

---

## 12. Cloud Logging

### Architecture
```
Sources (GCP services, GKE, GCE via Ops Agent, apps)
  ↓
Log Router (inclusion/exclusion filters)
  ↓
Log Sinks → Cloud Logging Buckets | Pub/Sub | BigQuery | Cloud Storage
```

### Log Types
| Type | Description |
|------|-------------|
| **Platform logs** | Written by GCP services (e.g., VPC Flow Logs) |
| **Component logs** | GCP infrastructure components |
| **Security logs** | Audit logs, access transparency |
| **User-written logs** | From your apps via Logging API / Ops Agent / stdout |
| **Multi/Hybrid Cloud logs** | From non-GCP environments |

### Log Sinks
- Route logs to: **Cloud Logging buckets**, **BigQuery**, **Pub/Sub**, **Cloud Storage**
- Use **inclusion filters** to include specific logs
- Use **exclusion filters** to drop specific logs
- Multiple sinks per project allowed

### Log-Based Metrics
- Create **counter** or **distribution** metrics from log entries
- Use cases: count specific error messages, measure latency from logs
- Metrics can trigger alerting policies in Cloud Monitoring

### Log Explorer
- Optimized for troubleshooting: stream logs, histogram, log resource explorer
- Query language: Logging Query Language (LQL)

### Log Analytics
- BigQuery-powered analytics within Cloud Logging console
- Enable on a bucket: **permanent upgrade** — cannot downgrade
- Logs appear in both Log Analytics UI and as a BigQuery read-only view
- BigQuery ingestion/storage costs included in Logging costs

### Log Retention
| Log Type | Default Retention |
|----------|------------------|
| `_Required` bucket (Admin Activity, System Event) | 400 days, no cost, can't delete |
| `_Default` bucket | 30 days |
| Custom buckets | Configurable |

---

## 13. Alerting Policies

### Alert Policy Components
- **Condition**: metric threshold, absent metric, rate of change, uptime check
- **Notification channels**: email, SMS, PagerDuty, Slack, Pub/Sub, webhooks
- **Documentation**: runbook links, alert description

### Alert Types
| Type | Use Case |
|------|----------|
| **Metric threshold** | CPU > 80% for 5 min |
| **Metric absence** | No data received (indicates agent failure) |
| **Rate of change** | Rapid spike/drop |
| **Uptime check failure** | Endpoint unreachable |
| **Log-based metric** | Based on log filter matches |

### Alerting Strategy
- Set thresholds **higher than SLA** minimum — detect problems before customers are impacted
- **Burn rate alerts**: alert when error budget is being consumed too fast
  - Example: 60-min lookback window; burn rate threshold = 10× → alert if on pace to exhaust budget in 1/10 of the compliance period

---

## 14. SLI, SLO, SLA & Error Budgets

### Definitions
| Term | Definition |
|------|-----------|
| **SLI** (Service Level Indicator) | Carefully selected metric measuring one aspect of reliability. Recommended: `good events / all valid events` |
| **SLO** (Service Level Objective) | SLI + target reliability (e.g., 99.9% availability). Internal target. |
| **SLA** (Service Level Agreement) | Contractual commitment to customers. Breach = compensation/refunds. |
| **Error Budget** | `100% - SLO`. Time/requests you're allowed to fail. Drives engineering decisions. |

### SLO Must Be S.M.A.R.T.
- **S**pecific, **M**easurable, **A**chievable, **R**elevant, **T**ime-bound
- "100% availability" is **not achievable** — never set as SLO

### SLO Calculation Types
| Type | Formula |
|------|---------|
| **Request-based** | good requests / total requests |
| **Windows-based** | good windows / total windows |

- Windows-based SLOs can **hide burst failures** (e.g., errors every Friday 9-9:05 AM may never violate a window-based SLO)

### Error Budget in Practice
- SLO = 99.9% / 30-day compliance → error budget = 0.1% of requests = ~43 min downtime/month
- When error budget is exhausted → slow feature releases, prioritize reliability
- When error budget is healthy → accelerate feature velocity
- Operations teams need **executive support** to enforce consequences of SLO breaches

### SLI → SLO → SLA Example
- SLA: "Error rate < 0.3% for billing system"
- SLI: error rate (measurable metric)
- SLO: error rate < 0.2% (internal target, stricter than SLA)

---

## 15. Cloud Audit Logs

### Four Audit Log Types
| Log | What it records | Always on? | Default retention | Cost |
|-----|----------------|------------|------------------|------|
| **Admin Activity** | Config/metadata modifications (create VM, change IAM) | Yes | 400 days | Free |
| **System Event** | Google-initiated config changes (non-human) | Yes | 400 days | Free |
| **Data Access** | Read config/metadata; create/modify/read user data | **No** (except BigQuery) | 30 days | Charged |
| **Policy Denied** | Access denied by security policy | Yes | 30 days | Charged |

### Viewing Audit Logs
- Requires `Logging/Logs Viewer` or `Project/Viewer` for Admin Activity & System Event
- Requires `Logging/Private Logs Viewer` or `Project/Owner` for Data Access logs
- Filter in Logs Explorer: type `cloudaudit` in filter box

### Access Transparency Logs
- Records **Google personnel** actions on your data (not automated systems)
- Different from Cloud Audit Logs (which record **your organization's** actions)
- Requires appropriate support package

---

## 16. Quick Reference: Service Decision Trees

### Compute
```
Need full OS control / custom hardware?         → Compute Engine
Need container orchestration?                   → GKE
Need managed serverless containers (HTTP)?      → Cloud Run
Event-driven single function?                   → Cloud Run Functions
Simple web app (managed runtime)?               → App Engine Standard
Container app + custom runtime (no serverless)? → App Engine Flexible
```

### Storage
```
Unstructured/binary large objects (images, video, backups)?  → Cloud Storage
Relational DB, SQL, up to 64 TB?                             → Cloud SQL
Relational DB, SQL, global scale, petabytes?                 → Cloud Spanner
Mobile/web app, real-time sync, NoSQL documents?             → Firestore
IoT / AdTech / heavy R/W analytics, NoSQL wide-column?       → Bigtable
SQL analytics, reporting, data warehouse?                     → BigQuery
```

### Networking Connections to Google
```
Need SLA + dedicated private circuit?           → Dedicated Interconnect
Via ISP, lower bandwidth, need SLA?             → Partner Interconnect
BGP peering, no SLA needed?                     → Direct/Carrier Peering
Encrypted over internet?                        → Cloud VPN
```

---

## 17. Common Exam Traps

1. **VPC is global; subnets are regional** — VMs in different zones can share a subnet
2. **Preemptible = max 24 hrs; Spot = no max** — same price, same terminability
3. **Services and APIs enabled per project**, not per folder/org
4. **IAM role order (broadest → finest):** Basic → Predefined → Custom
5. **Project ID is immutable after creation** but changeable during creation
6. **Cloud Storage is object storage** — not file storage, not RDBMS, not data warehouse
7. **Archive storage** has the highest retrieval cost; use for <1x/year access
8. **Data Access audit logs are OFF by default** (except BigQuery); Admin Activity always on
9. **Dedicated Interconnect** is the only interconnect option with an SLA
10. **GKE nodes are Compute Engine VMs**
11. **Autopilot mode = Google manages nodes; Standard mode = you manage nodes**
12. **Cloud Run scales to zero; App Engine Flexible does NOT scale to zero**
13. **SLO is internal; SLA is contractual** — SLO should be stricter than SLA
14. **Windows-based SLOs can hide burst failures** — request-based SLOs more accurate
15. **Log Analytics bucket upgrade is permanent** — cannot downgrade
16. **Policy Denied audit logs cannot be disabled** (but can be excluded from storage)
17. **Cloud Shell** has a persistent **5 GB** home directory
18. **Four Golden Signals:** Latency, Traffic, Saturation, Errors

---

## 18. Practice Quiz — 50 Questions

**1.** Which of the five cloud computing traits means you can get resources without calling a sales rep?
> **A) On-demand self-service**

**2.** In the GCP resource hierarchy, policies are inherited in which direction?
> **Downward** (parent → children)

**3.** Which project attribute is globally unique, assigned by Google, but can be changed during creation?
> **Project ID**

**4.** Services and APIs in GCP are enabled on a per-________ basis.
> **Project**

**5.** What is the correct order of IAM role types from broadest to finest-grained?
> **Basic → Predefined → Custom**

**6.** A company has multiple departments that should each manage their own Google Cloud resources independently. Which hierarchy feature enables this?
> **Folders**

**7.** Which GCP service lets non-Google-Workspace organizations create an Organization node?
> **Cloud Identity**

**8.** What is the main reason to choose a Preemptible VM?
> **Reduce cost** (up to 90% cheaper)

**9.** What distinguishes a Spot VM from a Preemptible VM?
> **Spot VMs have no maximum runtime** (Preemptible = max 24 hrs)

**10.** You need block storage replicated across two zones. Which Compute Engine storage option should you use?
> **Regional Persistent Disk**

**11.** What is the scope of a VPC in Google Cloud?
> **Global** (subnets are regional)

**12.** In Google Cloud VPCs, what scope do subnets have?
> **Regional**

**13.** Which load balancer option provides a single global anycast IP with automatic multi-region failover for HTTPS traffic?
> **Global External Application Load Balancer**

**14.** Which interconnect option comes with an SLA?
> **Dedicated Interconnect**

**15.** You want outbound internet access for VMs that have no public IP. Which service do you use?
> **Cloud NAT**

**16.** Which Cloud Storage class should you choose for data you access less than once a year?
> **Archive**

**17.** Which Cloud Storage class is best for data you access about once a month?
> **Nearline**

**18.** What does enabling object versioning in Cloud Storage allow you to do?
> Keep a history of all overwrites/deletes and restore objects to earlier versions

**19.** Cloud Storage provides what type of storage?
> **Object storage** (not file, block, or relational)

**20.** Which database service supports horizontal scaling to petabyte sizes with full SQL support?
> **Cloud Spanner**

**21.** Which database is best for storing IoT data with very heavy read/write events and no SQL join requirements?
> **Bigtable**

**22.** Which database is best for a mobile app requiring real-time data sync and offline support?
> **Firestore**

**23.** What is a Kubernetes Pod?
> **A group of one or more containers** deployed together, sharing a network IP and storage

**24.** Where do GKE cluster nodes come from?
> **Compute Engine** (VMs)

**25.** In GKE, which mode has Google manage the node infrastructure?
> **Autopilot**

**26.** A Kubernetes Service provides what benefit over using Pod IPs directly?
> A **stable/fixed IP address** — Pod IPs change; Services are stable

**27.** Cloud Run is built on which open-source platform?
> **Knative**

**28.** What is the billing granularity for Cloud Run?
> **Nearest 100 milliseconds** of request handling time

**29.** Which scenario best fits Cloud Run (vs Cloud Run Functions)?
> Hosting a **full web application** with persistent HTTP traffic (Cloud Run Functions = single event-triggered function)

**30.** App Engine Standard differs from App Engine Flexible in that Standard can:
> **Scale to zero** (Flexible cannot; it runs on Compute Engine VMs)

**31.** What are the four golden signals of monitoring?
> **Latency, Traffic, Saturation, Errors**

**32.** Which Cloud Monitoring feature checks whether your endpoint is reachable from multiple global locations?
> **Uptime checks**

**33.** The Ops Agent on Compute Engine is used for what?
> Collecting **process and third-party application metrics and logs** from VMs

**34.** What is a log sink?
> A **destination** for routed logs (Cloud Logging bucket, BigQuery, Pub/Sub, or Cloud Storage)

**35.** You want to count the occurrences of a specific error message in your logs and alert on it. What do you create?
> A **log-based metric**, then an alerting policy on that metric

**36.** Upgrading a Cloud Logging bucket to use Log Analytics is:
> **Permanent — cannot be undone**

**37.** Which audit log type records when a user creates a VM or changes an IAM permission?
> **Admin Activity audit log**

**38.** Data Access audit logs are enabled by default for which service?
> **BigQuery only**

**39.** Which audit log type records Google-initiated actions (not driven by user)?
> **System Event audit log**

**40.** Which audit log type records when a security policy violation occurs?
> **Policy Denied audit log**

**41.** Admin Activity audit logs are retained for how long?
> **400 days**, free of charge

**42.** What does an SLI measure?
> A **specific aspect of service reliability**, expressed as `good events / total valid events`

**43.** How does an SLO differ from an SLA?
> **SLO is an internal target**; SLA is a **contractual commitment to customers**. SLO should be stricter.

**44.** Your SLO is 99.9% over 30 days. What is your error budget?
> **0.1%** of requests (or ~43 minutes of downtime per month)

**45.** When should you slow feature releases according to SRE principles?
> When the **error budget is exhausted** (or being consumed too fast)

**46.** A windows-based SLO with a 10-minute window might miss what type of failure?
> **Burst failures** that occur and recover within a single window

**47.** What does a burn rate alert detect?
> A trend where the error budget is being consumed **faster than expected**, heading toward an SLO violation

**48.** Which generative AI issue occurs when a model produces incorrect or nonsensical output?
> **Hallucination**

**49.** What is the best prompt engineering practice when the model doesn't know what to respond?
> Provide a **fallback response** in the prompt (e.g., "If unsure, say 'I'm still learning about that'")

**50.** You want to analyze log data using SQL and join it with other BigQuery datasets. What do you need to enable?
> **Log Analytics** on your Cloud Logging bucket (which creates a BigQuery-accessible view)

---

---

## 19. SRE — DevOps, Practices & Culture

### DevOps vs SRE
| | DevOps | SRE |
|--|--------|-----|
| What it is | Philosophy / culture | Concrete implementation of DevOps principles |
| Origin | Industry movement | Google (Benjamin Treynor Sloss, 2003) |
| Focus | Collaboration, communication, automation | Reliability, error budgets, automation, measuring toil |

### Five DevOps Pillars (and SRE alignment)
| DevOps Pillar | SRE Practice |
|---------------|-------------|
| Reduce organizational silos | Shared ownership of production; shared SLOs and error budgets |
| Accept failure as normal | Blameless postmortems; psychological safety |
| Implement gradual change | CI/CD; canarying |
| Leverage tooling & automation | Automate toil; "automate this year's job away" |
| Measure everything | SLIs, SLOs, error budgets; toil measurement; OKRs |

### Blameless Postmortem
- Written record of an incident after it occurs
- Components: incident details + timeline, actions taken, impact, trigger + root cause(s), follow-up actions to prevent recurrence
- **Blameless**: focuses on root causes, not blaming individuals
- Uses **"5 Whys"** technique to find all contributing factors
- Goals: understand all root causes; prevent recurrence; reduce outage stress; avoid complexity accumulation; share learning across teams
- Use real-time collaborative tools (e.g., Google Docs) to enable crowdsourcing of information

### CI/CD
- **Continuous Integration (CI)**: build, integrate, and test code continuously in development — improves code quality, catches issues early
- **Continuous Delivery (CD)**: deploy to production frequently (or at the business's chosen cadence); includes test and deployment automation
- Benefits: fewer integration headaches, less human error, higher code quality, shorter time-to-market, easier rollback

### Canarying
- Deploy a change to a **small subset of users** without their knowledge; evaluate impact; then decide to roll out or roll back
- Requirements: canary population must be (1) large enough to be representative, (2) small enough not to endanger the whole service, (3) simple enough to reason about
- Reduces blast radius of bad deployments

### Toil
- Work that is: **manual, repetitive, automatable, tactical, devoid of enduring value**, and scales linearly with service growth
- Toil is **not** the same as overhead (meetings, planning, etc.)
- SRE goal: keep toil below **50%** of each SRE's time; the rest on engineering work
- **Measuring toil**: (1) identify it, (2) choose a unit of measure (hours/tickets), (3) track continuously before/during/after reduction

### Error Budget
- `Error Budget = 100% - SLO target`
- **When budget is healthy**: innovate fast, ship features
- **When budget is exhausted**: slow releases, focus on reliability
- Error budget is owned jointly by product and engineering; product management defines it, engineering informs it

### OKRs (Objectives and Key Results)
- Google's KPI framework; graded 0.0–1.0
- Optimal OKR grade: **60–70%** (stretch goals, not performance reviews)
- Graded publicly; frequent check-ins throughout the quarter
- Cross-functional teams often share OKRs to create shared accountability

### SRE Team Roles
- **Tech Lead**: sets technical direction, code reviews, consensus building
- **Manager**: performance management, first point of contact
- **Project Manager**: design reviews, writes code

### Service-Oriented Meetings
- Weekly, 30–60 min; compulsory attendance; designated lead
- Agenda: upcoming production changes, metrics, outages, paging/non-paging events, prior action items

---

## 20. Terraform on Google Cloud

### What is IaC?
- Define, provision, and manage infrastructure via code files (not UI clicks)
- **Declarative**: specify desired end state; Terraform figures out the steps
- Benefits: version control, auditability, portability (reusable modules), reduces human error

### Terraform Workflow
```
Scope → Author → Initialize → Plan → (Validate) → Apply
```
| Step | What happens |
|------|-------------|
| **Scope** | Confirm resources needed |
| **Author** | Write `.tf` config files |
| **Initialize** (`terraform init`) | Download provider plugins |
| **Plan** (`terraform plan`) | Preview what will be created/modified/destroyed |
| **Validate** (`gcloud beta terraform vet`) | Optional — check against org policy constraints |
| **Apply** (`terraform apply`) | Create actual infrastructure |
| **Destroy** (`terraform destroy`) | Delete all managed resources |

### HCL Syntax
```hcl
resource "google_compute_network" "default" {
  name                    = "mynetwork"
  auto_create_subnetworks = false
}
```
- **Block**: `resource`, `variable`, `output`, `module`, `provider`, `data`
- **Resource type**: e.g., `google_compute_network`
- **Resource name**: local label for referencing
- **Arguments**: key = value pairs inside the block
- Reference another resource: `<resource_type>.<resource_name>.<attribute>` (e.g., `google_compute_network.default.id`)

### Key HCL Constructs
| Construct | Purpose |
|-----------|---------|
| `variable` | Parameterize configuration (input) |
| `output` | Export resource attribute values after apply |
| `module` | Reusable group of resources (DRY principle) |
| `data` | Read existing resources not managed by this config |
| `depends_on` | Explicit dependency declaration |

### Resource Dependencies
- **Implicit dependency**: Terraform auto-detects when one resource references another's attribute — creates the dependency automatically
- **Explicit dependency**: Use `depends_on` when there's a dependency Terraform can't detect from references alone

### Modules
- A directory of `.tf` files = a module
- **Root module**: the working directory from which you run `terraform plan`/`apply`
- **Child modules**: reusable components called from root or other modules
- Module source can be: local path, Terraform Registry, GitHub, etc.
- Use `version` argument to pin module versions
- **DRY principle**: define once, reuse across dev/staging/prod with different variable values
- **Cloud Foundation Toolkit (CFT)**: Google-maintained collection of Terraform modules published to Terraform Registry; simplifies IAM, networking, and other GCP patterns

### Terraform State
- State file (`terraform.tfstate`) = metadata repository mapping config → real infrastructure
- Default: stored **locally** in working directory
- **Remote state** (recommended for teams): store in **Cloud Storage bucket**

| Local State | Remote State (Cloud Storage) |
|-------------|------------------------------|
| Works for solo dev | Required for teams |
| No locking | **Native locking** (prevents concurrent corruption) |
| Sensitive data exposed | Encrypted at rest + IAM-controlled |
| No versioning | Versioning supported |

### State Best Practices
- Always use remote state for team environments
- Use **GCS bucket** as Terraform backend for GCP
- Do **not** store secrets in state (stored in plaintext)
- Do **not** modify state manually — use `terraform state` commands
- Add `terraform.tfstate*` to `.gitignore`
- Use customer-supplied encryption keys for extra protection (`GOOGLE_ENCRYPTION_KEY`)
- Only build system + highly privileged admins should access the state bucket

### Terraform Validator (`gcloud beta terraform vet`)
- Runs policy compliance checks between `plan` and `apply`
- Uses **constraints** (policy library) defined by security/governance teams
- Can block non-compliant deployments before they reach production
- Different from `terraform validate` (which only checks syntax/structure)

### Infrastructure Manager
- Google-managed service that uses Terraform to deploy GCP infrastructure
- Does **not** deploy applications (use Cloud Build / Cloud Deploy for apps)

---

## 21. Infrastructure Design & Architecture

### Defining Services
**Qualitative requirements → User stories**
- Format: *"As a [role], I want to [do something], so that I can [benefit]"*
- Roles describe **user objectives**, not job titles; one person can have multiple roles
- **Personas**: imaginary representative users for a role; help architects make design decisions from the user perspective

**Quantitative requirements → KPIs & SLOs**
- KPIs should be **SMART**: Specific, Measurable, Achievable, Relevant, Time-bound
- Good SLIs have a **close linear relationship** with user experience (not just CPU/memory)
- Bad metric: high variance, overlapping ranges between "normal" and "outage" state
- Good metric: stable in normal operation, clearly different during outage

### Microservices vs Monolith
| | Monolith | Microservices |
|--|----------|--------------|
| Codebase | Single | Multiple, independent |
| Data store | Shared DB | Each service owns its data |
| Deployment | All at once | Independent deployments |
| Scaling | Scale entire app | Scale services independently |
| Failure | Single point of failure risk | Isolated failures |

**Benefits of microservices:** independent deployments, A/B testing, independent scaling, fault isolation, team autonomy
**Challenges:** network latency, service-to-service security, complex versioning, distributed tracing harder

**Decompose by:** feature (orders, products, reviews); architectural layer (UI, data access); shared functionality (auth)

**GCP compute for microservices:** App Engine, Cloud Run, GKE, Cloud Run Functions

### Reliability Patterns

#### Avoid Single Points of Failure
- Deploy **N+2** instances: N for load + 1 for upgrades + 1 for failures
- Deploy across **multiple zones** to avoid correlated zonal failures
- Make deployment units **stateless interchangeable clones**

#### Failure Domains
- A **failure domain** = group of related items that can fail together
- Types: single machine → rack (top-of-rack switch) → zone → region → software version → shared config system
- **Mitigation**: distribute across failure domains; use microservices in different zones/regions

#### Cascading Failures
- Occur when one system's failure overloads others (e.g., Server B fails → Server A gets 2× traffic → A fails too)
- **Prevention**: load shedding, circuit breakers, autoscaling, health checks

#### Circuit Breaker Pattern
- Proxy monitors service health; **stops forwarding requests** when service is unhealthy
- Prevents clients' retries from overloading an already-struggling service
- When service recovers, circuit breaker resumes forwarding gradually
- GKE: **Istio** implements circuit breakers automatically

#### Truncated Exponential Backoff
- On failure: retry, but wait progressively longer between attempts + random jitter
- Example: wait 1s → 2s → 4s → 8s (+ random ms each time), up to a max limit
- Prevents thundering herd / positive-feedback overload loops

#### Lazy Deletion
- Don't immediately purge deleted data — mark as deleted, keep for a grace period, then purge
- Allows recovery if user accidentally deletes data

### Disaster Recovery Planning

| Term | Definition |
|------|-----------|
| **RPO** (Recovery Point Objective) | Maximum acceptable data loss (e.g., "we can lose up to 1 hour of data") |
| **RTO** (Recovery Time Objective) | Maximum acceptable downtime (e.g., "must be back up within 5 minutes") |

**DR Planning Steps:**
1. Brainstorm failure scenarios per service
2. Assign RPO and RTO to each scenario
3. Prioritize by business impact
4. Design backup strategy based on RPO/RTO
5. Document and communicate recovery procedures
6. **Test and drill** at least once per year; ideally make recovery part of daily ops

**Backup strategies by service type:**
| Service | Strategy |
|---------|---------|
| Firestore | Daily automated backups → multi-regional Cloud Storage |
| Cloud SQL | Binary logging + failover replica (for low RPO) |
| BigQuery | Re-import from source data (no backup needed if source retained) |

### Security Design Principles

#### Shared Responsibility Model
- Google secures: hardware, physical infrastructure, hypervisor, network
- **You secure**: IAM, firewall rules, data encryption, application code, access controls

#### Key Principles
| Principle | Description |
|-----------|-------------|
| **Principle of Least Privilege** | Grant only the minimum permissions required; applies to users, service accounts, and processes |
| **Separation of Duties** | No single person can change + delete data + report on it; developers ≠ deployers |
| **Defense in Depth** | Multiple security layers; never rely on a single control |
| **Regular Audits** | Use Cloud Audit Logs to detect attacks and compliance violations |

#### Security Tools
| Tool | Use |
|------|-----|
| **IAM** | Authentication + authorization for humans and service accounts |
| **Identity-Aware Proxy (IAP)** | Control access to apps without VPN; verifies identity before allowing through |
| **Security Command Center** | Centralized vulnerability scanning and threat detection |
| **Cloud Armor** | DDoS protection and WAF (Web Application Firewall) for external load balancers |
| **VPC Service Controls** | Create security perimeters around GCP APIs to prevent data exfiltration |
| **Organization Policies** | Governance guardrails applied at org/folder/project level |
| **Private Google Access** | Allow VMs without public IPs to reach GCP APIs |

#### Network Security
- Use **private IPs** wherever possible — minimize exposure
- **Firewall rules**: restrict ingress/egress; use network tags for targeted rules
- **Cloud Armor**: mitigate DDoS at the edge
- **Cloud DNS + IAP**: protect internal services

---

## 22. Gemini Cloud Assist & AI Troubleshooting

### Gemini Cloud Assist
- AI-powered assistant embedded in the Google Cloud Console
- Capabilities:
  - **Design & Build**: Generate CLI commands and Terraform code from natural language
  - **Diagnose & Resolve**: AI-driven **Cloud Assist Investigations** for root-cause analysis
  - **Optimize Cost & Usage**: FinOps Hub, database cost recommendations

### Cloud Assist Investigations
- Root-cause analysis (RCA) tool for troubleshooting GCP infrastructure
- Key metric it targets: **MTTR** (Mean Time to Recovery)
- Can be triggered from: Logs Explorer, GKE Health page, Cloud Monitoring alerts, Gemini chat panel
- Process: Issue → Observations → Hypotheses → Root cause → Recommended fix → (escalate to support case)

---

## 23. Additional Practice Questions (New Topics)

**51.** What is the key difference between SRE and DevOps?
> DevOps is a **philosophy**; SRE is a **concrete implementation** of that philosophy with specific technical practices

**52.** What makes a postmortem "blameless"?
> It focuses only on **root causes and systemic factors**, not on individual blame or personal behavior

**53.** Toil must have which characteristics? (name 3)
> **Manual, repetitive, automatable** (also: tactical, no enduring value, scales linearly with load)

**54.** What does `terraform init` do?
> **Downloads provider plugins** and initializes the working directory

**55.** Which Terraform command shows what will be created/modified/destroyed without making changes?
> `terraform plan`

**56.** What is an implicit dependency in Terraform?
> When one resource **references another resource's attribute** — Terraform auto-detects and orders creation accordingly

**57.** Why should Terraform state be stored remotely in a team environment?
> Remote state provides **locking** (prevents concurrent corruption), **shared access**, and **secure encryption**

**58.** What does `gcloud beta terraform vet` do?
> Validates Terraform plans against **org policy constraints** before apply — enforces governance

**59.** What is the DRY principle in Terraform and how is it implemented?
> "Don't Repeat Yourself" — implement reusable code as **modules** and call them with different variables per environment

**60.** What is the RPO?
> **Recovery Point Objective** — the maximum acceptable amount of data loss (e.g., "1 hour of data")

**61.** What is the RTO?
> **Recovery Time Objective** — the maximum acceptable time to restore service after a failure

**62.** What pattern prevents cascading failures when a service is overloaded with retries?
> **Circuit breaker** — proxy stops forwarding requests to unhealthy service until it recovers

**63.** What is truncated exponential backoff used for?
> Retrying failed requests with **progressively longer waits + random jitter** to avoid overload

**64.** What is N+2 deployment?
> Deploy N instances needed for load **+ 1 for upgrades + 1 for failures** to maintain availability during maintenance

**65.** Which GCP tool provides DDoS protection?
> **Cloud Armor**

**66.** What is Identity-Aware Proxy (IAP)?
> Controls access to applications by **verifying user identity without requiring a VPN**

**67.** What GCP feature lets you create security perimeters around GCP APIs to prevent data exfiltration?
> **VPC Service Controls**

**68.** What is canarying?
> Deploying a change to a **small representative subset of users**, evaluating impact, then deciding to roll out fully or roll back

**69.** In Terraform, what is the root module?
> The **working directory from which you run Terraform commands** — where `.tf` files are loaded

**70.** What is Cloud Foundation Toolkit (CFT)?
> Google-maintained collection of **Terraform modules** for GCP, published to the Terraform Registry

---

*Study guide generated from: GCP Fundamentals (M1–M8), Cloud Monitoring & Operations (M1–M5), SRE Foundations (M1–M6), Terraform for GCP (M1–M5), Infrastructure Design & Architecture (M1–M9), Gemini Cloud Assist*
