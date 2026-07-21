# Kubernetes Node Affinity - Interview Revision

# What is Node Affinity?

**Node Affinity** is a scheduling rule that tells Kubernetes **on which Nodes a Pod can or should be scheduled** based on **Node labels**.

Think of it as:

> **"Schedule this Pod only on Nodes that match these labels."**

---

# Why do we need Node Affinity?

Sometimes all Nodes are not the same.

Example:

```
Node 1

SSD

High CPU

Production

------------------------

Node 2

HDD

Testing

------------------------

Node 3

GPU

AI Workloads
```

You may want:

- Database → SSD Nodes
- AI Application → GPU Nodes
- Production Pods → Production Nodes

Node Affinity helps Kubernetes place Pods accordingly.

---

# How does it work?

## Step 1: Label the Node

Example:

```bash
kubectl label node worker-1 disktype=ssd
```

Now the Node has:

```
worker-1

↓

disktype=ssd
```

---

## Step 2: Pod Requests That Label

```yaml
affinity:
  nodeAffinity:
```

Scheduler checks:

```
Node Label

↓

Matches?

↓

Yes

↓

Schedule Pod

------------------------

No

↓

Try Another Node
```

---

# Types of Node Affinity

| Type | Meaning |
|-------|---------|
| requiredDuringSchedulingIgnoredDuringExecution | Mandatory rule |
| preferredDuringSchedulingIgnoredDuringExecution | Preferred rule |

---

# 1. Required Node Affinity

## Meaning

The Pod **must** be scheduled on a matching Node.

If no matching Node exists:

```
Pod

↓

Pending
```

Example

```yaml
affinity:
  nodeAffinity:
    requiredDuringSchedulingIgnoredDuringExecution:
      nodeSelectorTerms:
      - matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
```

Flow

```
Scheduler

↓

Node has disktype=ssd ?

↓

Yes

↓

Schedule Pod

------------------------

No

↓

Pod Pending
```

---

# 2. Preferred Node Affinity

## Meaning

Kubernetes **tries** to schedule the Pod on a matching Node.

If none exists:

```
Still Schedule

On Another Node
```

Example

```yaml
affinity:
  nodeAffinity:
    preferredDuringSchedulingIgnoredDuringExecution:
    - weight: 100
      preference:
        matchExpressions:
        - key: disktype
          operator: In
          values:
          - ssd
```

Flow

```
SSD Node Available?

↓

Yes

↓

Use SSD Node

------------------------

No

↓

Use Any Available Node
```

---

# Understanding the Name

## requiredDuringSchedulingIgnoredDuringExecution

Break it down:

```
Required

↓

Must Match

------------------------

DuringScheduling

↓

Checked While Scheduling

------------------------

IgnoredDuringExecution

↓

If Node Labels Change Later,

Pod Keeps Running
```

Example:

```
worker-1

↓

disktype=ssd

↓

Pod Scheduled

↓

Later

↓

Label Removed

↓

Pod Continues Running
```

Kubernetes **does not evict** the Pod.

---

# Node Affinity Operators

| Operator | Meaning |
|-----------|---------|
| In | Label value must match one of the listed values |
| NotIn | Label value must not match |
| Exists | Label key must exist |
| DoesNotExist | Label key must not exist |
| Gt | Greater than |
| Lt | Less than |

---

# Common Commands

View Node Labels

```bash
kubectl get nodes --show-labels
```

Add Label

```bash
kubectl label node worker-1 disktype=ssd
```

Remove Label

```bash
kubectl label node worker-1 disktype-
```

Describe Node

```bash
kubectl describe node worker-1
```

---

# Node Selector vs Node Affinity

| Node Selector | Node Affinity |
|---------------|---------------|
| Simple key=value matching | Advanced scheduling rules |
| Equality only | Multiple operators (In, NotIn, Exists, etc.) |
| Less flexible | More flexible |
| Older feature | Recommended approach |

---

# Node Affinity vs Taints & Tolerations

| Node Affinity | Taints & Tolerations |
|---------------|----------------------|
| Attracts Pods to matching Nodes | Repels Pods from Nodes unless tolerated |
| Configured on Pods | Taint on Node, Toleration on Pod |
| Uses Node labels | Uses Taints & Tolerations |
| Controls preferred/required placement | Controls eligibility to run on a Node |

---

# Scheduling Flow

```
Pod Created

↓

Scheduler

↓

Read Node Affinity

↓

Matching Node Found?

↓

Yes

↓

Schedule Pod

------------------------

No

↓

Required?

↓

Yes

↓

Pod Pending

------------------------

No (Preferred)

↓

Schedule on Another Node
```

---

# Common Use Cases

- Database Pods on SSD Nodes
- GPU workloads on GPU Nodes
- Production workloads on Production Nodes
- Region/Zone-specific scheduling
- High-memory applications on large-memory Nodes

---

# Interview Questions

## What is Node Affinity?

A scheduling feature that places Pods on Nodes based on Node labels.

---

## Difference between Required and Preferred?

**Required**

- Mandatory
- No matching Node → Pod remains Pending

**Preferred**

- Best effort
- Falls back to another Node if needed

---

## What happens if Node labels change after scheduling?

Nothing.

Because of:

```
IgnoredDuringExecution
```

The Pod continues running.

---

## Does Node Affinity restart or evict Pods?

No.

It only affects **scheduling**, not running Pods.

---

## Difference between Node Selector and Node Affinity?

Node Selector supports only simple label matching.

Node Affinity supports advanced rules and operators.

---

## Difference between Node Affinity and Taints?

- **Node Affinity** tells Kubernetes **where a Pod wants to run**.
- **Taints** tell Kubernetes **which Pods should stay away** unless they have a matching toleration.

---

# Best Practices

- Prefer **Node Affinity** over `nodeSelector` for new workloads.
- Use **Required** only when the workload truly depends on specific Nodes.
- Use **Preferred** for optimization without reducing scheduling flexibility.
- Combine Node Affinity with Taints & Tolerations for stronger workload isolation.

---

# Memory Trick

```
Node Labels

↓

Node Affinity

↓

Find Matching Node

------------------------

Required

↓

Must Match

------------------------

Preferred

↓

Try to Match
```

---

# One-Line Revision

- **Node Affinity** schedules Pods based on **Node labels**
- **Required** = Must match or Pod stays Pending
- **Preferred** = Try to match, otherwise schedule elsewhere
- `IgnoredDuringExecution` means label changes after scheduling do not affect running Pods
- **Node Affinity attracts Pods**, while **Taints repel Pods**
- Node Affinity is the modern, flexible alternative to `nodeSelector`