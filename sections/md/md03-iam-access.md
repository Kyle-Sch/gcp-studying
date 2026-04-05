# 3. IAM & Access

## IAM Policy = Who + Role + Resource

- **Who (Principal):** Google Account Â· Google Group Â· Service Account Â· Cloud Identity domain
- **Role:** a collection of permissions
- **Resource:** what the role applies to

## Role Types â€” Broadest to Finest

| Type | Scope | Example |
|------|-------|---------|
| **Basic (Primitive)** | All resources in project | Owner, Editor, Viewer |
| **Predefined** | Specific service | `roles/storage.objectViewer` |
| **Custom** | You define exactly what's allowed | Bespoke project roles |

> Hierarchy: **Basic â†’ Predefined â†’ Custom**
> Always prefer Predefined or Custom â€” Basic roles are too broad.

## Service Accounts

- Identity for machines/workloads (not humans)
- Email format: `name@project-id.iam.gserviceaccount.com`
- Grant IAM roles to service accounts just like users
- Restrict the **default service account** â€” it has broad permissions by default

## Ways to Interact with GCP

| Tool | What it's for |
|------|--------------|
| **Cloud Console** | GUI â€” SSH in browser, billing, resource management |
| `gcloud` CLI | Main command-line tool |
| `bq` | BigQuery command-line |
| **Cloud Shell** | Browser-based Debian VM Â· 5 GB persistent home Â· always authenticated |
| **Client Libraries** | Java, Python, PHP, C#, Go, Node.js, Ruby, C++ |
| **Cloud Mobile App** | Start/stop VMs Â· manage Cloud SQL Â· view logs Â· billing alerts |
