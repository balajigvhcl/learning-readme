# Phase 6: Namespaces

## Why Namespaces?

Environment isolation.

Examples:

- dev
- test
- qa
- prod

## Commands

```bash
kubectl create ns dev
kubectl get pods -n dev
```

## Lab

Deploy application into multiple namespaces.