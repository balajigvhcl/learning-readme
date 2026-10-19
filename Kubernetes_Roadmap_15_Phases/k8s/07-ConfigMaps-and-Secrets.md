# ConfigMaps and Secrets

## Learning Goal

Applications require configuration.

Examples:

```text
Database Host
Database Port
Feature Flags
API Endpoints
Passwords
Tokens
Certificates
```

Hardcoding values inside container images is a bad practice.

Kubernetes provides:

```text
ConfigMap
Secret
```

to externalize configuration.

By the end of this phase, you should be able to:

✅ Create ConfigMaps

✅ Consume ConfigMaps

✅ Create Secrets

✅ Consume Secrets

✅ Inject Environment Variables

✅ Mount Configuration Files

✅ Mount Secret Files

✅ Manage Application Configuration

✅ Follow Security Best Practices

✅ Troubleshoot Configuration Issues

---

# Module 1: Why ConfigMaps and Secrets Exist

Consider an application.

Configuration:

```text
DB_HOST=mysql
DB_PORT=3306
ENV=production
```

Bad approach:

```python
DB_HOST="mysql"
DB_PORT="3306"
```

Problems:

- Requires rebuild for changes
- Difficult environment migration
- Hardcoded values

Better:

```text
Application
      ↓
External Configuration
```

This is the purpose of ConfigMaps.

---

# Module 2: What is a ConfigMap?

ConfigMap stores:

```text
Non-Sensitive Data
```

Examples:

```text
Application Config
Environment Variables
URLs
Endpoints
Feature Flags
```

Examples:

```text
APP_NAME
APP_ENV
API_URL
DB_HOST
```

Never store passwords in ConfigMaps.

---

# Module 3: Creating a ConfigMap

YAML Example:

```yaml
apiVersion: v1

kind: ConfigMap

metadata:
  name: app-config

data:

  APP_NAME: my-app

  APP_ENV: production

  DB_HOST: mysql-service
```

Apply:

```bash
kubectl apply -f configmap.yaml
```

Verify:

```bash
kubectl get configmap
```

---

# Module 4: Viewing ConfigMaps

List:

```bash
kubectl get configmaps
```

Describe:

```bash
kubectl describe configmap app-config
```

View YAML:

```bash
kubectl get configmap app-config -o yaml
```

View JSON:

```bash
kubectl get configmap app-config -o json
```

---

# Module 5: Create ConfigMap from Command Line

Create:

```bash
kubectl create configmap app-config \
--from-literal=APP_ENV=production
```

Verify:

```bash
kubectl get cm
```

---

# Module 6: Create ConfigMap from File

Example file:

```text
application.properties
```

Contents:

```properties
app.name=my-app
app.version=1.0
```

Create:

```bash
kubectl create configmap app-config \
--from-file=application.properties
```

---

# Module 7: Using ConfigMaps as Environment Variables

Deployment:

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: frontend

spec:

  replicas: 1

  selector:
    matchLabels:
      app: frontend

  template:

    metadata:
      labels:
        app: frontend

    spec:

      containers:

      - name: frontend
        image: nginx

        env:

        - name: APP_ENV

          valueFrom:

            configMapKeyRef:
              name: app-config
              key: APP_ENV
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

---

# Module 8: Import Entire ConfigMap

Instead of defining keys separately:

```yaml
envFrom:

- configMapRef:
    name: app-config
```

All values become environment variables.

---

# Module 9: Verifying Environment Variables

Get Pod:

```bash
kubectl get pods
```

Enter Pod:

```bash
kubectl exec -it pod-name -- sh
```

Check:

```bash
env
```

Expected:

```text
APP_ENV=production
```

---

# Module 10: Mounting ConfigMaps as Files

Many applications expect files.

ConfigMap:

```yaml
apiVersion: v1

kind: ConfigMap

metadata:
  name: nginx-config

data:

  nginx.conf: |
    server {
      listen 80;
    }
```

---

Mount:

```yaml
volumeMounts:

- name: config-volume

  mountPath: /etc/nginx
```

Volume:

```yaml
volumes:

- name: config-volume

  configMap:
    name: nginx-config
```

---

# Module 11: What is a Secret?

Secrets store:

```text
Sensitive Data
```

Examples:

```text
Passwords
Tokens
API Keys
Certificates
SSH Keys
```

Examples:

```text
DB_PASSWORD
JWT_SECRET
AWS_ACCESS_KEY
```

---

# Module 12: Why Not Use ConfigMaps for Passwords?

ConfigMaps:

```text
Plain Text
```

Anyone with access can read.

Secrets provide:

```text
Better Security Controls
```

Though note:

```text
Base64 ≠ Encryption
```

Additional security is still needed.

---

# Module 13: Creating a Secret

Create Base64 Value:

```bash
echo -n "mysecretpassword" | base64
```

Example:

```text
bXlzZWNyZXRwYXNzd29yZA==
```

Create Secret:

```yaml
apiVersion: v1

kind: Secret

metadata:
  name: db-secret

type: Opaque

data:

  DB_PASSWORD: bXlzZWNyZXRwYXNzd29yZA==
```

Apply:

```bash
kubectl apply -f secret.yaml
```

---

# Module 14: View Secrets

List:

```bash
kubectl get secrets
```

Describe:

```bash
kubectl describe secret db-secret
```

YAML:

```bash
kubectl get secret db-secret -o yaml
```

---

# Module 15: Decode Secret

Get value:

```bash
kubectl get secret db-secret \
-o jsonpath='{.data.DB_PASSWORD}'
```

Decode:

```bash
echo VALUE | base64 --decode
```

---

# Module 16: Create Secret from Command

Create:

```bash
kubectl create secret generic db-secret \
--from-literal=password=mysecret
```

Verify:

```bash
kubectl get secrets
```

---

# Module 17: Using Secrets as Environment Variables

Deployment:

```yaml
env:

- name: DB_PASSWORD

  valueFrom:

    secretKeyRef:

      name: db-secret

      key: DB_PASSWORD
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

---

# Module 18: Mount Secrets as Files

Volume:

```yaml
volumes:

- name: secret-volume

  secret:
    secretName: db-secret
```

Mount:

```yaml
volumeMounts:

- name: secret-volume

  mountPath: /etc/secrets
```

Result:

```text
/etc/secrets
```

Contains:

```text
DB_PASSWORD
```

File.

---

# Module 19: TLS Secrets

Common for HTTPS.

Create:

```bash
kubectl create secret tls tls-secret \
--cert=tls.crt \
--key=tls.key
```

Used by:

```text
Ingress
NGINX
Istio
```

---

# Module 20: Secret Types

Kubernetes supports:

```text
Opaque
TLS
docker-registry
service-account-token
```

Most Common:

```text
Opaque
TLS
```

---

# Module 21: Image Pull Secrets

Private Registry Example:

```text
Docker Hub Private Repository
AWS ECR
Azure ACR
```

Create:

```bash
kubectl create secret docker-registry regcred \
--docker-server=https://index.docker.io/v1/
```

Use:

```yaml
imagePullSecrets:

- name: regcred
```

---

# Module 22: Updating ConfigMaps

Update YAML:

```yaml
APP_ENV: staging
```

Apply:

```bash
kubectl apply -f configmap.yaml
```

Verify:

```bash
kubectl describe configmap
```

---

Important:

Many applications require:

```text
Pod Restart
```

for changes to take effect.

---

# Module 23: ConfigMap vs Secret

| Feature | ConfigMap | Secret |
|----------|----------|----------|
| Purpose | Configuration | Sensitive Data |
| Storage | Plain Text | Base64 Encoded |
| Passwords | No | Yes |
| Tokens | No | Yes |
| API Keys | No | Yes |

---

# Module 24: Common Problems

---

## ConfigMap Not Loading

Check:

```bash
kubectl describe pod
```

Verify:

```text
ConfigMap Name
ConfigMap Key
```

---

## Secret Missing

Check:

```bash
kubectl get secret
```

---

## Incorrect Base64

Validate:

```bash
echo value | base64
```

---

## Environment Variable Missing

Verify:

```bash
kubectl exec -it pod -- env
```

---

## Mounted File Missing

Check:

```bash
kubectl exec -it pod -- ls /mount/path
```

---

# Module 25: Hands-On Lab

Create:

```text
ConfigMap
Secret
Deployment
```

Tasks:

✅ Create ConfigMap

✅ Create Secret

✅ Inject Environment Variables

✅ Mount ConfigMap

✅ Mount Secret

✅ Verify Values

✅ Update Configuration

✅ Restart Application

✅ Validate Secret Access

---

# Mini Project

Deploy:

```text
Frontend
Backend
MySQL
```

Requirements:

Frontend:

```text
APP_ENV
API_URL
```

Using ConfigMap.

Backend:

```text
DB_HOST
DB_PORT
```

Using ConfigMap.

Database:

```text
DB_USER
DB_PASSWORD
```

Using Secret.

---

# Production Best Practices

---

## Never Hardcode Secrets

Bad:

```yaml
password: admin123
```

---

## Use Secrets

Good:

```yaml
secretKeyRef
```

---

## Separate Config and Secrets

ConfigMap:

```text
Application Settings
```

Secret:

```text
Passwords
Tokens
Certificates
```

---

## Restrict Secret Access

Use:

```text
RBAC
Namespaces
```

---

## Enable Encryption at Rest

Production clusters should encrypt:

```text
Secrets in etcd
```

---

## Rotate Secrets Regularly

Examples:

```text
Database Passwords
API Tokens
Certificates
```

---

# Interview Questions

1. What is a ConfigMap?
2. What is a Secret?
3. Difference between ConfigMap and Secret?
4. How do you inject a ConfigMap?
5. How do you mount a ConfigMap?
6. How do you create a Secret?
7. Is Base64 Encryption?
8. How do TLS Secrets work?
9. What are Image Pull Secrets?
10. How do you update ConfigMaps?
11. How would you secure Secrets in production?
12. What is Encryption at Rest?

---

# Readiness Checklist

You are ready for Phase 8 if:

- [ ] Created ConfigMaps
- [ ] Created Secrets
- [ ] Injected Environment Variables
- [ ] Mounted ConfigMaps as Files
- [ ] Mounted Secrets as Files
- [ ] Created TLS Secrets
- [ ] Used Image Pull Secrets
- [ ] Updated Configuration
- [ ] Applied Security Best Practices
- [ ] Completed Hands-On Lab

Congratulations.

You now understand Kubernetes configuration and secret management.

Next Phase:

08-Storage-PV-PVC.md