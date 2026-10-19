# Operators and Custom Resource Definitions (CRDs)

## Learning Goal

Kubernetes provides built-in resources:

```text
Pod
Deployment
Service
StatefulSet
ConfigMap
Secret
Ingress
```

But what if Kubernetes does not understand a resource you need?

For example:

```text
MySQL Cluster
MongoDB Replica Set
Kafka Cluster
Prometheus Stack
Redis Cluster
```

These are complex systems requiring:

- Backup
- Failover
- Scaling
- Recovery
- Upgrades

Managing all this manually is difficult.

Kubernetes solves this using:

```text
CRDs (Custom Resource Definitions)

Operators
```

By the end of this phase, you should be able to:

✅ Understand CRDs

✅ Understand Operators

✅ Understand the Operator Pattern

✅ Deploy Operators

✅ Manage Custom Resources

✅ Understand Reconciliation Loops

✅ Deploy Real-World Operators

✅ Troubleshoot Operators

✅ Build Operator-Centric Architectures

---

# Module 1: What Problem Do Operators Solve?

Imagine deploying MySQL.

You need to manage:

```text
Deployment
Service
Storage
Backup
Replication
Failover
Upgrades
```

Manually handling these tasks becomes difficult.

---

Example:

```text
MySQL Node Fails
```

Questions:

```text
How do we failover?

How do we restore?

How do we rebuild replicas?
```

Operators automate these actions.

---

# Module 2: What Is a CRD?

CRD stands for:

```text
Custom Resource Definition
```

CRDs extend Kubernetes APIs.

Kubernetes knows:

```text
Deployment
Service
Pod
```

A CRD allows Kubernetes to understand:

```text
MySQLCluster
KafkaCluster
MongoCluster
```

---

# Module 3: Built-In vs Custom Resources

Built-in:

```yaml
kind: Deployment
```

Custom:

```yaml
kind: MySQLCluster
```

or

```yaml
kind: KafkaCluster
```

These resources are added through CRDs.

---

# Module 4: CRD Architecture

```text
Kubernetes API Server
          ↓

     CRD Installed
          ↓

 New Resource Type Created
          ↓

     Custom Resource
```

Example:

```text
apiVersion: database.example.com/v1

kind: MySQLCluster
```

---

# Module 5: Creating a CRD

Example:

```yaml
apiVersion: apiextensions.k8s.io/v1

kind: CustomResourceDefinition

metadata:
  name: databases.company.com

spec:

  group: company.com

  names:

    plural: databases

    singular: database

    kind: Database

  scope: Namespaced

  versions:

  - name: v1

    served: true

    storage: true
```

Apply:

```bash
kubectl apply -f crd.yaml
```

---

# Module 6: Verify CRD

View:

```bash
kubectl get crd
```

Example:

```text
databases.company.com
```

Describe:

```bash
kubectl describe crd databases.company.com
```

---

# Module 7: Creating Custom Resources

Once CRD exists:

```yaml
apiVersion: company.com/v1

kind: Database

metadata:
  name: mysql-prod

spec:

  replicas: 3
```

Apply:

```bash
kubectl apply -f database.yaml
```

View:

```bash
kubectl get databases
```

---

# Module 8: What Is an Operator?

Operator =

```text
Controller
+
Domain Knowledge
+
Automation
```

Operators continuously monitor:

```text
Custom Resource
```

and take actions.

---

Example:

```text
Desired:

MySQL Cluster

Replicas = 3
```

Actual:

```text
Replicas = 2
```

Operator:

```text
Creates New Replica
```

---

# Module 9: Operator Pattern

Normal Kubernetes:

```text
Deployment
        ↓
Controller
        ↓
Pods
```

Operator Pattern:

```text
Custom Resource
          ↓
Operator
          ↓
Deployments
PVCs
Services
StatefulSets
```

Operator becomes an expert administrator.

---

# Module 10: Reconciliation Loop

Core Operator concept.

---

Desired State:

```text
Replicas = 3
```

Actual State:

```text
Replicas = 2
```

Operator detects mismatch.

Executes:

```text
Create Replica
```

Cluster returns to desired state.

---

This process continuously repeats.

---

# Module 11: Popular Operators

Some of the most widely used Operators:

```text
Prometheus Operator

MongoDB Operator

Elastic Operator

Redis Operator

Kafka Operator

ArgoCD Operator
```

---

# Module 12: Prometheus Operator

Traditional Monitoring Deployment:

```text
Deployment
Service
ConfigMap
RBAC
PVC
```

Many YAML files.

---

Operator Approach:

```yaml
kind: Prometheus
```

Operator creates everything automatically.

---

# Module 13: Installing a Prometheus Operator

Using Helm:

```bash
helm install kube-prometheus-stack \
prometheus-community/kube-prometheus-stack
```

Verify:

```bash
kubectl get pods
```

Monitor stack deployed automatically.

---

# Module 14: Operator Lifecycle

Typical flow:

```text
Operator Installed
          ↓

CRD Installed
          ↓

Custom Resource Created
          ↓

Operator Watches Resource
          ↓

Operator Creates Components
```

---

# Module 15: Operator Components

Operator generally manages:

```text
Deployments
StatefulSets
Services
Secrets
ConfigMaps
PVCs
```

Automatically.

---

# Module 16: Database Operator Example

Create:

```yaml
kind: MongoDB
```

Operator automatically creates:

```text
StatefulSets

Storage

Services

Backups

Replication
```

Administrator avoids manual work.

---

# Module 17: Operator SDK

Used to build Operators.

Popular languages:

```text
Go
Ansible
Helm
```

Framework:

```text
Operator SDK
```

Installation:

```bash
operator-sdk version
```

---

# Module 18: Building Custom Operators

Typical Steps:

```text
Create CRD
Create Controller
Watch Resource Events
Implement Reconciliation
Deploy Operator
```

Used by platform engineering teams.

---

# Module 19: Event Watching

Operators watch:

```text
Create
Update
Delete
```

events.

Example:

```text
Database Deleted
```

Operator reacts immediately.

---

# Module 20: Status Fields

CRs often contain status.

Example:

```yaml
status:

  phase: Running

  replicasReady: 3
```

Check:

```bash
kubectl get mysqlcluster
```

---

# Module 21: Operator Benefits

---

## Automation

Repetitive work eliminated.

---

## Consistency

Applications deployed identically.

---

## Reliability

Recovery becomes automated.

---

## Reduced Human Error

Fewer manual operations.

---

## Self-Healing Extensions

Operators extend Kubernetes self-healing.

---

# Module 22: Operator vs Helm

Helm:

```text
Installs Applications
```

Operator:

```text
Manages Application Lifecycle
```

---

Helm:

```text
Install
Upgrade
Rollback
```

Operator:

```text
Monitor
Recover
Backup
Scale
Repair
```

---

Often used together.

---

# Module 23: Troubleshooting Operators

---

## CRD Missing

Check:

```bash
kubectl get crd
```

---

## Operator Not Running

Check:

```bash
kubectl get pods -A
```

---

## Resource Not Reconciling

Check logs:

```bash
kubectl logs operator-pod
```

---

## Status Not Updating

Inspect:

```bash
kubectl describe custom-resource
```

---

# Module 24: Hands-On Lab

Install:

```text
Prometheus Operator
```

Tasks:

✅ Install Operator

✅ Verify CRDs

✅ Create Custom Resource

✅ Observe Reconciliation

✅ Scale Resource

✅ Review Logs

✅ Delete Resource

✅ Observe Cleanup

---

# Module 25: Mini Project

Deploy:

```text
Monitoring Platform
```

Requirements:

✅ Prometheus Operator

✅ Grafana

✅ Persistent Storage

✅ Custom Resources

✅ Automated Upgrades

✅ Automated Recovery

---

# Production Best Practices

---

## Prefer Operators for Databases

Examples:

```text
MongoDB
Kafka
ElasticSearch
```

---

## Monitor Operator Health

Operators become critical infrastructure.

Monitor:

```text
CPU
Memory
Logs
Events
```

---

## Use Stable Operator Versions

Avoid running experimental versions in production.

---

## Backup CRDs

Important cluster metadata.

---

## Read Operator Documentation

Every Operator behaves differently.

---

# CRD vs Native Resource

| Feature | Deployment | CRD |
|----------|------------|-----|
| Built Into Kubernetes | Yes | No |
| Customizable | Limited | High |
| Domain Knowledge | No | Yes |
| Requires Controller | Existing | Usually |

---

# Helm vs Operator

| Feature | Helm | Operator |
|----------|------|-----------|
| Deployment | Yes | Yes |
| Lifecycle Management | Limited | Advanced |
| Self Healing Logic | No | Yes |
| Automated Recovery | No | Yes |
| Backups | No | Often |
| Failover | No | Often |

---

# Interview Questions

1. What is a CRD?
2. Why do CRDs exist?
3. What is an Operator?
4. What problem do Operators solve?
5. Explain Reconciliation Loop.
6. Explain Operator Pattern.
7. Difference between Helm and Operators?
8. How does Prometheus Operator work?
9. What is Operator SDK?
10. Why are Operators useful for databases?
11. What are Custom Resources?
12. How would you troubleshoot an Operator?

---

# Readiness Checklist

You are ready for Phase 13 if:

- [ ] Understand CRDs
- [ ] Understand Custom Resources
- [ ] Understand Operators
- [ ] Installed an Operator
- [ ] Observed Reconciliation
- [ ] Viewed Operator Logs
- [ ] Used Helm with Operators
- [ ] Compared Helm vs Operators
- [ ] Completed Hands-On Lab
- [ ] Completed Mini Project

Congratulations.

You now understand how Kubernetes extends itself through CRDs and Operators.

Next Phase:

13-Kubernetes-Security.md