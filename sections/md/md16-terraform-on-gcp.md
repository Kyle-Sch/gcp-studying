# 16. Terraform on GCP

## The Terraform Workflow

```
1. Scope      â€” identify what resources are needed
2. Author     â€” write .tf configuration files
3. Initialize â€” terraform init  (downloads provider plugins)
4. Plan       â€” terraform plan  (preview changes â€” no modifications made)
5. Validate   â€” gcloud beta terraform vet  (optional policy check)
6. Apply      â€” terraform apply  (create / update infrastructure)
7. Destroy    â€” terraform destroy  (tear everything down)
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
| `variable` | Input â€” parameterize the config |
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
- **Cloud Foundation Toolkit (CFT)** â€” Google's published GCP modules on the Terraform Registry

## Terraform State

| | Local State | Remote State (GCS bucket) |
|-|-------------|--------------------------|
| Good for | Solo developer | Teams |
| Locking | âŒ None | âœ… Native locking |
| Security | Sensitive data in plaintext | Encrypted + IAM controlled |
| Sharing | âŒ Manual | âœ… Automatic |

## State Best Practices

- Always use **remote state** for teams â€” store in a Cloud Storage bucket
- **Never store secrets** in state files (they're stored in plaintext)
- **Never edit** the state file manually â€” use `terraform state` commands
- Add `terraform.tfstate*` to `.gitignore`
- Restrict state bucket access to build systems + privileged admins only

## Terraform Validator

| Command | Purpose |
|---------|---------|
| `gcloud beta terraform vet` | Check plan against org **policy constraints** before apply |
| `terraform validate` | Check **syntax only** â€” does not enforce policies |
