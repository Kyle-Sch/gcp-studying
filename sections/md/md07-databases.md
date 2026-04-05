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
Need SQL + >64 TB?             â†’ Spanner
Need SQL + <64 TB?             â†’ Cloud SQL
Mobile/web app, real-time?     â†’ Firestore
Heavy analytics, no SQL joins? â†’ Bigtable
Reporting / data warehouse?    â†’ BigQuery
Storing files / blobs?         â†’ Cloud Storage
```
