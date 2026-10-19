# StatefulSets

## Learning Goal

Deployments work well for:

```text
Frontend Applications
REST APIs
Microservices
Stateless Workloads
```

However, they are not ideal for:

```text
MySQL
PostgreSQL
MongoDB
Kafka
Zookeeper
Elasticsearch
Redis Cluster
```

These applications require:

✅ Stable Network Identity

✅ Persistent Storage

✅ Ordered Deployment

✅ Ordered Scaling

✅ Ordered Termination

Kubernetes provides:

```text
StatefulSets
```

for stateful applications.

By the end of this phase, you should be able to:

✅ Understand Stateful Workloads

✅ Understand StatefulSet Architecture

✅ Deploy Stateful Applications

✅ Manage Persistent Storage

✅ Understand Headless Services

✅ Scale StatefulSets

✅ Perform Rolling Updates

✅ Troubleshoot Stateful Workloads

✅ Design Production Database Deployments

---

# Module 1: Stateless vs Stateful Applications

---

## Stateless Applications

Examples:

```text
Nginx
Frontend
Node.js API
Java REST API
```

Characteristics:

```text
Any Pod Can Handle Traffic
No Unique Identity
No Local Data Dependency
```

Example:

```text
frontend-abc123
frontend-xyz456
frontend-qwe789
```

Any Pod may disappear.

No problem.

---

## Stateful Applications

Examples:

```text
MySQL
MongoDB
Kafka
PostgreSQL
```

Requirements:

```text
Persistent Data
Stable Hostnames
Stable Storage
Predictable Startup
```

Replacing Pods randomly can cause issues.

---

# Module 2: Why Deployments Fail for Databases

Deployment creates Pods:

```text
mysql-a123

mysql-b456

mysql-c789
```

Delete a Pod:

```text
mysql-a123
```

Replacement:

```text
mysql-z999
```

Problems:

❌ Different Name

❌ Different Storage

❌ Broken Replication

❌ Lost Identity

---

# Module 3: What is a StatefulSet?

StatefulSet guarantees:

```text
Stable Pod Identity

Stable Storage

Ordered Operations
```

StatefulSet Pods receive predictable names.

Example:

```text
mysql-0
mysql-1
mysql-2
```

These names never change.

---

# Module 4: StatefulSet Architecture

```text
StatefulSet

     ↓

mysql-0
mysql-1
mysql-2

     ↓

PVC-0
PVC-1
PVC-2

     ↓

Persistent Volumes
```

Each Pod owns its own storage.

---

# Module 5: StatefulSet Features

---

## Stable Network Identity

Pod Names:

```text
mysql-0
mysql-1
mysql-2
```

Remain consistent.

---

## Stable Storage

Each Pod gets:

```text
Dedicated PVC
```

Storage persists.

---

## Ordered Deployment

Creation sequence:

```text
mysql-0

then

mysql-1

then

mysql-2
```

---

## Ordered Deletion

Deletion sequence:

```text
mysql-2

mysql-1

mysql-0
```

Reverse order.

---

# Module 6: Headless Service

StatefulSets require:

```text
Headless Service
```

Why?

Normal Service:

```text
Load Balancing
```

Stateful applications need:

```text
Direct Pod Access
```

---

Example:

```yaml
apiVersion: v1

kind: Service

metadata:
  name: mysql

spec:

  clusterIP: None

  selector:
    app: mysql
```

Notice:

```yaml
clusterIP: None
```

This creates a Headless Service.

---

# Module 7: DNS Records

StatefulSet Pods receive DNS names.

Example:

```text
mysql-0.mysql.default.svc.cluster.local

mysql-1.mysql.default.svc.cluster.local

mysql-2.mysql.default.svc.cluster.local
```

Applications can communicate directly.

---

# Module 8: Creating a StatefulSet

Example:

```yaml
apiVersion: apps/v1

kind: StatefulSet

metadata:
  name: mysql

spec:

  serviceName: mysql

  replicas: 3

  selector:

    matchLabels:
      app: mysql

  template:

    metadata:

      labels:
        app: mysql

    spec:

      containers:

      - name: mysql

        image: mysql:8
```

Apply:

```bash
kubectl apply -f statefulset.yaml
```

---

# Module 9: Verify StatefulSet

View:

```bash
kubectl get statefulset
```

Output:

```text
mysql
```

View Pods:

```bash
kubectl get pods
```

Example:

```text
mysql-0

mysql-1

mysql-2
```

---

# Module 10: Scaling StatefulSets

Increase replicas:

```bash
kubectl scale statefulset mysql \
--replicas=5
```

Sequence:

```text
mysql-3
mysql-4
```

created in order.

---

Reduce replicas:

```bash
kubectl scale statefulset mysql \
--replicas=2
```

Deleted:

```text
mysql-4
mysql-3
```

Order preserved.

---

# Module 11: Persistent Volume Claim Templates

StatefulSets create storage automatically.

Example:

```yaml
volumeClaimTemplates:

- metadata:

    name: mysql-storage

  spec:

    accessModes:
      - ReadWriteOnce

    resources:

      requests:

        storage: 10Gi
```

Each Pod receives:

```text
mysql-storage-mysql-0

mysql-storage-mysql-1

mysql-storage-mysql-2
```

---

# Module 12: PVC Behavior

Delete Pod:

```bash
kubectl delete pod mysql-0
```

Kubernetes recreates:

```text
mysql-0
```

Same PVC reused.

Data remains.

---

# Module 13: Rolling Updates

Update MySQL:

```yaml
image: mysql:8.1
```

Apply:

```bash
kubectl apply -f statefulset.yaml
```

Update Order:

```text
mysql-2

mysql-1

mysql-0
```

One Pod at a time.

---

# Module 14: Update Strategies

---

## RollingUpdate

Default.

Pods updated sequentially.

Safe.

---

## OnDelete

Pods update only after manual restart.

Example:

```yaml
updateStrategy:

  type: OnDelete
```

Useful for databases.

---

# Module 15: Pod Identity

Check hostname:

```bash
kubectl exec mysql-0 -- hostname
```

Output:

```text
mysql-0
```

Always stable.

Even after restart.

---

# Module 16: StatefulSet Storage Layout

Example:

```text
mysql-0
   ↓
PVC-0
   ↓
PV-0

mysql-1
   ↓
PVC-1
   ↓
PV-1
```

Storage never shared accidentally.

---

# Module 17: Common Stateful Workloads

---

## MySQL

Requires:

```text
Persistent Storage
Stable Identity
```

Perfect for StatefulSet.

---

## PostgreSQL

Common Production Pattern.

---

## MongoDB

Replica Set Members need stable names.

---

## Kafka

Brokers require stable IDs.

---

## Elasticsearch

Cluster nodes depend on identity.

---

# Module 18: Troubleshooting StatefulSets

---

## Pod Stuck Pending

Check:

```bash
kubectl describe pod pod-name
```

Likely:

```text
PVC Issue
Storage Issue
```

---

## PVC Pending

Check:

```bash
kubectl get pvc
```

Possible:

```text
No Matching StorageClass
```

---

## DNS Not Working

Verify:

```bash
kubectl get svc
```

Headless Service required.

---

Check:

```yaml
clusterIP: None
```

---

## Pod Startup Order Problems

Review events:

```bash
kubectl describe statefulset mysql
```

---

# Module 19: Disaster Recovery

Storage survives Pod deletion.

Storage does NOT automatically survive:

```text
Disk Failure
Cloud Failure
Region Failure
```

Use:

```text
Snapshots
Backups
Replication
```

Examples:

```text
Velero
AWS EBS Snapshot
Azure Backup
GCP Snapshots
```

---

# Module 20: Hands-On Lab

Deploy:

```text
Headless Service

StatefulSet

PVC Templates
```

Tasks:

✅ Create StatefulSet

✅ Verify Hostnames

✅ Verify DNS

✅ Verify PVC Creation

✅ Write Data

✅ Delete Pod

✅ Confirm Data Persists

✅ Scale StatefulSet

✅ Perform Rolling Update

✅ Verify Ordered Operations

---

# Mini Project

Deploy:

```text
MySQL Cluster
```

Requirements:

✅ StatefulSet

✅ Headless Service

✅ Persistent Storage

✅ 3 Replicas

✅ Dynamic Provisioning

✅ Rolling Update

✅ Backup Strategy

---

# Production Best Practices

---

## Use StatefulSets for Databases

Do NOT use:

```yaml
kind: Deployment
```

for production databases.

---

## Use Dedicated Storage

Separate PVC per replica.

---

## Use Backups

Storage alone is not backup.

Implement:

```text
Snapshots
Replication
Offsite Backup
```

---

## Monitor PVC Usage

Track:

```text
Capacity
Growth
Latency
```

---

## Use Storage Classes

Prefer:

```text
Dynamic Provisioning
```

---

## Test Recovery

Practice:

```text
Pod Failure
Node Failure
Storage Failure
```

Recovery procedures.

---

# StatefulSet vs Deployment

| Feature | Deployment | StatefulSet |
|----------|------------|------------|
| Stable Names | No | Yes |
| Ordered Startup | No | Yes |
| Ordered Shutdown | No | Yes |
| Stable DNS | No | Yes |
| Dedicated Storage | No | Yes |
| Database Friendly | No | Yes |
| Web App Friendly | Yes | Yes |

---

# Interview Questions

1. What is a StatefulSet?
2. Why not use Deployments for databases?
3. What is a Headless Service?
4. Why does StatefulSet need a Headless Service?
5. Explain Stable Network Identity.
6. Explain volumeClaimTemplates.
7. How does StatefulSet scaling work?
8. How do StatefulSet updates work?
9. What is OnDelete strategy?
10. How is storage preserved?
11. What workloads should use StatefulSets?
12. Difference between Deployment and StatefulSet?

---

# Readiness Checklist

You are ready for Phase 12 if:

- [ ] Created StatefulSets
- [ ] Created Headless Services
- [ ] Verified Stable DNS
- [ ] Verified Stable Hostnames
- [ ] Used volumeClaimTemplates
- [ ] Scaled StatefulSets
- [ ] Performed Rolling Updates
- [ ] Tested Data Persistence
- [ ] Troubleshot Storage Issues
- [ ] Completed Mini Project

Congratulations.

You now understand Kubernetes stateful workloads and database deployment patterns.

Next Phase:

12-Operators-and-CRDs.md