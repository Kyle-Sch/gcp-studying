# 8. Containers & GKE

## Containers vs VMs

| | VM | Container |
|--|----|-----------|
| Virtualizes | Hardware | OS kernel |
| Startup time | Minutes | Seconds / milliseconds |
| OS per unit | Full OS | Shared kernel |
| Portability | Low | High â€” "code once, run anywhere" |

## Kubernetes Core Concepts

| Concept | What it is |
|---------|-----------|
| **Cluster** | Set of nodes running containers |
| **Node** | A VM (in GKE: a Compute Engine instance) |
| **Pod** | Smallest unit â€” 1+ containers sharing an IP + storage |
| **Deployment** | Manages a group of identical pods; keeps them running |
| **Service** | Stable IP/DNS for a set of pods (pod IPs change; Services don't) |

## GKE Modes

| Mode | Who manages nodes | Best for |
|------|------------------|---------|
| **Autopilot** | Google | Production â€” strong security, less ops overhead |
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
