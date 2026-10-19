# Managed Kubernetes (EKS, AKS, GKE)

## Learning Goal

So far, you've learned Kubernetes using:

```text
Minikube
Kind
Local Clusters
```

Real companies rarely run Kubernetes manually.

Instead, they use Managed Kubernetes services.

Major cloud providers offer:

```text
Amazon EKS (Elastic Kubernetes Service)

Microsoft AKS (Azure Kubernetes Service)

Google GKE (Google Kubernetes Engine)
```

These services reduce operational overhead by managing large portions of the Kubernetes control plane.

By the end of this phase, you should be able to:

✅ Understand Managed Kubernetes

✅ Compare EKS, AKS, and GKE

✅ Create Managed Clusters

✅ Configure Node Pools

✅ Configure Networking

✅ Configure Identity and Access

✅ Configure Storage

✅ Configure Load Balancers

✅ Monitor Managed Clusters

✅ Design Production Cloud Deployments

✅ Optimize Costs

---

# Module 1: Why Managed Kubernetes?

Self-managed Kubernetes requires:

```text
Control Plane Setup
etcd Management
Upgrades
Certificates
Backups
High Availability
```

This becomes operationally expensive.

---

Managed Kubernetes handles:

```text
Control Plane
API Server
Scheduler
Controller Manager
etcd
```

for you.

---

# Module 2: Managed Kubernetes Architecture

```text
Cloud Provider

      ↓

Managed Control Plane

      ↓

Worker Nodes

      ↓

Pods
```

You manage:

```text
Applications
Workloads
Security
Networking
Storage
```

Provider manages:

```text
Control Plane
Platform Reliability
Core Infrastructure
```

---

# Module 3: EKS Overview

Amazon Kubernetes Service.

```text
Amazon EKS
```

Common in:

```text
AWS Organizations
Enterprise Workloads
Startups on AWS
```

Key Integrations:

```text
IAM
EBS
EFS
ALB
CloudWatch
```

---

# Module 4: AKS Overview

Azure Kubernetes Service.

Popular among:

```text
Microsoft Enterprises
Azure Customers
.NET Organizations
```

Integrations:

```text
Azure AD
Azure Monitor
Azure Key Vault
Azure Storage
Azure Load Balancer
```

---

# Module 5: GKE Overview

Google Kubernetes Engine.

Known for:

```text
Strong Kubernetes Support
Autopilot Mode
Fast Feature Adoption
```

Integrations:

```text
Cloud Storage
Cloud Load Balancing
Cloud Monitoring
Workload Identity
```

---

# Module 6: EKS vs AKS vs GKE

| Feature | EKS | AKS | GKE |
|----------|-----|-----|-----|
| Provider | AWS | Azure | Google Cloud |
| Identity | IAM | Azure AD | IAM |
| Storage | EBS/EFS | Azure Disk/Files | Persistent Disks |
| Monitoring | CloudWatch | Azure Monitor | Cloud Monitoring |
| Managed Control Plane | Yes | Yes | Yes |
| Autopilot Mode | No | Limited | Yes |

---

# Module 7: Creating an EKS Cluster

Using eksctl:

```bash
eksctl create cluster \
--name demo-cluster \
--region us-east-1
```

Verify:

```bash
kubectl get nodes
```

---

# Module 8: Creating an AKS Cluster

Azure CLI:

```bash
az aks create \
--resource-group demo-rg \
--name demo-aks
```

Connect:

```bash
az aks get-credentials
```

Verify:

```bash
kubectl get nodes
```

---

# Module 9: Creating a GKE Cluster

Google Cloud CLI:

```bash
gcloud container clusters create demo-gke
```

Get Credentials:

```bash
gcloud container clusters get-credentials demo-gke
```

Verify:

```bash
kubectl get nodes
```

---

# Module 10: Node Groups

Worker Nodes run your applications.

Names vary:

```text
EKS → Node Groups

AKS → Node Pools

GKE → Node Pools
```

---

Example:

```text
Frontend Nodes

Backend Nodes

Database Nodes
```

Separate workloads.

---

# Module 11: Autoscaling

Cluster Autoscaler adjusts nodes automatically.

Example:

```text
Traffic Increase
      ↓
More Pods Needed
      ↓
More Nodes Created
```

---

Benefits:

✅ Cost Optimization

✅ High Availability

✅ Flexibility

---

# Module 12: Load Balancers

Services:

```yaml
type: LoadBalancer
```

Cloud provider automatically creates:

```text
AWS ELB/ALB
Azure Load Balancer
Google Cloud Load Balancer
```

Traffic reaches cluster.

---

# Module 13: Storage Integration

Each cloud integrates Kubernetes storage.

---

## AWS

```text
EBS
EFS
FSx
```

---

## Azure

```text
Azure Disk

Azure Files
```

---

## GCP

```text
Persistent Disk

Filestore
```

---

PVC:

```yaml
storageClassName: standard
```

creates cloud storage automatically.

---

# Module 14: Ingress Controllers

Popular Cloud Patterns:

---

AWS

```text
AWS Load Balancer Controller
NGINX Ingress
```

---

Azure

```text
Application Gateway Ingress Controller
NGINX Ingress
```

---

Google

```text
GKE Native Ingress
NGINX Ingress
```

---

# Module 15: Identity and Access

Cloud identity integration is critical.

---

AWS

```text
IAM Roles for Service Accounts (IRSA)
```

---

Azure

```text
Azure Workload Identity
```

---

Google

```text
Workload Identity
```

---

Applications securely access cloud resources.

---

# Module 16: Secrets Management

Avoid storing secrets directly.

Use:

```text
AWS Secrets Manager

Azure Key Vault

Google Secret Manager
```

Connected through Kubernetes.

---

Benefits:

✅ Centralized Security

✅ Secret Rotation

✅ Auditing

---

# Module 17: Monitoring

Production clusters require monitoring.

---

AWS

```text
CloudWatch
Prometheus
Grafana
```

---

Azure

```text
Azure Monitor
Prometheus
Grafana
```

---

Google

```text
Cloud Monitoring
Managed Prometheus
Grafana
```

---

# Module 18: Logging

Collect cluster logs.

Tools:

```text
CloudWatch Logs

Azure Log Analytics

Cloud Logging
```

Additionally:

```text
ELK Stack

Loki
```

---

# Module 19: Networking

Cloud Networking Components:

---

AWS

```text
VPC

Subnet

Security Groups
```

---

Azure

```text
Virtual Network

NSG
```

---

Google

```text
VPC

Firewall Rules
```

---

Clusters should use private networking whenever possible.

---

# Module 20: Private Clusters

Public Control Plane:

```text
Internet Accessible
```

---

Private Cluster:

```text
Internal Access Only
```

Production recommendation:

✅ Use Private Clusters.

---

# Module 21: High Availability

Multi-AZ deployment.

Example:

```text
Availability Zone A

Availability Zone B

Availability Zone C
```

Node failure:

```text
Applications continue running.
```

---

# Module 22: Cost Optimization

Common Cost Drivers:

```text
Nodes
Load Balancers
Storage
Network Traffic
Observability
```

Optimize:

✅ Autoscaling

✅ Spot Instances

✅ Right-Sized Pods

✅ Efficient Storage

---

# Module 23: Upgrades

Managed services simplify upgrades.

Control Plane:

```text
Provider Managed
```

Worker Nodes:

```text
Customer Managed
```

Upgrade Process:

```text
Control Plane

Node Pools

Applications
```

---

# Module 24: Security Best Practices

Use:

✅ Private Clusters

✅ Network Policies

✅ RBAC

✅ Workload Identity

✅ Secrets Manager

✅ Node Security

✅ Image Scanning

---

# Module 25: Disaster Recovery

Plan for:

```text
Region Failure
Node Failure
Storage Failure
Cluster Failure
```

Implement:

✅ Backups

✅ Snapshots

✅ Velero

✅ Multi-Region Design

---

# Module 26: Hands-On Lab

Deploy:

```text
Managed Cluster
```

Tasks:

✅ Create Cluster

✅ Create Node Pool

✅ Deploy Application

✅ Configure Storage

✅ Configure LoadBalancer

✅ Configure Ingress

✅ Configure Autoscaling

✅ Configure Monitoring

✅ Configure Logging

✅ Delete Resources

---

# Module 27: Mini Project

Deploy Production Web Application.

Components:

```text
Frontend

Backend

Redis

PostgreSQL
```

Requirements:

✅ Managed Cluster

✅ Autoscaling

✅ Storage

✅ Ingress

✅ Monitoring

✅ Logging

✅ Secrets Management

✅ TLS

✅ Backup Strategy

---

# Production Best Practices

---

## Use Managed Kubernetes

Avoid managing control planes unless required.

---

## Enable Autoscaling

For both:

```text
Pods
Nodes
```

---

## Use Private Networking

Production should avoid unnecessary public exposure.

---

## Use Cloud Identity

Avoid static credentials.

---

## Backup Everything

Including:

```text
PVCs
Cluster Resources
Secrets
```

---

## Monitor Cost

Review:

```text
Compute
Storage
Network
```

regularly.

---

## Automate with IaC

Use:

```text
Terraform

Pulumi

CloudFormation

Bicep
```

---

# Interview Questions

1. What is Managed Kubernetes?
2. Difference between EKS, AKS, and GKE?
3. What responsibilities remain with the customer?
4. What is a Node Group?
5. What is Cluster Autoscaling?
6. How does cloud storage integrate with Kubernetes?
7. What is Workload Identity?
8. How do you manage Secrets securely?
9. What are Private Clusters?
10. How would you perform upgrades?
11. How would you architect a production cluster?
12. How would you optimize Kubernetes costs?

---

# Readiness Checklist

You are ready for Phase 15 if:

- [ ] Created Managed Clusters
- [ ] Configured Node Pools
- [ ] Configured Autoscaling
- [ ] Configured Storage
- [ ] Configured Ingress
- [ ] Used Workload Identity
- [ ] Configured Monitoring
- [ ] Configured Logging
- [ ] Applied Security Best Practices
- [ ] Completed Mini Project

Congratulations.

You now understand how Kubernetes operates in public cloud environments.

Next Phase:

15-Service-Mesh-and-CKA-Preparation.md