# 2. Resource Hierarchy

```
Organization Node
    â””â”€â”€ Folders  (optional, nestable)
         â””â”€â”€ Projects  â† API & billing unit
              â””â”€â”€ Resources  (VMs, buckets, DBsâ€¦)
```

## Key Rules

- Policies **inherit downward** â€” a folder policy applies to all projects inside it
- A **more permissive parent** policy overrides a more restrictive child policy
- Each resource belongs to **exactly one project**
- APIs are enabled/disabled **per project** (not per folder or org)

## Project Identifiers

| Attribute | Globally Unique? | Who sets it? | Can change after creation? |
|-----------|-----------------|--------------|---------------------------|
| **Project ID** | Yes | Google (but you can edit during creation) | **No â€” immutable** |
| **Project name** | No | You | Yes |
| **Project number** | Yes | Google | No |

## Organization Node

- Required to use Folders
- Auto-created if you're a **Google Workspace** customer
- Otherwise: create one using **Cloud Identity**
- Special roles: `Organization Policy Administrator`, `Project Creator`
