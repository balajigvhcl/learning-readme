# Kubernetes Fundamentals

## Learning Goal

Build a solid Kubernetes foundation.

By the end of this phase, you should understand:

✅ Kubernetes Architecture

✅ Control Plane Components

✅ Worker Nodes

✅ Pods

✅ Deployments

✅ Services

✅ Namespaces

✅ kubectl

✅ Minikube

✅ Kind

✅ Basic Troubleshooting

---

# Module 1: Why Kubernetes?

---

## The Problem

Imagine you have:

```text
Frontend Container
Backend Container
MySQL Container
Redis Container
```

Running them manually:

```bash
docker run ...
docker run ...
docker run ...
docker run ...
```

Questions:

- What if a container crashes?
- What if traffic increases?
- What if a server dies?
- How do upgrades happen?
- How do containers find each other?

These challenges are solved by Kubernetes.

---

## What Kubernetes Provides

### Self-Healing

Container crashes:

```text
Pod Failed
```

Kubernetes:

```text
Creates New Pod
```

---

### Scaling

Before:

```text
1 Pod
```

After:

```text
10 Pods
```

Using:

```bash
kubectl scale
```

---

### Service Discovery

Applications communicate using service names.

Example:

```text
mysql-service
redis-service
```

instead of IP addresses.

---

### Rolling Updates

Deploy new version.

Old pods terminate gradually.

New pods come online gradually.

Users face no downtime.

---

# Module 2: Kubernetes Architecture

## High-Level Architecture

```text
                Kubernetes Cluster

+--------------------------------------+

          CONTROL PLANE

+--------------------------------------+
| API Server                           |
| Scheduler                            |
| Controller Manager                   |
| etcd                                |
+--------------------------------------+

                 |
                 |
                 v

+--------------------------------------+
| Worker Node 1                        |
| kubelet                              |
| kube-proxy                           |
| Pods                                 |
+--------------------------------------+

+--------------------------------------+
| Worker Node 2                        |
| kubelet                              |
| kube-proxy                           |
| Pods                                 |
+--------------------------------------+
```

---

# Module 3: Control Plane Components

The Control Plane is the brain of Kubernetes.

---

## API Server

Most important component.

All communication goes through it.

Example:

```bash
kubectl get pods
```

Flow:

```text
kubectl
   |
API Server
   |
Cluster
```

---

## Scheduler

Decides where Pods run.

Example:

```text
Node-1
20% CPU
```

```text
Node-2
90% CPU
```

Scheduler chooses:

```text
Node-1
```

---

## Controller Manager

Ensures actual state equals desired state.

Desired:

```text
3 Pods
```

Actual:

```text
2 Pods
```

Controller creates:

```text
1 Additional Pod
```

---

## etcd

Kubernetes database.

Stores:

- Pods
- Deployments
- Services
- Secrets
- ConfigMaps

Think of etcd as:

```text
Cluster Source of Truth
```

---

# Module 4: Worker Nodes

Worker Nodes run applications.

Components:

---

## Kubelet

Node Agent.

Responsibilities:

- Communicates with API Server
- Creates Pods
- Reports Health

---

## Container Runtime

Runs containers.

Examples:

```text
containerd
CRI-O
```

---

## kube-proxy

Handles networking.

Responsibilities:

- Routing
- Service Load Balancing
- Pod Connectivity

---

# Module 5: Installing Kubernetes

## Option 1: Minikube

Install:

```bash
minikube start
```

Verify:

```bash
kubectl get nodes
```

Expected:

```text
Ready
```

---

## Option 2: Kind

Kind = Kubernetes in Docker

Create Cluster:

```bash
kind create cluster
```

Verify:

```bash
kubectl cluster-info
```

---

# Module 6: kubectl

kubectl is Kubernetes CLI.

---

## View Nodes

```bash
kubectl get nodes
```

---

## View Pods

```bash
kubectl get pods
```

---

## View Services

```bash
kubectl get svc
```

---

## View Everything

```bash
kubectl get all
```

---

# Module 7: Your First Pod

Pods are the smallest deployable unit.

---

## Create Pod Definition

File:

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

Save:

```text
pod.yaml
```

Deploy:

```bash
kubectl apply -f pod.yaml
```

---

## Verify

```bash
kubectl get pods
```

Expected:

```text
nginx-pod
Running
```

---

# Module 8: Understanding Pods

Pod contains:

```text
One or More Containers
```

Common structure:

```text
Pod
 └── App Container
```

Advanced:

```text
Pod
 ├── App Container
 └── Sidecar Container
```

---

## Pod Lifecycle

```text
Pending
Running
Succeeded
Failed
Unknown
```

Check:

```bash
kubectl get pod
```

---

# Module 9: Pod Operations

---

## Logs

```bash
kubectl logs nginx-pod
```

---

## Enter Pod

```bash
kubectl exec -it nginx-pod -- bash
```

---

## Describe Pod

```bash
kubectl describe pod nginx-pod
```

---

## Delete Pod

```bash
kubectl delete pod nginx-pod
```

---

# Module 10: Deployments

Real applications use Deployments.

Not Pods directly.

---

## Why Deployment?

Pod dies:

```text
Application Gone
```

Deployment:

```text
Pod Dies
   ↓
New Pod Created
```

---

## Deployment Example

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: nginx-deployment

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
kubectl apply -f deployment.yaml
```

---

# Module 11: Scaling

View deployments:

```bash
kubectl get deployments
```

Scale:

```bash
kubectl scale deployment nginx-deployment \
--replicas=5
```

Verify:

```bash
kubectl get pods
```

Expected:

```text
5 Pods
```

---

# Module 12: Services

Problem:

Pods receive dynamic IPs.

Example:

```text
10.0.1.20
```

Delete Pod:

```text
10.0.1.22
```

New IP.

---

## Solution

Service.

Provides:

```text
Stable Endpoint
```

---

# Module 13: Service Types

---

## ClusterIP

Default.

Internal traffic only.

```yaml
type: ClusterIP
```

---

## NodePort

Exposes service externally.

```yaml
type: NodePort
```

Access:

```text
NodeIP:30080
```

---

## LoadBalancer

Used in cloud.

```yaml
type: LoadBalancer
```

Cloud creates external IP.

---

# Module 14: Namespaces

Logical isolation.

Default Namespace:

```text
default
```

Create:

```bash
kubectl create ns dev
```

View:

```bash
kubectl get ns
```

Deploy:

```bash
kubectl apply -f deployment.yaml -n dev
```

---

# Module 15: Labels & Selectors

Labels identify resources.

Example:

```yaml
labels:

  app: nginx

  tier: frontend
```

Service selects:

```yaml
selector:

  app: nginx
```

Flow:

```text
Service
   ↓
Selector
   ↓
Labels
   ↓
Pods
```

---

# Module 16: Declarative vs Imperative

---

## Imperative

Direct command:

```bash
kubectl run nginx --image=nginx
```

---

## Declarative

YAML:

```yaml
kind: Deployment
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

Production uses Declarative.

---

# Hands-On Lab

Build:

```text
Namespace: dev

Deployment:
  nginx

Replicas:
  3

Service:
  NodePort
```

Tasks:

✅ Create Namespace

✅ Deploy nginx

✅ Scale to 3 replicas

✅ Create Service

✅ Access Application

✅ View Logs

✅ Delete Deployment

✅ Recreate Deployment

---

# Troubleshooting

---

## Pods Not Running

Check:

```bash
kubectl describe pod pod_name
```

---

## Image Pull Error

Check:

```bash
kubectl describe pod pod_name
```

Common:

```text
ImagePullBackOff
```

---

## CrashLoopBackOff

Check logs:

```bash
kubectl logs pod_name
```

---

## Service Not Working

Check:

```bash
kubectl get svc
```

Verify labels:

```bash
kubectl get pods --show-labels
```

---

# Interview Questions

1. What is Kubernetes?
2. What is a Pod?
3. What is a Deployment?
4. What is a Service?
5. Difference between Pod and Deployment?
6. Difference between ClusterIP and NodePort?
7. Explain Scheduler.
8. Explain etcd.
9. Explain kubelet.
10. Why use Namespaces?

---

# Readiness Checklist

You are ready for Phase 3 if:

- [ ] Installed Kubernetes
- [ ] Created Cluster
- [ ] Used kubectl
- [ ] Created Pods
- [ ] Created Deployments
- [ ] Scaled Applications
- [ ] Created Services
- [ ] Used Namespaces
- [ ] Performed Troubleshooting

Congratulations.

You now understand the core building blocks of Kubernetes.

Next Phase:

03-Working-with-Kubernetes.md