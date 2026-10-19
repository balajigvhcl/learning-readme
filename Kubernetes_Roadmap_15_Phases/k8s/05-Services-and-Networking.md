# Services and Networking

## Learning Goal

Containers and Pods are temporary.

Pods get:

- Dynamic IP Addresses
- Dynamic Names
- Dynamic Placement

Applications need a stable way to communicate.

This is where Kubernetes Networking and Services become critical.

By the end of this phase, you should be able to:

✅ Understand Pod Networking

✅ Understand Service Discovery

✅ Configure ClusterIP

✅ Configure NodePort

✅ Configure LoadBalancer

✅ Understand Endpoints

✅ Understand CoreDNS

✅ Understand kube-proxy

✅ Troubleshoot Network Issues

✅ Design Production Networking

---

# Module 1: Why Services Exist

Consider this Deployment:

```text
Frontend Deployment

Frontend Pod A
Frontend Pod B
Frontend Pod C
```

Each Pod receives an IP.

Example:

```text
10.244.1.2
10.244.1.3
10.244.1.4
```

Problem:

Pod deleted.

New Pod:

```text
10.244.1.8
```

IP changes.

Applications would break.

---

## Solution

Create a Service.

Service provides:

```text
Stable Name
Stable IP
Load Balancing
```

---

# Module 2: Kubernetes Networking Model

Kubernetes networking follows four rules.

---

## Rule 1

Every Pod gets its own IP.

Example:

```text
Pod A

10.244.1.10
```

```text
Pod B

10.244.1.11
```

---

## Rule 2

Pods communicate directly.

No NAT required.

```text
Pod A
    ↓
Pod B
```

---

## Rule 3

Nodes communicate with Pods.

```text
Node
   ↓
Pods
```

---

## Rule 4

Services provide stable access.

```text
Service
   ↓
Pods
```

---

# Module 3: Service Architecture

```text
Frontend Service
       ↓

Load Balancing

       ↓

Frontend Pod A

Frontend Pod B

Frontend Pod C
```

Traffic automatically distributes.

---

# Module 4: Service Discovery

Without Service:

```text
10.244.1.2
10.244.1.3
10.244.1.4
```

Hard to manage.

With Service:

```text
frontend-service
```

Applications connect using:

```text
frontend-service
```

No IP management needed.

---

# Module 5: Service Types

Kubernetes provides:

```text
ClusterIP
NodePort
LoadBalancer
ExternalName
```

Most common:

```text
ClusterIP
NodePort
LoadBalancer
```

---

# Module 6: ClusterIP

Default Service Type.

Used for:

```text
Internal Communication
```

Example:

```text
Backend
   ↓
Database
```

Database should not be publicly accessible.

---

## ClusterIP Service

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-service

spec:

  selector:
    app: nginx

  ports:
  - port: 80
    targetPort: 80

  type: ClusterIP
```

Apply:

```bash
kubectl apply -f clusterip.yaml
```

---

# Module 7: Inspect Services

View:

```bash
kubectl get svc
```

Output:

```text
NAME            TYPE

nginx-service   ClusterIP
```

Detailed View:

```bash
kubectl describe svc nginx-service
```

---

# Module 8: NodePort

Expose service outside cluster.

Example:

```text
User
 ↓
NodeIP:30080
 ↓
Service
 ↓
Pods
```

---

## NodePort Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-nodeport

spec:

  selector:
    app: nginx

  ports:

  - port: 80

    targetPort: 80

    nodePort: 30080

  type: NodePort
```

Apply:

```bash
kubectl apply -f nodeport.yaml
```

---

## Access Example

```text
http://192.168.1.100:30080
```

---

# Module 9: LoadBalancer

Common in Cloud.

Providers:

```text
AWS EKS
Azure AKS
Google GKE
```

Service Type:

```yaml
type: LoadBalancer
```

Cloud automatically creates:

```text
Public Load Balancer
Public IP
```

---

## Example

```yaml
apiVersion: v1
kind: Service

metadata:
  name: nginx-lb

spec:

  selector:
    app: nginx

  ports:
  - port: 80

  type: LoadBalancer
```

Apply:

```bash
kubectl apply -f loadbalancer.yaml
```

---

# Module 10: Service Selectors

Services locate Pods using labels.

---

## Pod

```yaml
labels:
  app: nginx
```

---

## Service

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

---

# Module 11: Endpoints

Endpoints link Services to Pods.

View:

```bash
kubectl get endpoints
```

Example:

```text
nginx-service

10.244.1.2
10.244.1.3
10.244.1.4
```

Actual Pods behind Service.

---

# Module 12: CoreDNS

Kubernetes DNS system.

Every Service gets DNS.

Example Service:

```text
mysql-service
```

Can be reached from any Pod:

```text
mysql-service
```

Or:

```text
mysql-service.default.svc.cluster.local
```

---

## Check DNS

Run Pod:

```bash
kubectl run dns-test \
--image=busybox \
-it
```

Lookup:

```bash
nslookup nginx-service
```

---

# Module 13: kube-proxy

Network component on every node.

Responsibilities:

```text
Service Routing
Packet Forwarding
Load Balancing
```

Flow:

```text
Request
   ↓
kube-proxy
   ↓
Selected Pod
```

---

# Module 14: Pod-to-Pod Communication

Example:

```text
Frontend Pod
       ↓
Backend Pod
       ↓
MySQL Pod
```

No extra networking configuration needed.

Kubernetes handles:

```text
Routing
Connectivity
```

Automatically.

---

# Module 15: Container Network Interface (CNI)

Networking Plugin.

Examples:

```text
Calico
Cilium
Flannel
Weave
```

Responsible for:

```text
Pod Networking
Routing
Network Policies
```

---

## Popular Choices

---

### Calico

Provides:

```text
Networking
Network Policies
```

Common in production.

---

### Cilium

Uses:

```text
eBPF
```

Benefits:

```text
Performance
Security
Observability
```

Growing rapidly.

---

# Module 16: Testing Connectivity

Create BusyBox:

```bash
kubectl run network-test \
--image=busybox \
-it
```

Ping Service:

```bash
ping nginx-service
```

Connect:

```bash
wget nginx-service
```

---

# Module 17: Service Troubleshooting

---

## Service Not Working

Check:

```bash
kubectl get svc
```

---

## Verify Labels

```bash
kubectl get pods --show-labels
```

---

## Verify Endpoints

```bash
kubectl get endpoints
```

No endpoints means:

```text
Selector mismatch
```

---

## Check DNS

```bash
nslookup service-name
```

---

# Module 18: Common Networking Problems

---

## Problem

Pods Running

Service Empty

Cause:

```text
Wrong Labels
```

---

## Problem

DNS Resolution Fails

Cause:

```text
CoreDNS Issues
```

Check:

```bash
kubectl get pods -n kube-system
```

---

## Problem

NodePort Not Accessible

Check:

```bash
kubectl get svc
```

Verify:

```text
Node IP
Firewall Rules
```

---

# Module 19: Hands-On Lab

Deploy:

```text
Frontend Deployment
Backend Deployment
```

Create:

```text
Frontend Service
Backend Service
```

Tasks:

✅ Create ClusterIP

✅ Create NodePort

✅ Create Labels

✅ Verify Selectors

✅ View Endpoints

✅ Test DNS

✅ Verify Pod Connectivity

✅ Debug Failed Service

---

# Module 20: Production Networking Best Practices

---

## Internal Applications

Use:

```yaml
type: ClusterIP
```

Default choice.

---

## Public Applications

Prefer:

```yaml
LoadBalancer
```

or

```yaml
Ingress
```

---

## Avoid Hardcoded IPs

Always use:

```text
Service Names
```

---

## Use Meaningful Labels

Example:

```yaml
labels:

  app: backend

  env: prod

  team: payments
```

---

## Monitor DNS

CoreDNS is critical.

Monitor:

```text
Availability
Latency
Errors
```

---

# Mini Project

Deploy:

```text
Frontend
Backend
Redis
MySQL
```

Requirements:

- Frontend exposed via NodePort
- Backend via ClusterIP
- Redis internal
- MySQL internal
- Network tested
- DNS verified

---

# Interview Questions

1. What is a Kubernetes Service?
2. Why do Services exist?
3. Explain ClusterIP.
4. Explain NodePort.
5. Explain LoadBalancer.
6. What are Endpoints?
7. What is CoreDNS?
8. What is kube-proxy?
9. What is a CNI Plugin?
10. Difference between Service and Pod?
11. Why should applications use Service Names?
12. How would you troubleshoot Service failures?

---

# Readiness Checklist

You are ready for Phase 6 if:

- [ ] Created ClusterIP Services
- [ ] Created NodePort Services
- [ ] Understood LoadBalancers
- [ ] Verified DNS
- [ ] Used CoreDNS
- [ ] Viewed Endpoints
- [ ] Understood kube-proxy
- [ ] Tested Connectivity
- [ ] Troubleshot Service Failures

Congratulations.

You now understand Kubernetes Networking and Service Discovery.

Next Phase:

06-Namespaces.md