# Working with Kubernetes

## Learning Goal

This phase focuses on the daily activities performed by Kubernetes users and administrators.

By the end of this phase, you should be able to:

✅ Write Kubernetes YAML

✅ Create and manage Pods

✅ Understand Pod Lifecycle

✅ Use Labels and Selectors

✅ Understand ReplicaSets

✅ Debug Applications

✅ Execute Commands Inside Containers

✅ View Events and Logs

✅ Work with Multiple Containers Inside a Pod

✅ Apply Production Best Practices

---

# Module 1: Understanding Kubernetes Objects

Everything in Kubernetes is an Object.

Examples:

```text
Pod
Deployment
Service
Namespace
ConfigMap
Secret
Ingress
```

Resources are defined using YAML.

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
  - name: nginx
    image: nginx
```

---

# Module 2: YAML Fundamentals

Every Kubernetes manifest contains:

```yaml
apiVersion:
kind:
metadata:
spec:
```

---

## apiVersion

Tells Kubernetes which API to use.

Examples:

```yaml
apiVersion: v1
```

```yaml
apiVersion: apps/v1
```

---

## kind

Defines resource type.

Examples:

```yaml
kind: Pod
```

```yaml
kind: Deployment
```

```yaml
kind: Service
```

---

## metadata

Stores resource information.

Example:

```yaml
metadata:
  name: nginx
  labels:
    app: web
```

---

## spec

Contains desired state.

Example:

```yaml
spec:
  containers:
  - image: nginx
```

---

# Module 3: Creating Your First Pod

Create file:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx-pod

spec:

  containers:

  - name: nginx

    image: nginx
```

Apply:

```bash
kubectl apply -f pod.yaml
```

Verify:

```bash
kubectl get pods
```

Expected:

```text
nginx-pod Running
```

---

# Module 4: Pod Lifecycle

Kubernetes Pods move through different states.

```text
Pending
  ↓
ContainerCreating
  ↓
Running
  ↓
Succeeded
```

Possible failure states:

```text
Failed
Unknown
CrashLoopBackOff
ImagePullBackOff
```

---

## Check Pod Status

```bash
kubectl get pod
```

Example:

```text
NAME        READY STATUS
nginx-pod   1/1   Running
```

---

# Module 5: Pod Lifecycle Lab

Create Pod:

```bash
kubectl apply -f pod.yaml
```

Delete:

```bash
kubectl delete pod nginx-pod
```

Observe:

```bash
kubectl get pods -w
```

Watch state transitions.

---

# Module 6: Inspecting Pods

---

## Describe Pod

Most important troubleshooting command.

```bash
kubectl describe pod nginx-pod
```

Provides:

- Events
- Image
- Resources
- Status
- Node Assignment

---

## Get Detailed Output

```bash
kubectl get pod -o wide
```

Shows:

- Node
- Pod IP
- Internal Details

---

# Module 7: Logs

Application troubleshooting starts here.

Read logs:

```bash
kubectl logs nginx-pod
```

Stream logs:

```bash
kubectl logs -f nginx-pod
```

Previous logs:

```bash
kubectl logs --previous nginx-pod
```

---

# Module 8: Execute Commands Inside Pods

Pod shell access.

```bash
kubectl exec -it nginx-pod -- bash
```

If bash unavailable:

```bash
kubectl exec -it nginx-pod -- sh
```

Verify:

```bash
hostname
```

Check processes:

```bash
ps aux
```

Check files:

```bash
ls -la
```

---

# Module 9: Multi-Container Pods

Pods can contain multiple containers.

Example:

```text
Pod
├── Application Container
└── Logging Sidecar
```

---

Example:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: multi-pod

spec:

  containers:

  - name: nginx
    image: nginx

  - name: alpine
    image: alpine
    command:
      - sleep
      - "3600"
```

Deploy:

```bash
kubectl apply -f multi-pod.yaml
```

---

# Module 10: Labels

Labels are metadata.

Example:

```yaml
metadata:

  labels:
    app: nginx
    env: dev
    team: platform
```

Used for:

- Filtering
- Services
- Monitoring
- Scheduling

---

## View Labels

```bash
kubectl get pods --show-labels
```

Example:

```text
app=nginx
env=dev
```

---

# Module 11: Selectors

Selectors find resources.

Example:

```yaml
selector:
  app: nginx
```

Relationship:

```text
Service
  ↓
Selector
  ↓
Label
  ↓
Pod
```

Without selectors, Services cannot find Pods.

---

# Module 12: Label Operations

Create:

```bash
kubectl label pod nginx-pod env=dev
```

Verify:

```bash
kubectl get pod --show-labels
```

Filter:

```bash
kubectl get pods -l env=dev
```

Multiple Labels:

```bash
kubectl get pods -l app=nginx,env=dev
```

---

# Module 13: ReplicaSets

ReplicaSet maintains Pod count.

Desired:

```text
3 Pods
```

Actual:

```text
2 Pods
```

ReplicaSet creates:

```text
1 New Pod
```

---

## ReplicaSet Example

```yaml
apiVersion: apps/v1

kind: ReplicaSet

metadata:
  name: nginx-rs

spec:

  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:

    metadata:
      labels:
        app: nginx

    spec:

      containers:

      - name: nginx
        image: nginx
```

Apply:

```bash
kubectl apply -f replicaset.yaml
```

---

# Module 14: Understanding Owners

Observe:

```bash
kubectl describe pod pod_name
```

Owner:

```text
ReplicaSet
```

When using Deployments:

```text
Deployment
 ↓
ReplicaSet
 ↓
Pods
```

This hierarchy is important.

---

# Module 15: Resource Requests and Limits

Prevent resource abuse.

Example:

```yaml
resources:

  requests:
    cpu: "250m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "256Mi"
```

---

## Why Important?

Without limits:

```text
Container Consumes Entire Node
```

With limits:

```text
Container Restricted
```

Useful for production clusters.

---

# Module 16: Health Checks

Applications fail.

Kubernetes can detect failure automatically.

---

## Liveness Probe

Checks:

```text
Is Application Alive?
```

Example:

```yaml
livenessProbe:

  httpGet:
    path: /
    port: 80
```

---

## Readiness Probe

Checks:

```text
Can Application Receive Traffic?
```

Example:

```yaml
readinessProbe:

  httpGet:
    path: /
    port: 80
```

---

# Module 17: Kubernetes Events

Cluster events provide troubleshooting information.

View:

```bash
kubectl get events
```

Sorted:

```bash
kubectl get events \
--sort-by=.lastTimestamp
```

Common events:

```text
Scheduled
Pulled
Created
Started
Failed
```

---

# Module 18: Common Errors

---

## ImagePullBackOff

Cause:

```text
Invalid Image Name
```

Check:

```bash
kubectl describe pod
```

---

## CrashLoopBackOff

Cause:

```text
Application Crashing
```

Check:

```bash
kubectl logs pod_name
```

---

## Pending

Possible:

```text
Insufficient CPU
Insufficient Memory
Node Issue
```

Check:

```bash
kubectl describe pod
```

---

# Module 19: Hands-On Lab

Create:

```text
Namespace
  ↓
ReplicaSet
  ↓
3 Nginx Pods
```

Tasks:

✅ Apply Labels

✅ Use Selectors

✅ Scale ReplicaSet

✅ View Logs

✅ Execute Commands

✅ Delete One Pod

✅ Observe Self-Healing

✅ Examine Events

---

# Module 20: Production Best Practices

---

## Avoid Direct Pods

Bad:

```yaml
kind: Pod
```

Good:

```yaml
kind: Deployment
```

---

## Always Use Labels

Example:

```yaml
labels:
  app: frontend
  env: prod
```

---

## Define Resource Limits

Never leave unlimited.

---

## Add Health Checks

Use:

```yaml
livenessProbe
readinessProbe
```

---

## Use Namespaces

Never run everything in:

```text
default
```

---

# Interview Questions

1. What is a Pod?
2. What is Pod Lifecycle?
3. Difference between Pod and Container?
4. Explain ReplicaSet.
5. Why use Labels?
6. How do Selectors work?
7. Explain kubectl exec.
8. What is CrashLoopBackOff?
9. What is ImagePullBackOff?
10. Explain Readiness vs Liveness Probe.

---

# Readiness Checklist

You are ready for Phase 4 if you can:

- [ ] Write YAML
- [ ] Create Pods
- [ ] Debug Pods
- [ ] Work with Labels
- [ ] Work with Selectors
- [ ] Create ReplicaSets
- [ ] Understand Events
- [ ] Use kubectl logs
- [ ] Use kubectl exec
- [ ] Configure Probes

Congratulations.

You now understand the resource foundations used by all Kubernetes workloads.

Next Phase:

04-Deployments-and-ReplicaSets.md