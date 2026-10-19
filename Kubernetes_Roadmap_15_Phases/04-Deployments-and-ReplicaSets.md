# Phase 4: Deployments and ReplicaSets

## Concepts

### Deployment

```yaml
kind: Deployment
```

### ReplicaSet

Ensures desired replicas.

### Scaling

```bash
kubectl scale deployment nginx --replicas=5
```

### Rolling Updates

```bash
kubectl rollout restart deployment nginx
```

### Rollback

```bash
kubectl rollout undo deployment nginx
```

## Lab

Deploy and scale Nginx.