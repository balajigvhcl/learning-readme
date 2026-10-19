# Phase 3: Working with Kubernetes

## Topics

### Pods

```yaml
kind: Pod
```

### YAML Files

- apiVersion
- kind
- metadata
- spec

### Labels

```yaml
labels:
  app: nginx
```

### Selectors

```yaml
selector:
  app: nginx
```

### Common Commands

```bash
kubectl get pods
kubectl describe pod
kubectl logs
kubectl exec
```

## Lab

Deploy single Nginx Pod.