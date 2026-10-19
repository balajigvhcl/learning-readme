# Kubernetes Security

## Learning Goal

Security is one of the most important responsibilities in a Kubernetes platform.

A Kubernetes cluster contains:

```text
Applications
Databases
Secrets
Certificates
Customer Data
Production Traffic
```

A single security misconfiguration can expose:

```text
Entire Cluster
Sensitive Data
Cloud Resources
Production Workloads
```

Kubernetes security follows a layered defense model.

By the end of this phase, you should be able to:

✅ Understand Kubernetes Security Architecture

✅ Secure Cluster Access

✅ Configure Service Accounts

✅ Configure RBAC

✅ Implement Network Policies

✅ Secure Secrets

✅ Configure Pod Security Standards

✅ Secure Container Images

✅ Implement Supply Chain Security

✅ Secure the Kubernetes API

✅ Perform Security Troubleshooting

✅ Apply Production Security Best Practices

---

# Module 1: Kubernetes Security Layers

Security exists at multiple layers.

```text
Cloud Provider
       ↓
Cluster
       ↓
Node
       ↓
Control Plane
       ↓
Network
       ↓
Pod
       ↓
Container
       ↓
Application
```

Every layer must be secured.

---

# Module 2: Shared Responsibility Model

Example:

```text
AWS EKS
Azure AKS
Google GKE
```

Provider secures:

```text
Infrastructure
Networking Backbone
Managed Control Plane
```

You secure:

```text
Applications
Namespaces
RBAC
Secrets
Policies
Workloads
```

---

# Module 3: Authentication vs Authorization

Two important concepts.

---

## Authentication

Question:

```text
Who Are You?
```

Examples:

```text
User
Service Account
Administrator
Automation Tool
```

---

## Authorization

Question:

```text
What Are You Allowed To Do?
```

Examples:

```text
Read Pods
Delete Deployments
View Secrets
Access Namespaces
```

---

# Module 4: Service Accounts

Applications need identities.

Kubernetes provides:

```text
ServiceAccount
```

Example:

```yaml
apiVersion: v1

kind: ServiceAccount

metadata:
  name: app-service-account
```

Apply:

```bash
kubectl apply -f sa.yaml
```

---

## View Service Accounts

```bash
kubectl get serviceaccounts
```

Output:

```text
default
app-service-account
```

---

# Module 5: Using Service Accounts

Example Deployment:

```yaml
spec:

  serviceAccountName: app-service-account
```

Application receives:

```text
Identity
```

inside cluster.

---

# Module 6: RBAC Overview

RBAC stands for:

```text
Role-Based Access Control
```

Controls:

```text
Who Can Access What
```

RBAC Components:

```text
Role
ClusterRole
RoleBinding
ClusterRoleBinding
```

---

# Module 7: Roles

Role provides permissions inside a namespace.

Example:

```yaml
kind: Role
```

Allow:

```yaml
apiGroups: [""]

resources:

- pods

verbs:

- get

- list
```

User can:

```text
Read Pods
```

Only.

---

# Module 8: RoleBinding

Role does nothing alone.

Need:

```text
RoleBinding
```

Example:

```yaml
kind: RoleBinding
```

Connects:

```text
User
     ↓
Role
```

Permissions become active.

---

# Module 9: ClusterRoles

Cluster-wide permissions.

Example:

```yaml
kind: ClusterRole
```

May allow:

```text
Read Nodes
Read Namespaces
Access Entire Cluster
```

Use carefully.

---

# Module 10: ClusterRoleBinding

Provides cluster-wide access.

```text
User
   ↓
ClusterRoleBinding
   ↓
ClusterRole
```

Powerful.

Should be restricted.

---

# Module 11: Verify Permissions

Check capabilities:

```bash
kubectl auth can-i get pods
```

Example:

```text
yes
```

Check another action:

```bash
kubectl auth can-i delete nodes
```

Output:

```text
no
```

Very useful for troubleshooting.

---

# Module 12: Principle of Least Privilege

Industry best practice.

Grant:

```text
Minimum Permissions Required
```

Avoid:

```text
Cluster Admin Everywhere
```

Bad Example:

```text
Developer
   ↓
Cluster Admin
```

Good Example:

```text
Developer
   ↓
Read Access
Dev Namespace
```

---

# Module 13: Network Policies

By default:

```text
Pods Can Talk To Everything
```

Security risk.

---

Network Policies control:

```text
Backend Access
Database Access
Namespace Communication
```

---

# Module 14: Network Policy Example

Allow only backend access.

```yaml
apiVersion: networking.k8s.io/v1

kind: NetworkPolicy

metadata:
  name: db-policy

spec:

  podSelector:

    matchLabels:

      app: mysql

  policyTypes:

  - Ingress
```

---

Result:

```text
Unauthorized Pods Blocked
```

---

# Module 15: Ingress vs Egress

---

## Ingress

Traffic coming in.

```text
Frontend
      ↓
Database
```

---

## Egress

Traffic leaving.

```text
Application
      ↓
External API
```

Network Policies can secure both.

---

# Module 16: Pod Security Standards

Kubernetes replaced:

```text
PodSecurityPolicy (Deprecated)
```

with:

```text
Pod Security Standards
```

Modes:

```text
Privileged
Baseline
Restricted
```

---

# Module 17: Restricted Policy

Most secure profile.

Restrictions:

```text
No Privileged Containers
No Host Networking
No Root User
```

Recommended for production workloads.

---

# Module 18: Running Containers as Non-Root

Bad:

```yaml
securityContext:

  runAsUser: 0
```

Root.

---

Better:

```yaml
securityContext:

  runAsUser: 1000
```

Non-root.

---

# Module 19: Container Capabilities

Linux capabilities:

```text
NET_ADMIN
SYS_ADMIN
SYS_TIME
```

Avoid unnecessary privileges.

Example:

```yaml
securityContext:

  capabilities:

    drop:

    - ALL
```

---

# Module 20: Read-Only Filesystem

Improve security.

```yaml
securityContext:

  readOnlyRootFilesystem: true
```

Benefits:

```text
Harder To Modify Container
Reduced Attack Surface
```

---

# Module 21: Secure Secrets

Never store:

```text
Passwords
API Keys
Tokens
```

inside:

```yaml
Deployment Files
Git Repositories
```

Use:

```text
Secrets
Vault
Key Management Systems
```

---

# Module 22: Encrypt Secrets

By default:

```text
Secrets Stored In etcd
```

Production systems should enable:

```text
Encryption At Rest
```

Protects:

```text
Passwords
Tokens
Certificates
```

---

# Module 23: Image Security

Only deploy trusted images.

Avoid:

```text
Unknown DockerHub Images
```

Prefer:

```text
Internal Registry
Official Images
Signed Images
```

---

# Module 24: Image Scanning

Scan for vulnerabilities.

Popular Tools:

```text
Trivy
Clair
Anchore
Snyk
```

Example:

```bash
trivy image nginx
```

Displays:

```text
Critical
High
Medium
Low
```

Vulnerabilities.

---

# Module 25: Admission Controllers

Intercept API requests.

Examples:

```text
Pod Creation
Deployment Creation
Secret Creation
```

Used for:

```text
Validation
Policy Enforcement
Security Controls
```

---

# Module 26: OPA Gatekeeper

Popular policy engine.

Examples:

```text
Require Labels
Block Root Containers
Enforce Security Standards
```

Policies applied automatically.

---

# Module 27: Kyverno

Alternative policy engine.

Simpler than OPA.

Examples:

```text
Require Resource Limits
Require Labels
Require Security Contexts
```

Widely used.

---

# Module 28: Audit Logs

Enable auditing.

Track:

```text
Who
Did What
When
```

Examples:

```text
Deleted Deployment
Modified Secret
Created ClusterRole
```

Important for:

```text
Compliance
Incident Response
```

---

# Module 29: Supply Chain Security

Modern attacks target:

```text
Images
Dependencies
Pipeline Tools
```

Protect using:

```text
Signed Images
Image Scanning
SBOM
Trusted Registries
```

---

# Module 30: Security Troubleshooting

---

## Access Denied

Check:

```bash
kubectl auth can-i
```

---

## Service Account Issues

Check:

```bash
kubectl get serviceaccount
```

---

## Network Policy Blocking Traffic

Verify:

```bash
kubectl get networkpolicy
```

---

## Secret Not Accessible

Check:

```bash
kubectl describe secret
```

---

## Pod Rejected

View Events:

```bash
kubectl describe pod pod-name
```

Look for:

```text
Policy Violation
Admission Rejection
```

---

# Hands-On Lab

Create:

```text
Namespace
Service Account
Role
RoleBinding
```

Tasks:

✅ Create Service Account

✅ Create Role

✅ Create RoleBinding

✅ Test RBAC

✅ Create NetworkPolicy

✅ Restrict Database

✅ Apply Security Context

✅ Create Secret

✅ Scan Image

✅ Validate Permissions

---

# Mini Project

Build Secure Application Platform.

Components:

```text
Frontend
Backend
Database
```

Requirements:

✅ Namespace Isolation

✅ RBAC

✅ Service Accounts

✅ Secrets

✅ Network Policies

✅ Security Contexts

✅ Non-Root Containers

✅ Image Scanning

✅ Audit Logging

---

# Production Best Practices

---

## Never Use Cluster Admin By Default

Apply:

```text
Least Privilege
```

---

## Use Dedicated Service Accounts

Avoid:

```text
default
```

Service Account.

---

## Restrict Container Privileges

Use:

```yaml
securityContext
```

---

## Scan All Images

Before deployment.

---

## Encrypt Secrets

At rest and in transit.

---

## Use Network Policies

Default deny strategy.

---

## Enable Auditing

Track every important action.

---

## Secure Supply Chain

Use:

```text
Signed Images
Private Registry
CI/CD Validation
```

---

# Security Checklist

✅ RBAC

✅ Service Accounts

✅ Network Policies

✅ Secrets

✅ Encryption

✅ Security Contexts

✅ Non-Root Containers

✅ Image Scanning

✅ Admission Policies

✅ Auditing

---

# Interview Questions

1. What is RBAC?
2. Difference between Authentication and Authorization?
3. What is a Service Account?
4. What is a Role?
5. What is a ClusterRole?
6. What is a RoleBinding?
7. What is a NetworkPolicy?
8. Difference between Ingress and Egress?
9. Why run containers as non-root?
10. What is Encryption at Rest?
11. How would you secure Secrets?
12. What is OPA Gatekeeper?
13. What is Kyverno?
14. How do you scan container images?
15. What is Least Privilege?

---

# Readiness Checklist

You are ready for Phase 14 if:

- [ ] Implemented RBAC
- [ ] Created Service Accounts
- [ ] Applied Network Policies
- [ ] Configured Security Contexts
- [ ] Used Non-Root Containers
- [ ] Managed Secrets Securely
- [ ] Scanned Container Images
- [ ] Understood Policy Engines
- [ ] Enabled Security Controls
- [ ] Completed Mini Project

Congratulations.

You now understand Kubernetes security fundamentals and production security practices.

Next Phase:

14-Managed-Kubernetes-EKS-AKS-GKE.md