# Phase 2: Kubernetes Fundamentals

## Objectives

Learn Kubernetes Architecture.

## Topics

### Why Kubernetes?

- Self Healing
- Scaling
- Load Balancing
- Service Discovery

### Architecture

Control Plane

- API Server
- Scheduler
- Controller Manager
- etcd

Worker Node

- kubelet
- kube-proxy
- container runtime

### Local Cluster

Minikube

```bash
minikube start
```

Kind

```bash
kind create cluster
```

### kubectl

```bash
kubectl get nodes
kubectl cluster-info
```

## Lab

Create local cluster and inspect nodes.