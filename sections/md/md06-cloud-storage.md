# 6. Cloud Storage

## Storage Classes

| Class | Access Pattern | Min Duration | Cost |
|-------|---------------|--------------|------|
| **Standard** | Frequent / hot data | None | Highest storage |
| **Nearline** | ~Once per month | 30 days | Lower storage, retrieval fee |
| **Coldline** | ~Once per 90 days | 90 days | Lower storage, higher retrieval fee |
| **Archive** | Less than once per year | 365 days | Lowest storage, highest retrieval fee |

- **Autoclass** â€” automatically moves objects to the right class based on access patterns
- All classes share the same API, tools, and security

## Key Features

| Feature | Detail |
|---------|--------|
| **Immutability** | Objects can't be edited â€” new version created on every change |
| **Versioning** | Keep full history of overwrites/deletes; restore anytime |
| **Lifecycle policies** | Auto-delete or transition objects (e.g., delete after 365 days) |
| **Encryption** | Server-side at rest â€” free. HTTPS/TLS in transit â€” always on |
| **Access control** | IAM (preferred) + ACLs for object-level control |
| **Max object size** | 5 TB |

> Cloud Storage = **object storage only** â€” not a file system, not a database, not a data warehouse.
