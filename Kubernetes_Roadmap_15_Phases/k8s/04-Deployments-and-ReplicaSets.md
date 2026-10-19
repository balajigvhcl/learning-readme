# Deployments and ReplicaSets

## Learning Goal

In real-world Kubernetes environments, applications are rarely deployed as standalone Pods.

Deployments and ReplicaSets provide:

✅ High Availability

✅ Self-Healing

✅ Scaling

✅ Zero Downtime Updates

✅ Rollbacks

✅ Version Management

✅ Production Application Lifecycle Management

By the end of this phase, you should be able to:

- Create Deployments
- Manage ReplicaSets
- Scale Applications
- Perform Rolling Updates
- Perform Rollbacks
- Troubleshoot Deployment Failures
- Implement Production Deployment Strategies

---

# Module 1: Why Deployments Exist

Imagine you create a Pod directly.

```yaml
kind: Pod
```

Pod crashes.

Result:

```text
Application Down
```

Pod deleted.

Result:

```text
Application Gone
```

Kubernetes does not recreate manually-created Pods.

This is why Deployments exist.

---

# Module 2: Deployment Architecture

Deployment hierarchy:

```text
Deployment
      ↓
ReplicaSet
      ↓
Pods
```

Example:

```text
Nginx Deployment

Replicas: 3
```

Kubernetes creates:

```text
ReplicaSet
     ↓

Pod-1
Pod-2
Pod-3
```

---

# Module 3: Understanding Deployments

Deployment responsibilities:

### Self-Healing

A Pod crashes.

Deployment creates a replacement.

---

### Scaling

Increase:

```text
3 Pods
```

to

```text
10 Pods
```

Automatically.

---

### Updates

Deploy new versions safely.

---

### Rollbacks

Return to previous version.

---

# Module 4: Your First Deployment

Create file:

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

# Module 5: Verify Deployment

Check Deployments:

```bash
kubectl get deployments
```

Example:

```text
NAME               READY
nginx-deployment   3/3
```

Check ReplicaSets:

```bash
kubectl get rs
```

Output:

```text
nginx-deployment-xxxxx
```

Check Pods:

```bash
kubectl get pods
```

Expected:

```text
3 Pods Running
```

---

# Module 6: Understanding ReplicaSets

ReplicaSet ensures desired Pod count.

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

Automatically.

---

# Module 7: ReplicaSet Demonstration

Delete a Pod:

```bash
kubectl delete pod pod-name
```

Watch:

```bash
kubectl get pods -w
```

Observe:

```text
Pod Deleted

New Pod Created
```

Self-Healing in action.

---

# Module 8: Scaling Deployments

Current state:

```text
3 Pods
```

Increase to:

```text
5 Pods
```

Command:

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
5 Pods Running
```

---

# Module 9: Declarative Scaling

Update YAML:

```yaml
replicas: 10
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

Verify:

```bash
kubectl get deployment
```

---

# Module 10: Rolling Updates

Current Version:

```text
nginx:1.24
```

Update:

```text
nginx:1.25
```

Modify YAML:

```yaml
image: nginx:1.25
```

Apply:

```bash
kubectl apply -f deployment.yaml
```

Kubernetes performs:

```text
One Pod Updated
One Pod Updated
One Pod Updated
```

No downtime.

---

# Module 11: Monitor Rollout

Check rollout:

```bash
kubectl rollout status deployment nginx-deployment
```

Output:

```text
successfully rolled out
```

---

# Module 12: Rollout History

View revisions:

```bash
kubectl rollout history deployment nginx-deployment
```

Example:

```text
REVISION

1
2
3
```

Each update creates a new revision.

---

# Module 13: Rollbacks

Current version broken:

```text
v3
```

Rollback:

```bash
kubectl rollout undo deployment nginx-deployment
```

Kubernetes restores:

```text
v2
```

---

## Rollback To Specific Revision

View History:

```bash
kubectl rollout history deployment nginx-deployment
```

Rollback:

```bash
kubectl rollout undo deployment nginx-deployment \
--to-revision=1
```

---

# Module 14: Deployment Strategies

Several deployment approaches exist.

---

## Recreate

Old Pods:

```text
Stopped
```

Then:

```text
New Pods Started
```

Result:

```text
Downtime
```

Rarely used.

---

## Rolling Update

Default Kubernetes strategy.

```text
Old Pod Removed
New Pod Added
```

Gradual replacement.

No downtime.

---

## Blue-Green

Two environments:

```text
Blue = Current Version

Green = New Version
```

Switch traffic after testing.

Benefits:

- Instant rollback
- Safe releases

---

## Canary Deployment

Traffic split.

Example:

```text
90% → Version 1

10% → Version 2
```

Evaluate before full rollout.

Popular with:

```text
Istio
Argo Rollouts
NGINX Ingress
```

---

# Module 15: Deployment Strategy Configuration

Example:

```yaml
strategy:

  type: RollingUpdate

  rollingUpdate:

    maxUnavailable: 1
    maxSurge: 1
```

---

## maxUnavailable

Pods allowed offline.

Example:

```yaml
maxUnavailable: 1
```

---

## maxSurge

Extra Pods during deployment.

Example:

```yaml
maxSurge: 1
```

---

# Module 16: Deployment Status

Check:

```bash
kubectl describe deployment nginx-deployment
```

Useful Information:

- Events
- Conditions
- Progress
- Replica Count

---

# Module 17: Updating Images

Current:

```yaml
image: nginx:1.24
```

Update:

```bash
kubectl set image deployment/nginx-deployment \
nginx=nginx:1.25
```

Check:

```bash
kubectl rollout status deployment nginx-deployment
```

---

# Module 18: Pausing Deployments

Pause rollout:

```bash
kubectl rollout pause deployment nginx-deployment
```

Resume:

```bash
kubectl rollout resume deployment nginx-deployment
```

Useful during production releases.

---

# Module 19: Resource Management

Production deployments should define:

```yaml
resources:

  requests:

    cpu: "250m"

    memory: "256Mi"

  limits:

    cpu: "500m"

    memory: "512Mi"
```

Benefits:

- Capacity planning
- Prevent noisy neighbors
- Stable workloads

---

# Module 20: Deployment Debugging

---

## Pods Not Starting

Check:

```bash
kubectl describe pod pod-name
```

---

## Wrong Image

Check:

```bash
kubectl get deployment -o yaml
```

---

## CrashLoopBackOff

View logs:

```bash
kubectl logs pod-name
```

---

## Failed Rollout

Check:

```bash
kubectl rollout status deployment deployment-name
```

---

## Events

View:

```bash
kubectl get events
```

---

# Hands-On Lab

Create:

```text
Namespace
       ↓

Deployment
       ↓

5 Replicas
       ↓

Nginx v1
```

Tasks:

✅ Create Deployment

✅ Verify ReplicaSet

✅ Scale to 10 Pods

✅ Upgrade Image

✅ Monitor Rollout

✅ Rollback Deployment

✅ Pause Rollout

✅ Resume Rollout

✅ Delete Pod

✅ Observe Self-Healing

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

1. Use Deployments
2. 3 Replicas Each
3. Rolling Updates Enabled
4. Resource Limits Configured
5. Labels Applied
6. Namespace Isolation
7. Rollback Tested

---

# Production Best Practices

---

## Never Deploy Standalone Pods

Use:

```yaml
kind: Deployment
```

---

## Use Labels Consistently

Example:

```yaml
labels:

  app: frontend

  env: production

  team: platform
```

---

## Set Resource Limits

Always define:

```yaml
requests
limits
```

---

## Monitor Rollouts

Always check:

```bash
kubectl rollout status
```

---

## Practice Rollbacks

Every deployment should have rollback validation.

---

# Interview Questions

1. What is a Deployment?
2. What is a ReplicaSet?
3. Difference between Deployment and ReplicaSet?
4. What is Self-Healing?
5. What is a Rolling Update?
6. What is Rollback?
7. Explain maxSurge.
8. Explain maxUnavailable.
9. What is Canary Deployment?
10. What is Blue-Green Deployment?
11. Why not use standalone Pods?
12. How do you scale a Deployment?

---

# Readiness Checklist

You are ready for Phase 5 if:

- [ ] Created Deployments
- [ ] Scaled Applications
- [ ] Viewed ReplicaSets
- [ ] Performed Rolling Updates
- [ ] Monitored Rollouts
- [ ] Executed Rollbacks
- [ ] Configured Resource Limits
- [ ] Applied Deployment Strategies
- [ ] Troubleshot Failed Deployments

Congratulations.

You now understand Kubernetes workload management.

Next Phase:

05-Services-and-Networking.md