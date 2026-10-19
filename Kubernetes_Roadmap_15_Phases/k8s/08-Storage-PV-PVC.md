# Storage, Persistent Volumes (PV), and Persistent Volume Claims (PVC)

## Learning Goal

Containers are ephemeral.

This means:

```text
Container Deleted
            ↓
Data Lost
```

For stateless applications:

```text
Frontend
API Gateway
Web Servers
```

This is acceptable.

For stateful applications:

```text
MySQL
PostgreSQL
MongoDB
Redis
Kafka
Elasticsearch
```

Data persistence is critical.

Kubernetes provides a storage architecture that enables persistent data.

By the end of this phase, you should be able to:

✅ Understand Ephemeral Storage

✅ Use Volumes

✅ Create Persistent Volumes

✅ Create Persistent Volume Claims

✅ Use Storage Classes

✅ Configure Dynamic Provisioning

✅ Mount Storage into Pods

✅ Understand Access Modes

✅ Troubleshoot Storage Issues

✅ Design Production Storage Architectures

---

# Module 1: Why Storage Matters

Consider a MySQL Pod.

```text
MySQL Pod
      ↓
Database Files
```

Data stored inside container.

Delete Pod:

```text
Pod Deleted
```

Result:

```text
Database Lost
```

Not acceptable.

---

## Solution

Persist storage outside the Pod.

```text
Pod
  ↓
Persistent Storage
```

Pod may disappear.

Data remains.

---

# Module 2: Ephemeral Storage

Every Pod has temporary storage.

Example:

```text
Container Filesystem
```

Advantages:

✅ Fast

✅ Easy

Drawbacks:

❌ Lost on Pod deletion

❌ Not suitable for databases

---

# Module 3: Kubernetes Storage Architecture

Storage Flow:

```text
Pod
 ↓
PVC
 ↓
PV
 ↓
Physical Storage
```

Example:

```text
Pod
 ↓
PersistentVolumeClaim
 ↓
PersistentVolume
 ↓
AWS EBS Disk
```

---

# Module 4: What is a Volume?

Volumes provide storage inside Pods.

Example:

```yaml
volumes:

- name: app-storage
```

Mounted into:

```yaml
volumeMounts:

- name: app-storage
```

Volumes survive:

```text
Container Restart
```

but not necessarily:

```text
Pod Removal
```

depending on volume type.

---

# Module 5: emptyDir Volume

Temporary storage.

Example:

```yaml
volumes:

- name: cache-volume

  emptyDir: {}
```

Characteristics:

✅ Shared Between Containers

✅ Exists While Pod Exists

❌ Deleted With Pod

---

Common Uses:

```text
Caching
Scratch Space
Temporary Files
```

---

# Module 6: hostPath Volume

Mount Node Filesystem.

Example:

```yaml
volumes:

- name: host-storage

  hostPath:
    path: /data
```

Pod gains access to:

```text
Node Disk
```

---

Advantages:

✅ Simple

Disadvantages:

❌ Not Portable

❌ Not Suitable For Production

---

# Module 7: Persistent Volumes (PV)

A Persistent Volume (PV) is cluster storage.

Created by:

```text
Administrator
```

or

```text
Dynamic Provisioner
```

Example:

```yaml
apiVersion: v1

kind: PersistentVolume
```

Think:

```text
Storage Resource
```

inside the cluster.

---

# Module 8: Persistent Volume Example

```yaml
apiVersion: v1

kind: PersistentVolume

metadata:
  name: mysql-pv

spec:

  capacity:
    storage: 5Gi

  accessModes:

  - ReadWriteOnce

  hostPath:
    path: /data/mysql
```

Apply:

```bash
kubectl apply -f pv.yaml
```

Verify:

```bash
kubectl get pv
```

---

# Module 9: Persistent Volume Claims (PVC)

Applications do NOT use PVs directly.

Instead:

```text
Pod
 ↓
PVC
 ↓
PV
```

PVC acts as a request for storage.

Think:

```text
Storage Request
```

---

# Module 10: PVC Example

```yaml
apiVersion: v1

kind: PersistentVolumeClaim

metadata:
  name: mysql-pvc

spec:

  accessModes:

  - ReadWriteOnce

  resources:

    requests:

      storage: 2Gi
```

Apply:

```bash
kubectl apply -f pvc.yaml
```

View:

```bash
kubectl get pvc
```

---

# Module 11: PV and PVC Binding

Kubernetes automatically matches:

```text
PVC Request
```

with

```text
Suitable PV
```

Example:

```text
PVC Request: 2Gi

PV Available: 5Gi
```

Result:

```text
Bound
```

Check:

```bash
kubectl get pv
```

Output:

```text
STATUS

Bound
```

---

# Module 12: Using PVC in a Pod

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: app

spec:

  containers:

  - name: app

    image: nginx

    volumeMounts:

    - mountPath: "/data"
      name: app-storage

  volumes:

  - name: app-storage

    persistentVolumeClaim:

      claimName: mysql-pvc
```

Apply:

```bash
kubectl apply -f app.yaml
```

---

# Module 13: Access Modes

Kubernetes supports:

---

## ReadWriteOnce (RWO)

```text
One Node
Read + Write
```

Most common.

Examples:

```text
AWS EBS
Azure Disk
```

---

## ReadOnlyMany (ROX)

```text
Multiple Nodes
Read Only
```

---

## ReadWriteMany (RWX)

```text
Multiple Nodes
Read + Write
```

Examples:

```text
NFS
Azure Files
CephFS
```

---

# Module 14: Reclaim Policies

Defines behavior after PVC deletion.

---

## Retain

```text
PVC Deleted
 ↓
Data Kept
```

Safe for databases.

---

## Delete

```text
PVC Deleted
 ↓
Storage Deleted
```

Common in cloud environments.

---

## Recycle

Deprecated.

Do not use.

---

# Module 15: Storage Classes

Modern Kubernetes uses:

```text
StorageClass
```

instead of manually creating PVs.

StorageClass provides:

```text
Dynamic Provisioning
```

---

# Module 16: Dynamic Provisioning

Traditional:

```text
Admin Creates PV
User Creates PVC
```

Modern:

```text
User Creates PVC
 ↓
PV Auto-Created
```

Much easier.

---

# Module 17: StorageClass Example

```yaml
apiVersion: storage.k8s.io/v1

kind: StorageClass

metadata:
  name: fast-storage

provisioner: kubernetes.io/aws-ebs

parameters:

  type: gp3
```

Apply:

```bash
kubectl apply -f storageclass.yaml
```

---

# Module 18: PVC Using StorageClass

```yaml
apiVersion: v1

kind: PersistentVolumeClaim

metadata:
  name: app-pvc

spec:

  storageClassName: fast-storage

  accessModes:

  - ReadWriteOnce

  resources:

    requests:

      storage: 10Gi
```

Apply:

```bash
kubectl apply -f pvc.yaml
```

PV created automatically.

---

# Module 19: Stateful Application Example

MySQL Deployment:

```text
MySQL Pod
      ↓
PVC
      ↓
PV
```

Delete Pod:

```text
Pod Deleted
```

Data:

```text
Still Exists
```

New Pod starts.

Database remains intact.

---

# Module 20: StatefulSets and Storage

StatefulSets use:

```text
Stable Storage
Stable Identity
```

Each replica receives:

```text
Dedicated PVC
```

Example:

```text
mysql-0
mysql-1
mysql-2
```

Each gets unique storage.

---

# Module 21: Volume Expansion

Many StorageClasses support resizing.

Example:

Before:

```text
10Gi
```

After:

```text
50Gi
```

Update PVC:

```yaml
resources:

  requests:

    storage: 50Gi
```

Apply:

```bash
kubectl apply -f pvc.yaml
```

---

# Module 22: Storage Troubleshooting

---

## PVC Pending

Check:

```bash
kubectl get pvc
```

Possible Cause:

```text
No Matching PV
```

---

Check:

```bash
kubectl describe pvc
```

---

## PV Not Binding

Verify:

```text
AccessMode
Storage Size
StorageClass
```

Match correctly.

---

## Volume Mount Failure

Describe Pod:

```bash
kubectl describe pod pod-name
```

Check Events section.

---

## Node Disk Full

Check:

```bash
kubectl describe node
```

Monitor:

```text
Disk Pressure
```

---

# Module 23: Backup Considerations

Storage Does Not Equal Backup.

Always protect:

```text
MySQL
PostgreSQL
MongoDB
```

Using:

```text
Snapshots
Velero
Cloud Backups
```

---

# Module 24: Hands-On Lab

Create:

```text
PV
PVC
Pod
```

Tasks:

✅ Create Persistent Volume

✅ Create Persistent Volume Claim

✅ Bind PVC to PV

✅ Mount Volume

✅ Write Data

✅ Delete Pod

✅ Create New Pod

✅ Verify Data Persists

✅ Create StorageClass

✅ Test Dynamic Provisioning

---

# Mini Project

Deploy:

```text
Nginx
MySQL
```

Requirements:

Nginx:

```text
Shared Static Files
```

MySQL:

```text
Persistent Database Storage
```

Implement:

✅ PV

✅ PVC

✅ StorageClass

✅ Data Verification

✅ Recovery Testing

---

# Production Best Practices

---

## Use Dynamic Provisioning

Prefer:

```text
StorageClass
```

over manually created PVs.

---

## Use SSD Storage

Examples:

```text
AWS gp3
Azure Premium SSD
GCP SSD Persistent Disk
```

---

## Separate Storage for Databases

Avoid sharing:

```text
MySQL
PostgreSQL
MongoDB
```

storage.

---

## Enable Backup Strategy

Use:

```text
Velero
Snapshots
Database Dumps
```

---

## Monitor Storage

Watch:

```text
Capacity
Latency
IOPS
```

---

## Use StatefulSets

For databases.

Not Deployments.

---

# Interview Questions

1. Why is persistent storage needed?
2. What is a Persistent Volume?
3. What is a PVC?
4. Explain PV-PVC Binding.
5. What is ReadWriteOnce?
6. What is ReadWriteMany?
7. What is a StorageClass?
8. What is Dynamic Provisioning?
9. What happens if a Pod is deleted?
10. Explain Reclaim Policies.
11. Which workloads require persistent storage?
12. How would you troubleshoot a Pending PVC?

---

# Readiness Checklist

You are ready for Phase 9 if:

- [ ] Created Volumes
- [ ] Created Persistent Volumes
- [ ] Created PVCs
- [ ] Mounted Storage
- [ ] Tested Persistence
- [ ] Created StorageClasses
- [ ] Used Dynamic Provisioning
- [ ] Troubleshot Storage Issues
- [ ] Understood Access Modes
- [ ] Completed Mini Project

Congratulations.

You now understand Kubernetes persistent storage architecture.

Next Phase:

09-Ingress.md