# Kubernetes Deployment - Interview Revision

## What is a Deployment?

A **Deployment** is a Kubernetes controller that manages the lifecycle of Pods.

It provides:

- Declarative updates
- Replica management
- Rolling updates
- Rollbacks
- Self-healing
- Scaling

---

# Deployment Architecture

```
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
      │
      ▼
Containers
```

Deployment **does not create Pods directly**.

It creates a **ReplicaSet**, which creates and maintains the Pods.

---

# Responsibilities

- Create Pods
- Maintain desired replica count
- Replace failed Pods
- Rolling Updates
- Rollback to previous version
- Scale Pods
- Ensure desired state

---

# Deployment Workflow

```
kubectl apply

↓

Deployment Created

↓

ReplicaSet Created

↓

Pods Created

↓

Application Running
```

---

# Self-Healing

If a Pod crashes:

```
Pod Deleted

↓

ReplicaSet Detects

↓

Creates New Pod

↓

Desired State Restored
```

---

# Scaling

Increase replicas

```
replicas: 2

↓

replicas: 5

↓

ReplicaSet Creates 3 More Pods
```

Decrease replicas

```
replicas: 5

↓

replicas: 2

↓

ReplicaSet Deletes 3 Pods
```

---

# Rolling Update

Updating image/version:

```
Old Pods (v1)

↓

Create New Pods (v2)

↓

Verify Health

↓

Delete Old Pods

↓

Deployment Complete
```

No downtime (if configured correctly).

---

# Rollback

If a deployment fails:

```
Version 1

↓

Version 2

↓

Problem Detected

↓

Rollback

↓

Version 1 Restored
```

---

# Deployment Strategy

## RollingUpdate (Default)

```
Old Pods

↓

Gradually Replace

↓

New Pods
```

Advantages:

- No downtime
- Safe updates

---

## Recreate

```
Delete All Old Pods

↓

Create New Pods
```

Advantages:

- Simpler

Disadvantage:

- Downtime

---

# Important YAML Fields

```yaml
replicas:
selector:
template:
strategy:
```

---

## replicas

Desired number of Pods.

Example:

```yaml
replicas: 3
```

---

## selector

Selects Pods managed by the Deployment.

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

## strategy

Deployment update strategy.

Example:

```yaml
strategy:
  type: RollingUpdate
```

---

# Common Commands

Create Deployment

```bash
kubectl apply -f deployment.yaml
```

List Deployments

```bash
kubectl get deployments
```

Describe Deployment

```bash
kubectl describe deployment <deployment-name>
```

Scale Deployment

```bash
kubectl scale deployment nginx --replicas=5
```

Update Image

```bash
kubectl set image deployment/nginx nginx=nginx:1.27
```

View Rollout Status

```bash
kubectl rollout status deployment nginx
```

View Rollout History

```bash
kubectl rollout history deployment nginx
```

Rollback

```bash
kubectl rollout undo deployment nginx
```

Restart Deployment

```bash
kubectl rollout restart deployment nginx
```

Delete Deployment

```bash
kubectl delete deployment nginx
```

---

# Deployment vs ReplicaSet

| Deployment | ReplicaSet |
|------------|------------|
| Manages ReplicaSets | Manages Pods |
| Supports Rollback | No Rollback |
| Supports Rolling Updates | No Rolling Updates |
| Used in Production | Rarely created directly |

---

# Deployment vs Pod

| Deployment | Pod |
|------------|-----|
| Creates & manages Pods | Runs application |
| Self-healing | No self-healing |
| Supports scaling | Single instance |
| Supports updates | No update management |

---

# Deployment Lifecycle

```
Create Deployment

↓

ReplicaSet Created

↓

Pods Created

↓

Pods Running

↓

Application Serving Traffic
```

Update:

```
New Image

↓

New ReplicaSet

↓

Rolling Update

↓

Old ReplicaSet Scaled Down
```

---

# Interview Questions

## What is a Deployment?

A Deployment is a Kubernetes controller that manages ReplicaSets and provides declarative updates, scaling, rollback, and self-healing for Pods.

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

## What is the purpose of ReplicaSet?

Maintains the desired number of Pod replicas.

---

## What happens if a Pod crashes?

ReplicaSet automatically creates a replacement Pod.

---

## What happens during Rolling Update?

New Pods are created gradually while old Pods are terminated, ensuring minimal or no downtime.

---

## Can Deployment perform Rollback?

Yes.

Using:

```bash
kubectl rollout undo deployment <deployment-name>
```

---

## Can Deployment manage Stateful Applications?

No.

Use **StatefulSet** for stateful applications.

---

## Which update strategy is used by default?

```
RollingUpdate
```

---

## Can Deployment scale Pods?

Yes.

Using:

```bash
kubectl scale deployment <deployment-name> --replicas=<count>
```

---

# Best Practices

- Use Deployments for stateless applications.
- Avoid managing Pods directly.
- Use RollingUpdate for production.
- Configure readiness/liveness probes.
- Use resource requests and limits.
- Keep replica count greater than one for high availability.

---

# Memory Trick

```
Deployment
      │
      ▼
ReplicaSet
      │
      ▼
Pods
      │
      ▼
Containers
```

Remember:

- Deployment manages ReplicaSets
- ReplicaSet manages Pods
- Pods run Containers

---

# One-Line Revision

- Deployment manages the complete lifecycle of Pods
- Creates ReplicaSets, which create Pods
- Provides self-healing, scaling, rolling updates, and rollback
- RollingUpdate is the default strategy
- Deployment is best suited for stateless applications
- Never manage Pods directly in production