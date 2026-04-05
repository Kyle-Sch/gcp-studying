# 5. VPC Networking

## Scope Rules

| Resource | Scope |
|----------|-------|
| **VPC** | **Global** â€” one VPC can span all regions |
| **Subnets** | **Regional** â€” but can span zones within a region |
| VMs in different zones | Can share the same subnet âœ“ |

## Connecting VPCs

| Method | When to use |
|--------|------------|
| **VPC Peering** | Connect two VPCs to exchange traffic â€” no IAM control |
| **Shared VPC** | Share a host project's VPC with service projects â€” full IAM control |

## Load Balancer Types

| Balancer | Layer | Scope | Best for |
|----------|-------|-------|---------|
| Global External Application LB | L7 HTTP/S | Global | Web apps, SSL termination, multi-region failover |
| Regional External Application LB | L7 HTTP/S | Regional | Regional web apps |
| Regional Internal Application LB | L7 HTTP/S | Internal | Internal microservices |
| Network LB â€” Proxy | L4 | Regional | TCP/SSL proxy |
| Network LB â€” Passthrough | L4 | Regional | UDP / raw TCP / preserve client IP |

> Global LB = single anycast IP + automatic multi-region failover + **no pre-warming needed**

## Connecting to Google's Network

| Option | SLA? | Notes |
|--------|------|-------|
| **Dedicated Interconnect** | âœ… Yes | Private physical circuit direct to Google |
| **Partner Interconnect** | âœ… Yes | Via service provider |
| **Direct Peering** | âŒ No | Unmanaged BGP |
| **Carrier Peering** | âŒ No | Via ISP |
| **Cloud VPN** | â€” | Encrypted over public internet |

> Only **Dedicated Interconnect** guarantees an SLA.

## Other Networking Services

| Service | Purpose |
|---------|---------|
| **Cloud DNS** | Authoritative DNS, 100% uptime SLA |
| **Cloud CDN** | Cache at Google POPs â€” uses global LB |
| **Cloud NAT** | Outbound internet for VMs with no public IP |
| **Firewall rules** | Stateful, distributed â€” applied via tags or IP ranges |
