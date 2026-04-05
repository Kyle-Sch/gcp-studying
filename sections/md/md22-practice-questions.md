# 22. Practice Questions

## Cloud Fundamentals & IAM (Q1â€“10)

**Q1.** Which NIST cloud trait means you can provision resources without human assistance?
> **On-demand self-service**

**Q2.** In the GCP resource hierarchy, policies are inherited in which direction?
> **Downward** â€” parent policies apply to all children

**Q3.** Which project attribute is globally unique, assigned by Google, but can be changed *during* creation only?
> **Project ID**

**Q4.** Services and APIs are enabled on a per-________ basis.
> **Project**

**Q5.** What is the correct order of IAM roles from broadest to finest-grained?
> **Basic â†’ Predefined â†’ Custom**

**Q6.** A company wants each department to manage its own GCP resources independently. What hierarchy feature enables this?
> **Folders**

**Q7.** A non-Google-Workspace company needs to create an Organization node. What do they use?
> **Cloud Identity**

**Q8.** What is the main reason to choose a Preemptible VM?
> **Reduce cost** â€” up to 90% cheaper

**Q9.** What distinguishes a Spot VM from a Preemptible VM?
> **Spot VMs have no maximum runtime** (Preemptible = 24 hr max)

**Q10.** You need block storage replicated across two zones. Which option?
> **Regional Persistent Disk**

---

## Networking & Storage (Q11â€“22)

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
> **Object storage** â€” not file storage, not block storage, not a database

**Q20.** Which database supports horizontal SQL scaling to petabytes?
> **Cloud Spanner**

**Q21.** Which database is best for IoT data with heavy read/write events and no SQL join requirements?
> **Bigtable**

**Q22.** Which database is best for a mobile app needing real-time sync and offline support?
> **Firestore**

---

## Containers, Serverless & AI (Q23â€“35)

**Q23.** What is a Kubernetes Pod?
> One or more containers deployed together, sharing a network IP and storage volume

**Q24.** Where do GKE cluster nodes come from?
> **Compute Engine VMs**

**Q25.** In GKE, which mode has Google manage the node infrastructure?
> **Autopilot**

**Q26.** Why use a Kubernetes Service instead of Pod IPs directly?
> Services provide a **stable IP and DNS name** â€” Pod IPs change; Services don't

**Q27.** What open-source platform is Cloud Run built on?
> **Knative**

**Q28.** What is Cloud Run's billing granularity?
> **Nearest 100 milliseconds** of active request handling

**Q29.** Which scenario fits Cloud Run better than Cloud Run Functions?
> **A full web application** with ongoing HTTP traffic â€” Functions = single event-triggered tasks

**Q30.** How does App Engine Standard differ from Flexible?
> Standard can **scale to zero** â€” Flexible runs on Compute Engine VMs and cannot

**Q31.** What is a hallucination in a generative AI model?
> The model produces **plausible-sounding but incorrect or fabricated output**

**Q32.** What is the best practice when a prompt model doesn't know the answer?
> Include a **fallback response** in the prompt (e.g., "If unsure, say 'I don't have that information'")

**Q33.** What are the three prompt engineering techniques involving examples?
> **Zero-shot** (no examples) Â· **Few-shot** (a few examples) Â· **Chain-of-thought** (reason step-by-step)

**Q34.** Gemini Cloud Assist "Diagnose & Resolve" uses which tool?
> **Cloud Assist Investigations**

**Q35.** What metric does Cloud Assist Investigations primarily help reduce?
> **MTTR â€” Mean Time to Recovery**

---

## Monitoring, Logging & Alerts (Q36â€“50)

**Q36.** What are the four golden signals?
> **Latency Â· Traffic Â· Saturation Â· Errors**

**Q37.** Which Cloud Monitoring feature verifies endpoint reachability from multiple global locations?
> **Uptime checks**

**Q38.** What does the Ops Agent do?
> Collects **process and third-party application metrics and logs** from Compute Engine VMs

**Q39.** What is a log sink?
> A **destination for routed logs** â€” Logging bucket, BigQuery, Pub/Sub, or Cloud Storage

**Q40.** You want to count a specific error in logs and trigger an alert. What do you create first?
> A **log-based metric** â†’ then an alerting policy on that metric

**Q41.** Upgrading a Cloud Logging bucket to use Log Analytics is:
> **Permanent â€” it cannot be undone**

**Q42.** Which audit log records "user creates a VM" or "user changes IAM policy"?
> **Admin Activity audit log**

**Q43.** Data Access audit logs are enabled by default only for which service?
> **BigQuery**

**Q44.** Which audit log records Google-initiated (non-human) changes?
> **System Event audit log**

**Q45.** Which audit log records a security policy blocking access?
> **Policy Denied audit log**

**Q46.** How long are Admin Activity audit logs retained?
> **400 days** â€” free of charge

**Q47.** SLO = 99.9% over 30 days. What is the error budget in minutes?
> **~43 minutes** per month

**Q48.** When should a team slow feature releases according to SRE principles?
> When the **error budget is exhausted**

**Q49.** What kind of failure can a windows-based SLO hide?
> **Burst failures** that occur and recover within a single measurement window

**Q50.** You want to query log data with SQL and join it to other BigQuery tables. What do you enable?
> **Log Analytics** on the Cloud Logging bucket (creates a BigQuery read-only view)

---

## SRE, Terraform & Infrastructure (Q51â€“70)

**Q51.** What is the key difference between SRE and DevOps?
> DevOps is a **philosophy**. SRE is a **concrete implementation** of that philosophy.

**Q52.** What makes a postmortem "blameless"?
> It focuses on **systemic root causes** â€” never on individual blame

**Q53.** Name three characteristics that define toil.
> **Manual Â· Repetitive Â· Automatable** (also: tactical, no enduring value, scales linearly)

**Q54.** What does `terraform init` do?
> **Downloads provider plugins** and initializes the working directory

**Q55.** Which Terraform command previews changes without making them?
> `terraform plan`

**Q56.** What is an implicit dependency in Terraform?
> When resource A references resource B's attribute â€” **Terraform auto-detects the ordering**

**Q57.** Why store Terraform state remotely for teams?
> Remote state provides **locking**, **shared access**, and **encryption**

**Q58.** What does `gcloud beta terraform vet` do?
> Validates a Terraform plan against **organization policy constraints** before apply

**Q59.** What is the DRY principle in Terraform?
> "Don't Repeat Yourself" â€” extract reusable code into **modules**, call with different variables per environment

**Q60.** What is RPO?
> **Recovery Point Objective** â€” maximum acceptable data loss (e.g., "1 hour of data")

**Q61.** What is RTO?
> **Recovery Time Objective** â€” maximum acceptable time to restore the service

**Q62.** Which pattern stops retries from overloading a struggling service?
> **Circuit breaker** â€” proxy stops forwarding requests until the service recovers

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
> The **working directory from which you run Terraform commands** â€” where `.tf` files are loaded

**Q70.** What is the Cloud Foundation Toolkit (CFT)?
> Google's collection of **pre-built Terraform modules** for GCP, published to the Terraform Registry

---

*Sources: GCP Fundamentals (M1â€“M8) Â· Cloud Operations Suite (M1â€“M5) Â· SRE Foundations (M1â€“M6) Â· Terraform for GCP (M1â€“M5) Â· Infrastructure Design & Architecture (M1â€“M9) Â· Gemini Cloud Assist*
