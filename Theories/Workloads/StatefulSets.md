# Kubernetes StatefulSet - Interview Revision

# What is a StatefulSet?

A **StatefulSet** is a Kubernetes controller used to manage **stateful applications** that require:

- Stable Pod Names
- Stable Network Identity
- Persistent Storage
- Ordered Deployment & Scaling

Examples:

- MySQL
- PostgreSQL
- MongoDB
- Cassandra
- Kafka
- ZooKeeper
- Elasticsearch

---

# When to use StatefulSet?

Use StatefulSet when an application requires:

- Persistent data
- Fixed Pod identity
- Stable hostname
- Ordered startup/shutdown

Do **NOT** use StatefulSet for stateless applications like:

- Node.js APIs
- React Apps
- Nginx
- Express Apps

Use **Deployment** instead.

---

# StatefulSet Architecture

```
StatefulSet
      │
      ▼
Pod-0
Pod-1
Pod-2
      │
      ▼
PVC-0
PVC-1
PVC-2
      │
      ▼
Persistent Volumes
```

Each Pod gets **its own dedicated PVC**.

---

# StatefulSet Features

✅ Stable Pod Names

```
mysql-0

mysql-1

mysql-2
```

Names never change.

---

✅ Stable Hostnames

```
mysql-0.mysql-service

mysql-1.mysql-service

mysql-2.mysql-service
```

---

✅ Stable Storage

Each Pod gets its own PVC.

```
mysql-0

↓

mysql-data-mysql-0

↓

PV
```

If Pod restarts, it gets the **same storage**.

---

✅ Ordered Deployment

Pods are created sequentially.

```
mysql-0

↓

mysql-1

↓

mysql-2
```

Next Pod starts only after the previous one becomes Ready.

---

✅ Ordered Deletion

Pods are deleted in reverse order.

```
mysql-2

↓

mysql-1

↓

mysql-0
```

---

# StatefulSet Lifecycle

```
Create StatefulSet

↓

Pod-0

↓

Ready

↓

Pod-1

↓

Ready

↓

Pod-2

↓

Ready
```

---

# Scaling

Scale Up

```
3 Pods

↓

Scale to 5

↓

mysql-3

↓

mysql-4
```

---

Scale Down

```
5 Pods

↓

Scale to 3

↓

Delete mysql-4

↓

Delete mysql-3
```

PVCs remain unless manually deleted.

---

# Persistent Storage

Uses

```
volumeClaimTemplates
```

Example:

```yaml
volumeClaimTemplates:
- metadata:
    name: mysql-data
```

Each Pod gets its own PVC.

Example:

```
mysql-data-mysql-0

mysql-data-mysql-1

mysql-data-mysql-2
```

---

# Headless Service

StatefulSet requires a **Headless Service**.

```
clusterIP: None
```

Purpose:

- Stable DNS
- Direct Pod communication

Example:

```
mysql-0.mysql-service

mysql-1.mysql-service

mysql-2.mysql-service
```

---

# Pod Identity

Unlike Deployment:

```
nginx-abc123

↓

nginx-xyz456
```

Names change.

StatefulSet:

```
mysql-0

↓

Restart

↓

mysql-0
```

Identity never changes.

---

# Important YAML Fields

```yaml
serviceName:
replicas:
selector:
template:
volumeClaimTemplates:
```

---

## serviceName

Headless Service name used for stable DNS.

---

## replicas

Desired number of Pods.

---

## selector

Selects Pods managed by StatefulSet.

Must match Pod labels.

---

## template

Blueprint for Pod creation.

---

## volumeClaimTemplates

Automatically creates one PVC per Pod.

---

# Deployment vs StatefulSet

| Deployment | StatefulSet |
|------------|-------------|
| Stateless Apps | Stateful Apps |
| Random Pod Names | Stable Pod Names |
| Shared/Optional Storage | Dedicated Storage per Pod |
| Parallel Pod Creation | Ordered Pod Creation |
| Parallel Deletion | Reverse Ordered Deletion |
| No Stable Identity | Stable Identity |

---

# StatefulSet vs ReplicaSet

| ReplicaSet | StatefulSet |
|-------------|-------------|
| Maintains replicas | Maintains replicas with identity |
| No stable storage | Dedicated PVC per Pod |
| Random Pod names | Fixed Pod names |

---

# Common Commands

Create

```bash
kubectl apply -f statefulset.yaml
```

List

```bash
kubectl get statefulsets
```

Describe

```bash
kubectl describe statefulset mysql
```

Scale

```bash
kubectl scale statefulset mysql --replicas=5
```

Delete

```bash
kubectl delete statefulset mysql
```

---

# Common Use Cases

- MySQL
- PostgreSQL
- MongoDB
- Cassandra
- Kafka
- ZooKeeper
- Elasticsearch
- Redis Cluster

---

# Interview Questions

## What is a StatefulSet?

A Kubernetes controller used for managing stateful applications that require stable identity and persistent storage.

---

## Why not use Deployment for MySQL?

Deployment recreates Pods with new identities.

Databases require:

- Stable Hostnames
- Stable Storage
- Ordered Startup

Hence StatefulSet.

---

## Why is a Headless Service required?

To provide stable DNS names for each Pod.

---

## Can Pods have fixed names?

Yes.

Example:

```
mysql-0

mysql-1

mysql-2
```

---

## Does each Pod get its own PVC?

Yes.

Created automatically using

```
volumeClaimTemplates
```

---

## What happens if mysql-0 crashes?

```
mysql-0 Deleted

↓

New mysql-0 Created

↓

Same PVC Attached

↓

Same Data Available
```

---

## Does scaling down delete PVCs?

No.

PVCs remain unless manually deleted.

---

## Can StatefulSet perform Rolling Updates?

Yes.

Pods are updated one by one in order.

---

# Best Practices

- Use StatefulSet only for stateful applications.
- Always use a Headless Service.
- Use Persistent Volumes for data.
- Don't use StatefulSet for stateless APIs.
- Configure Readiness Probes before scaling.

---

# Memory Trick

```
Deployment
=
Stateless

↓

Random Pod Names

↓

Random Identity

-------------------------

StatefulSet
=
Stateful

↓

Fixed Pod Names

↓

Fixed DNS

↓

Dedicated PVC

↓

Ordered Deployment
```

---

# One-Line Revision

- StatefulSet manages stateful applications
- Provides stable Pod names and stable DNS
- Uses Headless Service (`clusterIP: None`)
- Creates one dedicated PVC per Pod using `volumeClaimTemplates`
- Pods are created in order and deleted in reverse order
- Best for databases and distributed systems
- Deployment → Stateless | StatefulSet → Stateful