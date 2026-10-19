# Ingress

## Learning Goal

Services provide connectivity inside and outside the cluster.

However, exposing every application using:

```text
LoadBalancer
```

or

```text
NodePort
```

creates several problems:

- High Cloud Costs
- Difficult Management
- No Central Routing
- No TLS Termination
- No URL-Based Routing

Ingress solves these problems.

By the end of this phase, you should be able to:

✅ Understand Ingress Architecture

✅ Install an Ingress Controller

✅ Configure Host-Based Routing

✅ Configure Path-Based Routing

✅ Configure TLS

✅ Configure SSL Termination

✅ Understand Ingress Controllers

✅ Understand IngressClass

✅ Troubleshoot Ingress Issues

✅ Implement Production Ingress Design

---

# Module 1: What is Ingress?

Ingress is a Kubernetes API object that manages:

```text
External HTTP Access
```

to services inside the cluster.

Think of Ingress as:

```text
Reverse Proxy
+
Load Balancer
+
Traffic Router
```

---

# Module 2: Why Ingress Exists

Without Ingress:

```text
Frontend
  ↓
LoadBalancer

Backend
  ↓
LoadBalancer

Admin UI
  ↓
LoadBalancer
```

Problems:

✅ Expensive

✅ Hard to manage

✅ Multiple public IPs

---

With Ingress:

```text
Single Load Balancer

        ↓

     Ingress

   ↓       ↓      ↓

Frontend  API  Admin
```

Single Entry Point.

---

# Module 3: Ingress Architecture

```text
Internet
    ↓

Load Balancer
    ↓

Ingress Controller
    ↓

Ingress Rules
    ↓

Services
    ↓

Pods
```

Traffic flow:

```text
User Request
    ↓
Ingress Controller
    ↓
Service
    ↓
Pod
```

---

# Module 4: Ingress vs Service

Service:

```text
Provides Access
```

Ingress:

```text
Provides Routing
```

Service Example:

```yaml
type: ClusterIP
```

Ingress Example:

```yaml
kind: Ingress
```

---

# Module 5: Ingress Controllers

Ingress resource alone does nothing.

It requires:

```text
Ingress Controller
```

Which actually processes traffic.

Popular Controllers:

```text
NGINX Ingress
Traefik
HAProxy
Istio Gateway
AWS ALB Controller
```

Most Widely Used:

```text
NGINX Ingress Controller
```

---

# Module 6: Installing NGINX Ingress

Minikube:

```bash
minikube addons enable ingress
```

Verify:

```bash
kubectl get pods -n ingress-nginx
```

Expected:

```text
Controller Running
```

---

# Module 7: Ingress Resource Basics

Simple Example:

```yaml
apiVersion: networking.k8s.io/v1

kind: Ingress

metadata:
  name: web-ingress

spec:

  rules:

  - host: app.example.com

    http:

      paths:

      - path: /

        pathType: Prefix

        backend:

          service:

            name: frontend-service

            port:

              number: 80
```

Apply:

```bash
kubectl apply -f ingress.yaml
```

---

# Module 8: Host-Based Routing

Example:

```text
api.company.com
```

goes to:

```text
backend-service
```

and

```text
admin.company.com
```

goes to:

```text
admin-service
```

Configuration:

```yaml
rules:

- host: api.company.com

- host: admin.company.com
```

---

# Module 9: Path-Based Routing

Single domain.

Multiple applications.

Example:

```text
company.com/api
```

→ Backend

```text
company.com/admin
```

→ Admin

```text
company.com
```

→ Frontend

---

Example:

```yaml
paths:

- path: /

- path: /api

- path: /admin
```

---

# Module 10: Multiple Services Example

```text
company.com
       ↓

Ingress

       ↓

/          → Frontend

/api       → Backend

/admin     → Admin UI
```

Very common production pattern.

---

# Module 11: TLS Fundamentals

Without TLS:

```text
HTTP
```

Traffic is unencrypted.

---

With TLS:

```text
HTTPS
```

Traffic encrypted.

---

Benefits:

✅ Security

✅ Authentication

✅ Compliance

✅ Customer Trust

---

# Module 12: Create TLS Secret

Certificates:

```text
tls.crt

tls.key
```

Create Secret:

```bash
kubectl create secret tls app-tls \
--cert=tls.crt \
--key=tls.key
```

Verify:

```bash
kubectl get secret
```

---

# Module 13: Configure TLS in Ingress

Example:

```yaml
tls:

- hosts:

  - app.company.com

  secretName: app-tls
```

Now:

```text
https://app.company.com
```

becomes encrypted.

---

# Module 14: SSL Termination

Process:

```text
Client
 ↓ HTTPS

Ingress
 ↓ HTTP

Backend Service
```

Encryption ends at Ingress.

Benefits:

✅ Simpler Applications

✅ Centralized Certificates

✅ Easier Management

---

# Module 15: IngressClass

Multiple Ingress Controllers can exist.

Examples:

```text
NGINX
Traefik
Istio
```

Specify controller:

```yaml
ingressClassName: nginx
```

Example:

```yaml
spec:

  ingressClassName: nginx
```

---

# Module 16: Rewrite Rules

Sometimes URLs need modification.

Request:

```text
/api/users
```

Backend expects:

```text
/users
```

NGINX supports:

```yaml
nginx.ingress.kubernetes.io/rewrite-target
```

Annotation.

---

# Module 17: Common Annotations

NGINX annotations:

Example:

```yaml
annotations:
```

Force SSL:

```yaml
nginx.ingress.kubernetes.io/ssl-redirect: "true"
```

---

Request Limit:

```yaml
nginx.ingress.kubernetes.io/proxy-body-size: 20m
```

---

Timeout:

```yaml
nginx.ingress.kubernetes.io/proxy-read-timeout: "60"
```

---

# Module 18: Ingress Troubleshooting

---

## Ingress Not Working

Verify:

```bash
kubectl get ingress
```

---

Check:

```bash
kubectl describe ingress
```

---

## No Controller Installed

Check:

```bash
kubectl get pods -n ingress-nginx
```

---

## Service Not Found

Verify:

```bash
kubectl get svc
```

---

## DNS Problems

Check:

```text
A Record
Ingress Address
```

Match correctly.

---

## TLS Not Working

Verify Secret:

```bash
kubectl get secret
```

Ensure:

```text
Certificate
Key
```

Exist.

---

# Module 19: Production Architecture

Example:

```text
Cloud Load Balancer
          ↓

NGINX Ingress

    ↓         ↓

Frontend   Backend

       ↓

Database
```

Benefits:

✅ Simplicity

✅ Cost Reduction

✅ Centralized Routing

✅ Better Security

---

# Module 20: Advanced Routing Concepts

Supported:

✅ Host Routing

✅ Path Routing

✅ TLS

✅ Sticky Sessions

✅ Rate Limiting

✅ Authentication

✅ Canary Routing

✅ Blue-Green Routing

Advanced features often depend on controller type.

---

# Module 21: DNS Design

Common Structure:

```text
app.company.com

api.company.com

admin.company.com
```

Mapped to:

```text
Ingress Public IP
```

DNS Provider:

```text
Route53
Cloud DNS
Azure DNS
Cloudflare
```

---

# Module 22: Hands-On Lab

Deploy:

```text
Frontend
Backend
Admin
```

Create:

✅ ClusterIP Services

✅ NGINX Ingress

✅ Host Routing

✅ Path Routing

✅ TLS Secret

✅ HTTPS Access

---

Verify:

```text
/company
/api
/admin
```

Routes correctly.

---

# Module 23: Mini Project

Build:

```text
E-Commerce Platform

Frontend
Backend API
Admin Portal
```

Routing:

```text
shop.example.com

api.example.com

admin.example.com
```

Requirements:

✅ Ingress

✅ TLS

✅ ClusterIP Services

✅ DNS Design

✅ SSL Redirect

✅ Monitoring

---

# Production Best Practices

---

## Use Ingress

Avoid:

```text
Many LoadBalancers
```

---

## Use TLS Everywhere

Prefer:

```text
HTTPS Only
```

---

## Use DNS Names

Avoid:

```text
Direct IP Access
```

---

## Monitor Controller

Critical component.

Monitor:

```text
Latency
Errors
Availability
```

---

## Use Cert Manager

Automate certificates.

Examples:

```text
Let's Encrypt
Internal PKI
```

---

## Limit Public Exposure

Expose:

```text
Ingress
```

Keep:

```text
Services Internal
```

---

# Interview Questions

1. What is Ingress?
2. Why is Ingress needed?
3. Difference between Service and Ingress?
4. What is an Ingress Controller?
5. Explain NGINX Ingress.
6. What is Host-Based Routing?
7. What is Path-Based Routing?
8. What is TLS Termination?
9. What is IngressClass?
10. What are Ingress Annotations?
11. How would you troubleshoot Ingress?
12. Why is Ingress preferred over multiple LoadBalancers?

---

# Readiness Checklist

You are ready for Phase 10 if:

- [ ] Installed NGINX Ingress
- [ ] Created Ingress Resources
- [ ] Configured Path Routing
- [ ] Configured Host Routing
- [ ] Created TLS Secrets
- [ ] Enabled HTTPS
- [ ] Used DNS Names
- [ ] Understood SSL Termination
- [ ] Troubleshot Ingress Issues
- [ ] Completed Mini Project

Congratulations.

You now understand Kubernetes traffic routing and application exposure.

Next Phase:

10-Helm.md