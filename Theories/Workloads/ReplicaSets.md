# Kubernetes ReplicaSet - Interview Revision

# What is a ReplicaSet?

A **ReplicaSet** is a Kubernetes controller that ensures a specified number of **identical Pod replicas** are always running.

Its primary responsibility is **maintaining the desired number of Pods**.

---

# Why do we need ReplicaSet?

Suppose:

```
Desired Pods = 3
```

Currently:

```
Pod1 ✅
Pod2 ✅
Pod3 ❌ (Deleted)
```

ReplicaSet detects this and immediately creates:

```
Pod4 ✅
```

Desired state is restored.

---

# ReplicaSet Architecture

```
ReplicaSet
      │
      ▼
Desired Replicas = 3
      │
      ▼
Pod1
Pod2
Pod3
```

---

# Responsibilities

- Maintain desired replica count
- Create missing Pods
- Delete extra Pods
- Self-healing
- Monitor Pod health

---

# ReplicaSet Workflow

```
ReplicaSet Created

↓

Create Desired Pods

↓

Monitor Pods

↓

Pod Deleted?

↓

Create New Pod

↓

Desired State Restored
```

---

# Self-Healing

```
Pod Crashes

↓

ReplicaSet Detects

↓

Creates New Pod

↓

Application Continues Running
```

---

# Scaling

Increase replicas

```
replicas: 2

↓

replicas: 5

↓

Create 3 New Pods
```

---

Decrease replicas

```
replicas: 5

↓

replicas: 2

↓

Delete 3 Pods
```

---

# Important YAML Fields

```yaml
replicas:
selector:
template:
```

---

## replicas

Desired number of Pods.

```yaml
replicas: 3
```

---

## selector

Selects Pods managed by ReplicaSet.

```yaml
selector:
  matchLabels:
    app: nginx
```

Must match Pod labels.

---

## template

Blueprint for creating Pods.

Contains:

- labels
- containers
- image
- ports
- env
- volumes

---

# Selector Matching

ReplicaSet

```yaml
selector:
  matchLabels:
    app: nginx
```

Pods

```yaml
labels:
  app: nginx
```

↓

ReplicaSet manages these Pods.

---

# Common Commands

Create

```bash
kubectl apply -f replicaset.yaml
```

List

```bash
kubectl get replicasets
```

or

```bash
kubectl get rs
```

Describe

```bash
kubectl describe rs <replicaset-name>
```

Delete

```bash
kubectl delete rs <replicaset-name>
```

Scale

```bash
kubectl scale rs <replicaset-name> --replicas=5
```

---

# ReplicaSet vs Deployment

| ReplicaSet | Deployment |
|-------------|------------|
| Maintains Pods | Manages ReplicaSets |
| Self-healing | Self-healing |
| Scaling | Scaling |
| No Rollback | Rollback Supported |
| No Rolling Update | Rolling Update Supported |
| Rarely used directly | Used in Production |

---

# ReplicaSet vs Pod

| ReplicaSet | Pod |
|-------------|-----|
| Creates Pods | Runs application |
| Maintains replicas | Single instance |
| Self-healing | No self-healing |

---

# Deployment Relationship

```
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
```

Important:

**Deployment does NOT create Pods directly.**

It creates a ReplicaSet, and the ReplicaSet creates the Pods.

---

# Rolling Update

ReplicaSet itself **does NOT support Rolling Updates**.

During a Deployment update:

```
Deployment

↓

Creates New ReplicaSet

↓

New ReplicaSet Creates New Pods

↓

Old ReplicaSet Scaled Down
```

Multiple ReplicaSets may temporarily exist during an update.

---

# Interview Questions

## What is a ReplicaSet?

A Kubernetes controller that ensures the desired number of identical Pods are always running.

---

## What is the primary responsibility of ReplicaSet?

Maintain the desired number of Pod replicas.

---

## Does ReplicaSet create Pods?

Yes.

---

## Does Deployment create Pods directly?

No.

```
Deployment

↓

ReplicaSet

↓

Pods
```

---

## What happens if a Pod crashes?

ReplicaSet automatically creates a replacement Pod.

---

## Can ReplicaSet perform Rollback?

No.

Rollback is provided by **Deployment**.

---

## Can ReplicaSet perform Rolling Updates?

No.

Rolling Updates are managed by **Deployment**.

---

## Why don't we usually create ReplicaSets directly?

Because Deployment provides everything ReplicaSet does, plus:

- Rolling Updates
- Rollback
- Version History

Deployment internally manages ReplicaSets.

---

# Best Practices

- Use **Deployment** instead of creating ReplicaSets directly.
- Create ReplicaSets manually only for learning or very specific use cases.
- Ensure selector matches Pod labels.
- Use Deployments for production workloads.

---

# Memory Trick

```
ReplicaSet

↓

Maintains

↓

Desired Number of Pods

----------------------

Deployment

↓

Manages

↓

ReplicaSets

↓

Pods
```

---

# One-Line Revision

- ReplicaSet maintains the desired number of Pod replicas
- Provides self-healing and scaling
- Creates replacement Pods if one fails
- Uses selectors to identify Pods
- Does not support rolling updates or rollback
- Deployment manages ReplicaSets and should be preferred in production