# Helm

## Learning Goal

As Kubernetes environments grow, managing YAML files becomes difficult.

Consider a typical application:

```text
Deployment
Service
Ingress
ConfigMap
Secret
PVC
ServiceAccount
```

This could easily become:

```text
20+
YAML Files
```

Managing them manually becomes slow and error-prone.

Helm solves this problem.

Helm is the package manager for Kubernetes.

Think of Helm as:

```text
apt for Ubuntu

yum for RHEL

npm for JavaScript

helm for Kubernetes
```

By the end of this phase, you should be able to:

✅ Understand Helm Architecture

✅ Install Helm

✅ Use Helm Charts

✅ Deploy Applications with Helm

✅ Configure Values

✅ Create Your Own Charts

✅ Package Applications

✅ Upgrade Applications

✅ Roll Back Releases

✅ Troubleshoot Helm Deployments

✅ Follow Production Best Practices

---

# Module 1: Why Helm Exists

Without Helm:

Application Deployment:

```text
deployment.yaml
service.yaml
ingress.yaml
configmap.yaml
secret.yaml
pvc.yaml
```

Deploy:

```bash
kubectl apply -f .
```

Problems:

❌ Too Many Files

❌ No Versioning

❌ Hard Reusability

❌ Difficult Upgrades

❌ Manual Rollbacks

---

Helm solves this.

---

# Module 2: What Is Helm?

Helm is a package manager for Kubernetes.

Helm packages applications as:

```text
Charts
```

A Chart contains:

```text
YAML Templates
Configuration
Metadata
Version Information
```

Example:

```text
Prometheus
Grafana
Nginx
Jenkins
MongoDB
```

can be installed using a single command.

---

# Module 3: Helm Architecture

```text
Helm CLI
      ↓
Helm Chart
      ↓
Templates
      ↓
Rendered YAML
      ↓
Kubernetes API
```

---

Flow:

```text
helm install
       ↓

Values.yaml
       ↓

Templates
       ↓

Deployment
Service
Ingress
ConfigMap
```

---

# Module 4: Install Helm

Linux:

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

Verify:

```bash
helm version
```

Expected:

```text
version.BuildInfo
```

---

# Module 5: Core Helm Concepts

Helm has several important concepts.

---

## Chart

Application Package.

Example:

```text
nginx chart

grafana chart

prometheus chart
```

---

## Release

Running instance of a chart.

Example:

```bash
helm install my-nginx nginx-chart
```

Release Name:

```text
my-nginx
```

---

## Repository

Collection of charts.

Similar to:

```text
Docker Hub
```

but for Helm charts.

---

# Module 6: Helm Repository

Add repository:

```bash
helm repo add bitnami \
https://charts.bitnami.com/bitnami
```

Update:

```bash
helm repo update
```

Search:

```bash
helm search repo nginx
```

---

# Module 7: Installing Applications

Install NGINX:

```bash
helm install nginx \
bitnami/nginx
```

Verify:

```bash
helm list
```

Output:

```text
NAME
nginx
```

---

Check resources:

```bash
kubectl get all
```

---

# Module 8: Release Management

List releases:

```bash
helm list
```

View release:

```bash
helm status nginx
```

Details:

```bash
helm get values nginx
```

---

# Module 9: Upgrading Releases

Upgrade:

```bash
helm upgrade nginx \
bitnami/nginx
```

Common Use:

```text
New Version
New Config
New Resources
```

---

# Module 10: Rollbacks

View history:

```bash
helm history nginx
```

Example:

```text
REVISION

1
2
3
```

Rollback:

```bash
helm rollback nginx 2
```

Return to revision:

```text
2
```

---

# Module 11: Uninstalling Releases

Remove:

```bash
helm uninstall nginx
```

Verify:

```bash
helm list
```

Release disappears.

Kubernetes resources removed.

---

# Module 12: Helm Chart Structure

Create chart:

```bash
helm create myapp
```

Generated Structure:

```text
myapp

├── Chart.yaml
├── values.yaml
├── charts/
└── templates/
```

---

# Module 13: Chart.yaml

Metadata file.

Example:

```yaml
apiVersion: v2

name: myapp

version: 1.0.0

appVersion: "1.0"
```

Contains:

```text
Name
Version
Description
Dependencies
```

---

# Module 14: values.yaml

Configuration file.

Example:

```yaml
replicaCount: 2

image:

  repository: nginx

  tag: latest
```

Allows customization.

---

# Module 15: Templates

Templates generate actual Kubernetes YAML.

Example:

```text
templates/deployment.yaml
```

Uses values:

```yaml
replicas: {{ .Values.replicaCount }}
```

Helm converts templates into Kubernetes manifests.

---

# Module 16: Template Variables

Access values:

```yaml
{{ .Values.image.repository }}
```

Example:

```yaml
image:
  repository: nginx
```

Produces:

```yaml
image: nginx
```

---

# Module 17: Template Functions

Helm provides functions.

Example:

```yaml
{{ upper .Values.environment }}
```

Input:

```text
dev
```

Output:

```text
DEV
```

Useful for dynamic templates.

---

# Module 18: Installing with Custom Values

Create:

```yaml
replicaCount: 5
```

Install:

```bash
helm install myapp \
./myapp \
-f values.yaml
```

Result:

```text
5 Replicas
```

---

# Module 19: Override Values

Instead of file:

```bash
helm install myapp \
./myapp \
--set replicaCount=5
```

Quick customization.

---

# Module 20: Rendering Templates

Preview YAML:

```bash
helm template myapp ./myapp
```

Useful for:

✅ Validation

✅ Debugging

✅ GitOps

---

# Module 21: Debugging Charts

Render with debug:

```bash
helm install myapp ./myapp \
--debug
```

Dry Run:

```bash
helm install myapp ./myapp \
--dry-run
```

Does not create resources.

Great for testing.

---

# Module 22: Dependencies

Applications often require:

```text
Database
Redis
Monitoring
```

Chart dependencies help.

Example:

```yaml
dependencies:

- name: mysql

  version: "9.0.0"

  repository: https://charts.bitnami.com/bitnami
```

Retrieve:

```bash
helm dependency update
```

---

# Module 23: Packaging Charts

Package:

```bash
helm package myapp
```

Output:

```text
myapp-1.0.0.tgz
```

Share internally.

Upload to chart registry.

---

# Module 24: Real-World Helm Examples

Common Charts:

```text
NGINX Ingress
Prometheus
Grafana
Jenkins
ArgoCD
MongoDB
PostgreSQL
Redis
Elasticsearch
```

Install Grafana:

```bash
helm install grafana \
grafana/grafana
```

Install Prometheus:

```bash
helm install prometheus \
prometheus-community/prometheus
```

---

# Module 25: Helm and CI/CD

Typical Flow:

```text
Developer Pushes Code
          ↓

Build Image
          ↓

Update Chart
          ↓

Helm Upgrade
          ↓

Deploy Application
```

Popular Tools:

```text
Jenkins
GitHub Actions
GitLab CI
Azure DevOps
ArgoCD
```

---

# Module 26: Helm Security

Avoid:

```yaml
password: admin123
```

inside:

```yaml
values.yaml
```

Use:

```text
Kubernetes Secrets
External Secret Managers
```

Examples:

```text
HashiCorp Vault
AWS Secrets Manager
Azure Key Vault
```

---

# Module 27: Helm Troubleshooting

---

## Failed Install

Check:

```bash
helm status release-name
```

---

## Template Errors

Test:

```bash
helm template
```

---

## Invalid YAML

Validate:

```bash
helm lint
```

---

Run:

```bash
helm lint myapp
```

---

## Failed Upgrade

Review:

```bash
helm history release-name
```

Rollback:

```bash
helm rollback release-name revision
```

---

# Module 28: Hands-On Lab

Build:

```text
Frontend
Backend
Database
```

Create Helm Chart.

Tasks:

✅ Create Chart

✅ Modify values.yaml

✅ Create Deployments

✅ Create Service

✅ Install Chart

✅ Upgrade Chart

✅ Rollback Chart

✅ Remove Chart

✅ Validate Templates

---

# Module 29: Mini Project

Create:

```text
E-Commerce Application
```

Components:

```text
Frontend
Backend
Redis
MySQL
Ingress
```

Requirements:

✅ Helm Chart

✅ Values File

✅ Templated Deployments

✅ Templated Services

✅ Configurable Replicas

✅ Configurable Images

✅ Rollback Validation

---

# Production Best Practices

---

## Use Versioned Charts

Example:

```text
myapp-1.0.0

myapp-1.1.0

myapp-2.0.0
```

---

## Store Charts in Git

Version Control everything.

---

## Use Helm Lint

Always run:

```bash
helm lint
```

before deployment.

---

## Use Dry Runs

Validate:

```bash
--dry-run
```

First.

---

## Avoid Secrets in values.yaml

Use:

```text
Vault
Secrets Manager
Kubernetes Secrets
```

---

## Separate Values Files

Example:

```text
values-dev.yaml

values-qa.yaml

values-prod.yaml
```

---

# Interview Questions

1. What is Helm?
2. Why is Helm used?
3. What is a Chart?
4. What is a Release?
5. What is a Repository?
6. What is values.yaml?
7. What are Templates?
8. How does Helm perform upgrades?
9. How does rollback work?
10. What is helm lint?
11. What is helm template?
12. How would you package a chart?

---

# Readiness Checklist

You are ready for Phase 11 if:

- [ ] Installed Helm
- [ ] Added Repositories
- [ ] Installed Charts
- [ ] Upgraded Releases
- [ ] Performed Rollbacks
- [ ] Created Custom Charts
- [ ] Used values.yaml
- [ ] Used Templates
- [ ] Packaged Charts
- [ ] Completed Mini Project

Congratulations.

You now understand Kubernetes package management and application templating.

Next Phase:

11-StatefulSets.md