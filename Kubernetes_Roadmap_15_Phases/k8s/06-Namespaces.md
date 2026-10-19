# Namespaces

## Learning Goal

As Kubernetes clusters grow, managing resources becomes difficult.

Imagine a cluster hosting:

```text
Development Team
QA Team
Staging Team
Production Team
```

Without separation, resources become difficult to manage.

Namespaces solve this problem.

By the end of this phase, you should be able to:

✅ Understand Namespace Architecture

✅ Create and Manage Namespaces

✅ Deploy Applications into Namespaces

✅ Isolate Workloads

✅ Use Resource Quotas

✅ Configure Limit Ranges

✅ Apply Namespace-Level Policies

✅ Troubleshoot Namespace Issues

✅ Implement Multi-Environment Deployments

✅ Follow Production Best Practices

---

# Module 1: What Is a Namespace?

A Namespace is a logical partition inside a Kubernetes cluster.

Think of it as a:

```text
Virtual Cluster
```

inside the physical cluster.

Example:

```text
Kubernetes Cluster

├── dev
├── qa
├── staging
└── production
```

Each Namespace can contain:

- Pods
- Deployments
- Services
- ConfigMaps
- Secrets
- Jobs

---

# Module 2: Why Namespaces Exist?

Without Namespaces:

```text
frontend
backend
database
```

Everything exists together.

Problems:

- Name conflicts
- Resource contention
- Poor organization
- Difficult security management

---

Example:

Team A:

```text
frontend
```

Team B:

```text
frontend
```

Conflict occurs.

---

Using Namespaces:

```text
dev/frontend

prod/frontend
```

No conflict.

---

# Module 3: Built-In Namespaces

View Namespaces:

```bash
kubectl get ns
```

Typical output:

```text
default
kube-system
kube-public
kube-node-lease
```

---

## default

Default workspace.

If Namespace is not specified:

```bash
kubectl apply -f app.yaml
```

Resources go here.

---

## kube-system

Contains Kubernetes system components:

```text
CoreDNS
kube-proxy
metrics-server
network plugins
```

Never delete resources here.

---

## kube-public

Public cluster information.

Rarely modified.

---

## kube-node-lease

Stores node heartbeat leases.

Improves cluster scalability.

---

# Module 4: Creating Namespaces

Create:

```bash
kubectl create namespace dev
```

or

```bash
kubectl create ns dev
```

Verify:

```bash
kubectl get ns
```

---

# Module 5: Namespace YAML

Namespaces can also be created declaratively.

File:

```yaml
apiVersion: v1

kind: Namespace

metadata:
  name: dev
```

Apply:

```bash
kubectl apply -f namespace.yaml
```

---

# Module 6: Deploying into a Namespace

Example:

```bash
kubectl apply -f deployment.yaml \
-n dev
```

Verify:

```bash
kubectl get pods -n dev
```

---

Without Namespace:

```bash
kubectl get pods
```

Only default namespace resources appear.

---

# Module 7: Namespace Isolation

Consider:

```text
Namespace: dev

frontend
backend
database
```

Another Namespace:

```text
Namespace: prod

frontend
backend
database
```

Resources remain logically separated.

---

Namespaces isolate:

✅ Names

✅ Resource Limits

✅ Access Control

✅ Policies

---

Namespaces do NOT isolate:

❌ Node Resources Automatically

❌ Network Traffic Automatically

❌ Cluster Resources

These require additional configurations.

---

# Module 8: Switching Namespace Context

Current Namespace:

```bash
kubectl config view
```

---

Set permanent namespace:

```bash
kubectl config set-context \
--current \
--namespace=dev
```

Now:

```bash
kubectl get pods
```

Automatically queries:

```text
dev
```

Namespace.

---

# Module 9: Viewing Resources by Namespace

All Pods:

```bash
kubectl get pods -A
```

or

```bash
kubectl get pods --all-namespaces
```

---

Specific Namespace:

```bash
kubectl get pods -n dev
```

---

Services:

```bash
kubectl get svc -n prod
```

---

Deployments:

```bash
kubectl get deployment -n qa
```

---

# Module 10: Namespace Communication

Namespaces can communicate.

Example:

```text
Namespace A
     ↓
Namespace B
```

Communication allowed by default.

---

DNS format:

```text
service-name.namespace.svc.cluster.local
```

Example:

```text
mysql.dev.svc.cluster.local
```

---

# Module 11: Multi-Environment Design

Common Structure:

```text
dev
qa
staging
production
```

---

Development

```text
Low Resources
Frequent Changes
```

---

QA

```text
Testing Environment
```

---

Staging

```text
Production-like Environment
```

---

Production

```text
Customer Traffic
```

---

# Module 12: Resource Quotas

Problem:

One Team uses:

```text
100 Pods
```

Another Team:

```text
0 Resources Left
```

---

Solution:

Resource Quotas.

---

Example:

```yaml
apiVersion: v1

kind: ResourceQuota

metadata:
  name: team-a-quota

spec:

  hard:

    pods: "20"

    requests.cpu: "10"

    requests.memory: 20Gi

    limits.cpu: "20"

    limits.memory: 40Gi
```

Apply:

```bash
kubectl apply -f quota.yaml
```

---

# Module 13: View Resource Quotas

```bash
kubectl get resourcequota -n dev
```

Details:

```bash
kubectl describe resourcequota
```

---

# Module 14: Limit Ranges

LimitRanges define:

```text
Default CPU
Default Memory
Maximum CPU
Maximum Memory
```

---

Example:

```yaml
apiVersion: v1

kind: LimitRange

metadata:
  name: memory-limit

spec:

  limits:

  - default:

      memory: 512Mi

      cpu: 500m

    defaultRequest:

      memory: 256Mi

      cpu: 250m

    type: Container
```

---

Benefits:

✅ Prevent resource abuse

✅ Enforce standards

✅ Improve cluster stability

---

# Module 15: Namespace Labels

Namespaces can be labeled.

Example:

```bash
kubectl label namespace dev \
environment=development
```

Verify:

```bash
kubectl get ns --show-labels
```

Common Labels:

```text
environment=production

team=platform

department=payments
```

---

# Module 16: Namespace Annotations

Store metadata.

Example:

```bash
kubectl annotate namespace prod \
owner=platform-team
```

Verify:

```bash
kubectl describe namespace prod
```

---

# Module 17: RBAC and Namespaces

Access control often starts with Namespaces.

Developer Access:

```text
dev Namespace Only
```

Production Access:

```text
Restricted
```

Using:

```text
Roles
RoleBindings
```

Cluster-wide permissions should be minimized.

---

# Module 18: Troubleshooting Namespace Issues

---

## Resource Not Found

Check Namespace:

```bash
kubectl get pods -A
```

---

## Wrong Namespace

Check:

```bash
kubectl config view
```

Current Context:

```bash
kubectl config current-context
```

---

## Service Not Reachable

Verify DNS:

```bash
service.namespace.svc.cluster.local
```

---

## Resource Quota Exceeded

Check:

```bash
kubectl describe quota
```

Common Error:

```text
Exceeded Resource Quota
```

---

# Module 19: Hands-On Lab

Create:

```text
dev
qa
prod
```

Namespaces.

Deploy:

```text
frontend
backend
```

Into Each.

Tasks:

✅ Create Namespaces

✅ Deploy Applications

✅ Configure Resource Quotas

✅ Configure LimitRanges

✅ Switch Contexts

✅ Validate Isolation

✅ Test DNS Between Namespaces

✅ View Resources

---

# Module 20: Mini Project

Build:

```text
Cluster
│
├── dev
│   ├── frontend
│   └── backend
│
├── qa
│   ├── frontend
│   └── backend
│
└── production
    ├── frontend
    └── backend
```

Requirements:

✅ Namespace Isolation

✅ Resource Quotas

✅ LimitRanges

✅ Labels

✅ Annotations

✅ DNS Validation

✅ RBAC Planning

---

# Production Best Practices

---

## Avoid Using Default Namespace

Bad:

```text
default
```

Production:

```text
dev
qa
staging
prod
```

---

## Use Naming Standards

Examples:

```text
payments-dev

payments-prod

platform-staging
```

---

## Apply Resource Quotas

Protect shared clusters.

---

## Define LimitRanges

Prevent resource misuse.

---

## Label Namespaces

Example:

```text
team=sre

environment=prod
```

---

## Use RBAC

Restrict access.

Follow:

```text
Least Privilege Principle
```

---

# Interview Questions

1. What is a Namespace?
2. Why do Namespaces exist?
3. What are the default Namespaces?
4. How do you create a Namespace?
5. Explain ResourceQuota.
6. Explain LimitRange.
7. Do Namespaces provide network isolation?
8. How does DNS work across Namespaces?
9. How do you switch Namespaces?
10. What are Namespace labels used for?
11. How does RBAC relate to Namespaces?
12. What are best practices for Namespace design?

---

# Readiness Checklist

You are ready for Phase 7 if:

- [ ] Created Namespaces
- [ ] Used Namespace Contexts
- [ ] Deployed Applications
- [ ] Configured Resource Quotas
- [ ] Configured LimitRanges
- [ ] Labeled Namespaces
- [ ] Annotated Namespaces
- [ ] Tested DNS
- [ ] Planned Multi-Environment Design
- [ ] Applied Best Practices

Congratulations.

You now understand Kubernetes multi-tenancy and environment separation.

Next Phase:

07-ConfigMaps-and-Secrets.md