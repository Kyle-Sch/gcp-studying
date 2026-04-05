# 20. Quick Decision Trees

## Compute

```
Full OS control / persistent workload?   â†’  Compute Engine
Container orchestration at scale?        â†’  GKE
Serverless container (HTTP/Pub/Sub)?     â†’  Cloud Run
Event-driven single function?            â†’  Cloud Run Functions
Simple web app, managed runtime?         â†’  App Engine Standard
Custom runtime, no scale-to-zero?        â†’  App Engine Flexible
```

## Storage

```
Blobs, images, video, backups?           â†’  Cloud Storage
SQL, up to 64 TB?                        â†’  Cloud SQL
SQL, global scale, petabytes?            â†’  Cloud Spanner
Mobile/web app, real-time NoSQL?         â†’  Firestore
IoT / AdTech / heavy reads + writes?     â†’  Bigtable
SQL analytics / data warehouse?          â†’  BigQuery
```

## Connecting to Google's Network

```
Need SLA + dedicated private circuit?    â†’  Dedicated Interconnect
Via ISP / lower bandwidth + SLA?         â†’  Partner Interconnect
BGP peering, no SLA required?            â†’  Direct / Carrier Peering
Encrypted tunnel over public internet?   â†’  Cloud VPN
```
