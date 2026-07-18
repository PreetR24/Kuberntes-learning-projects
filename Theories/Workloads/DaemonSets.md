# Kubernetes DaemonSet - Interview Revision

# What is a DaemonSet?

A **DaemonSet** is a Kubernetes controller that ensures **exactly one Pod runs on every eligible Node**.

As new nodes join the cluster, a Pod is automatically created on them.

As nodes leave the cluster, the corresponding Pod is removed.

---

# When to use DaemonSet?

Use DaemonSet when you need **one Pod per Node**.

Common Examples:

- Fluentd / Fluent Bit (Log Collection)
- Prometheus Node Exporter
- Datadog Agent
- New Relic Agent
- Security Agents
- CNI Plugins (Calico, Cilium)
- kube-proxy

---

# DaemonSet Architecture

```
DaemonSet
     │
     ▼
 ┌───────────────┐
 │               │
 ▼               ▼
Worker1      Worker2      Worker3
   │             │             │
   ▼             ▼             ▼
Pod            Pod           Pod
```

One Pod per Node.

---

# DaemonSet Behaviour

Cluster:

```
Worker1

Worker2

Worker3
```

DaemonSet

↓

```
Worker1 → 1 Pod

Worker2 → 1 Pod

Worker3 → 1 Pod
```

---

## New Node Added

```
Worker4 Joins Cluster

↓

DaemonSet

↓

Automatically Creates

↓

Pod on Worker4
```

---

## Node Removed

```
Worker2 Removed

↓

DaemonSet

↓

Deletes Corresponding Pod
```

---

# Scheduling

Default behavior:

```
Every Worker Node

↓

One Pod
```

Can be restricted using:

- Node Selector
- Node Affinity
- Taints & Tolerations

---

# DaemonSet vs Deployment

Deployment

```
Replicas: 3

↓

Scheduler decides

↓

Any 3 Nodes
```

Example:

```
Worker1 → 2 Pods

Worker2 → 1 Pod

Worker3 → 0 Pods
```

---

DaemonSet

```
One Pod

↓

Every Node
```

Example:

```
Worker1 → 1 Pod

Worker2 → 1 Pod

Worker3 → 1 Pod
```

---

# Important YAML Fields

```yaml
selector:
template:
updateStrategy:
```

---

## selector

Selects Pods managed by the DaemonSet.

Must match Pod labels.

---

## template

Blueprint for Pod creation.

Contains:

- containers
- image
- ports
- env
- volumes

---

## updateStrategy

Controls how Pods are updated.

Options:

- RollingUpdate (Default)
- OnDelete

---

# Rolling Update

```
Old Pod

↓

New Pod

↓

Next Node

↓

Repeat
```

Pods are updated one node at a time.

---

# Common Commands

Create

```bash
kubectl apply -f daemonset.yaml
```

List

```bash
kubectl get daemonsets
```

Describe

```bash
kubectl describe daemonset <name>
```

Delete

```bash
kubectl delete daemonset <name>
```

---

# DaemonSet vs Deployment vs StatefulSet

| Feature | Deployment | StatefulSet | DaemonSet |
|---------|------------|-------------|-----------|
| Purpose | Stateless Apps | Stateful Apps | One Pod per Node |
| Replica Count | User Defined | User Defined | Number of Nodes |
| Stable Identity | ❌ | ✅ | ❌ |
| Stable Storage | Optional | Dedicated PVC | Usually Not Required |
| Scheduling | Any Node | Any Node | Every Eligible Node |

---

# Common Use Cases

## Log Collection

```
Every Node

↓

Fluentd

↓

Collect Logs
```

---

## Monitoring

```
Every Node

↓

Node Exporter

↓

Expose Metrics
```

---

## Networking

```
Every Node

↓

kube-proxy

↓

Manage Networking
```

---

## Security

```
Every Node

↓

Security Agent

↓

Monitor Node
```

---

# Interview Questions

## What is a DaemonSet?

A Kubernetes controller that ensures one Pod runs on every eligible node.

---

## Does DaemonSet require replicas?

No.

Number of Pods = Number of eligible Nodes.

---

## What happens when a new Node joins?

DaemonSet automatically creates one Pod on the new node.

---

## What happens when a Node is removed?

The corresponding DaemonSet Pod is removed automatically.

---

## Can DaemonSet run on Control Plane Nodes?

By default, usually **No**, because control-plane nodes are tainted.

It **can** run there if appropriate **tolerations** are added.

---

## Can DaemonSet have two Pods on one Node?

Not from the **same DaemonSet**.

A single DaemonSet maintains **at most one Pod per eligible node**.

If you need two Pods:

- Create another DaemonSet, or
- Use a Deployment.

---

## Can DaemonSet be restricted to specific Nodes?

Yes.

Using:

- Node Selector
- Node Affinity
- Taints & Tolerations

---

## Does DaemonSet support Rolling Updates?

Yes.

Default strategy:

```
RollingUpdate
```

---

# Best Practices

- Use DaemonSets only for node-level services.
- Avoid running application workloads using DaemonSets.
- Use tolerations if DaemonSet should run on control-plane nodes.
- Restrict scheduling using node selectors when needed.

---

# Memory Trick

```
Deployment
=
Desired Number of Pods

StatefulSet
=
Desired Number of Stateful Pods

DaemonSet
=
One Pod Per Node
```

---

# One-Line Revision

- DaemonSet ensures one Pod runs on every eligible node
- Automatically handles node additions and removals
- Used for logging, monitoring, networking, and security agents
- No `replicas` field (Pods = eligible nodes)
- Supports Rolling Updates
- Can be restricted using nodeSelector, affinity, and tolerations
- Same DaemonSet cannot run multiple Pods on the same node