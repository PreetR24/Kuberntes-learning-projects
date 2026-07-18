# Kubernetes Labels, Selectors & Annotations - Interview Revision

# 1. Labels

## What are Labels?

Labels are **key-value pairs** attached to Kubernetes objects to identify and organize them.

Examples:

```yaml
labels:
  app: nginx
  env: production
  tier: frontend
```

---

## Why Labels?

- Identify resources
- Group resources
- Used by Selectors
- Organize applications

---

## Label Characteristics

- Key-Value pair
- Multiple labels allowed
- Can be added to almost every Kubernetes resource
- Can be modified anytime

Example:

```yaml
metadata:
  labels:
    app: nginx
    env: dev
    version: v1
```

---

# 2. Selectors

## What are Selectors?

Selectors are used to **find resources based on Labels**.

Simply:

```
Labels

↓

Selectors

↓

Matching Resources
```

---

## Why Selectors?

Controllers use Selectors to know

- Which Pods belong to them
- Which Pods should receive traffic

---

## Example

Pods

```
Pod1
app=nginx

Pod2
app=nginx

Pod3
app=redis
```

Selector

```yaml
selector:
  app: nginx
```

Matches

```
Pod1

Pod2
```

Not

```
Pod3
```

---

## Types of Selectors

### Equality-Based

```
app=nginx

env=prod
```

---

### Set-Based

```
In

NotIn

Exists

DoesNotExist
```

Example

```yaml
matchExpressions:
- key: env
  operator: In
  values:
    - prod
    - stage
```

---

# Where are Selectors Used?

- Deployment
- ReplicaSet
- Service
- DaemonSet
- StatefulSet
- Job (optional)

---

# Deployment Example

```
Deployment

↓

selector:
app=nginx

↓

Find Matching Pods
```

---

# Service Example

```
Service

↓

selector:
app=nginx

↓

Load Balance

↓

Matching Pods
```

---

# Important Rule

Deployment selector must match Pod labels.

Example

```yaml
selector:
  matchLabels:
    app: nginx
```

Pod

```yaml
labels:
  app: nginx
```

If they don't match,

Deployment will not manage the Pods.

---

# 3. Annotations

## What are Annotations?

Annotations are **key-value pairs used to store metadata**.

Unlike Labels,

they are **NOT used for selection**.

---

## Why Annotations?

Store information like:

- Build Version
- Git Commit
- Owner
- Description
- Ingress Configuration
- Monitoring Config

---

## Example

```yaml
annotations:
  owner: preet
  build: "1.0.2"
  git-commit: abc123
```

---

## Another Example

Ingress Rewrite

```yaml
annotations:
  nginx.ingress.kubernetes.io/rewrite-target: /
```

Used by NGINX Ingress Controller.

---

# Labels vs Annotations

| Labels | Annotations |
|---------|-------------|
| Used to identify resources | Used to store metadata |
| Used by Selectors | Not used by Selectors |
| Small identifying information | Large descriptive information |
| Queryable | Not queryable |

---

# Labels vs Selectors

```
Pod

Label

↓

app=nginx

↓

Deployment / Service

↓

Selector

↓

app=nginx
```

Labels identify.

Selectors search.

---

# Complete Flow

```
Deployment
      │
      ▼
Selector

↓

Find Pods

↓

Matching Labels

↓

Manage Pods
```

---

```
Service
      │
      ▼
Selector

↓

Matching Pods

↓

Load Balance Traffic
```

---

# Common Commands

Show Labels

```bash
kubectl get pods --show-labels
```

Add Label

```bash
kubectl label pod nginx env=dev
```

Remove Label

```bash
kubectl label pod nginx env-
```

Filter by Label

```bash
kubectl get pods -l app=nginx
```

Show Annotations

```bash
kubectl describe pod <pod-name>
```

---

# Interview Questions

## What are Labels?

Key-value pairs used to identify and organize Kubernetes resources.

---

## What are Selectors?

Mechanism used to find resources based on Labels.

---

## What are Annotations?

Key-value pairs used to store metadata.

They are **not used for selecting resources**.

---

## Where are Selectors used?

- Deployment
- ReplicaSet
- Service
- DaemonSet
- StatefulSet

---

## What happens if Deployment selector doesn't match Pod labels?

Deployment will not manage those Pods.

---

## Can one Pod have multiple Labels?

Yes.

Example

```yaml
labels:
  app: nginx
  env: prod
  version: v1
```

---

## Can multiple Pods have the same Label?

Yes.

That's how Deployments and Services group Pods.

---

# Best Practices

- Keep Labels short and meaningful.
- Use consistent naming (`app`, `env`, `tier`, `version`).
- Use Labels for grouping.
- Use Annotations for configuration and descriptive metadata.
- Never store sensitive data in Labels or Annotations.

---

# Memory Trick

```
Labels
=
Identity

↓

Selectors
=
Search

↓

Annotations
=
Extra Information
```

---

# One-Line Revision

- Labels = Identify resources (key-value pairs)
- Selectors = Find resources using Labels
- Annotations = Store metadata (not used for selection)
- Deployments and Services use Selectors to manage Pods
- Selector must match Pod Labels
- One Pod can have multiple Labels
- Multiple Pods can share the same Label
- Annotations are commonly used by tools like Ingress Controllers, monitoring, and CI/CD