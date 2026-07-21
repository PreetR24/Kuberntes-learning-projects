# Kubernetes Taints & Tolerations

## Why do we need Taints & Tolerations?

By default, Kubernetes Scheduler can place Pods on **any node** that has enough resources.

```
Scheduler
     │
     ▼
Any Available Node
```

But sometimes we have special nodes:

- GPU Nodes
- Database Nodes
- High Memory Nodes
- Monitoring Nodes

We don't want normal Pods running there.

Taints & Tolerations solve this problem.

---

# What is a Taint?

A **Taint is applied on a Node**.

It tells Kubernetes:

> "Don't schedule Pods here unless they are allowed."

Think of it as:

```
Node

⚠️ Restricted Area

Only Authorized Pods Allowed
```

Example:

```bash
kubectl taint nodes worker3 gpu=true:NoSchedule
```

Meaning:

```
worker3

gpu=true

NoSchedule
```

---

# Taint Syntax

```
key=value:effect
```

Example:

```
gpu=true:NoSchedule
│    │          │
│    │          └── Effect
│    │
│    └──────────── Value
│
└───────────────── Key
```

---

# What is a Toleration?

A **Toleration is applied on a Pod**.

It tells Kubernetes:

> "This Pod is allowed to run on a node having this taint."

Example:

```yaml
tolerations:
- key: gpu
  operator: Equal
  value: "true"
  effect: NoSchedule
```

---

# Taint vs Toleration

```
Node
│
├── Taint
│
▼
"No Pods Allowed"

          │

Pod
│
└── Toleration

↓

Allowed
```

---

# Important Rule

## Taint

Repels Pods.

```
Node

↓

"No Entry"
```

## Toleration

Allows Pod to enter.

```
Pod

↓

Permission Granted
```

---

# Very Important

A Toleration **does NOT force** a Pod onto that node.

It only allows it.

Scheduler may still choose another node.

Example

```
Worker1

Worker2

Worker3 (GPU)
```

Pod has GPU toleration.

Scheduler can still place it on

```
Worker1

or

Worker2

or

Worker3
```

if no nodeSelector/nodeAffinity is used.

---

# If you want to force the Pod?

Use:

- Node Selector
- Node Affinity

along with

- Toleration

---

# Taint Effects

There are **3 Effects**.

---

## 1. NoSchedule ⭐

Most Common

Meaning

```
Do NOT schedule new Pods.
```

Existing Pods stay.

```
Before

Worker3

PodA
PodB

↓

Apply Taint

↓

PodA ✅

PodB ✅

New Pod ❌
```

---

## 2. PreferNoSchedule

Soft Restriction.

Meaning

```
Try NOT to schedule Pods here.
```

Scheduler avoids the node.

If there is no better option,

it may still schedule.

Think of it as

```
Please Avoid
```

instead of

```
Strictly Forbidden
```

---

## 3. NoExecute

Most Powerful.

Meaning

```
Don't schedule new Pods

AND

Remove existing Pods
```

Example

```
Worker3

PodA

PodB

↓

Apply NoExecute

↓

PodA Removed

PodB Removed
```

unless Pods have matching tolerations.

---

# Toleration Fields

## key

Matches the taint key.

Example

```
gpu=true
```

Pod

```yaml
key: gpu
```

---

## value

Matches taint value.

Example

```
gpu=true
```

Pod

```yaml
value: "true"
```

---

## operator

There are **2 Operators**.

---

### Equal

Both key and value must match.

Node

```
gpu=true
```

Pod

```yaml
key: gpu
value: "true"
operator: Equal
```

Result

```
Match ✅
```

---

### Exists

Ignores value.

Only key must exist.

Node

```
gpu=anything
```

Pod

```yaml
key: gpu
operator: Exists
```

Result

```
Match ✅
```

---

## effect

Must match the node's taint effect.

Node

```
NoSchedule
```

Pod

```yaml
effect: NoSchedule
```

---

# Complete Example

## Step 1

Taint the node

```bash
kubectl taint nodes worker3 gpu=true:NoSchedule
```

Worker3

```
gpu=true

NoSchedule
```

---

## Step 2

Normal Pod

```yaml
kind: Pod
```

Result

```
Cannot be scheduled ❌
```

---

## Step 3

GPU Pod

```yaml
tolerations:
- key: gpu
  operator: Equal
  value: "true"
  effect: NoSchedule
```

Result

```
Allowed ✅
```

---

# Scheduling Flow

```
Pod Created
      │
      ▼
Scheduler Checks Nodes
      │
      ▼
Node Has Taint?
      │
 ┌────┴─────┐
 │          │
 No         Yes
 │          │
 ▼          ▼
Schedule   Pod Has Matching
            Toleration?
             │
      ┌──────┴──────┐
      │             │
      No            Yes
      │             │
      ▼             ▼
 Reject         Scheduling Allowed
```

---

# Production Examples

## GPU Nodes

```
gpu=true:NoSchedule
```

Only AI/ML Pods should tolerate.

---

## Database Nodes

```
database=true:NoSchedule
```

Only MySQL/PostgreSQL Pods.

---

## Monitoring Nodes

```
monitoring=true:NoSchedule
```

Only Prometheus/Grafana Pods.

---

## Dedicated Build Nodes

```
ci=true:NoSchedule
```

Only Jenkins/GitLab Runner Pods.

---

# Taints vs Node Selector vs Node Affinity

| Feature | Purpose |
|----------|---------|
| Taint | Keeps Pods away from a Node |
| Toleration | Allows a Pod onto a tainted Node |
| Node Selector | Forces Pod onto specific Nodes |
| Node Affinity | Advanced Node Selection Rules |

---

# Interview Questions

## Can a Pod without toleration run on a tainted node?

No.

---

## Can a Pod with toleration run on a tainted node?

Yes.

---

## Does toleration force scheduling?

No.

It only gives permission.

---

## How to guarantee scheduling on a node?

Use:

```
Toleration

+

Node Selector

or

Node Affinity
```

---

## Difference between NoSchedule and NoExecute?

### NoSchedule

```
Existing Pods → Stay

New Pods → Blocked
```

### NoExecute

```
Existing Pods → Evicted

New Pods → Blocked
```

---

# Memory Trick

```
Taint
=
Node says

"Stay Away"

↓

Toleration
=
Pod says

"I Have Permission"

↓

Node Selector / Affinity
=
Pod says

"I Want THIS Node"
```

---

# Final Summary

| Concept | Applied On | Meaning |
|----------|------------|---------|
| Taint | Node | Repels Pods |
| Toleration | Pod | Allows Pod to ignore a Taint |
| NoSchedule | Taint Effect | Blocks new Pods |
| PreferNoSchedule | Taint Effect | Tries to avoid scheduling |
| NoExecute | Taint Effect | Evicts existing Pods and blocks new Pods |
| Equal | Operator | Key and Value must match |
| Exists | Operator | Only Key must exist |

---

# One-Line Revision

- **Taint → Restricts a Node**
- **Toleration → Gives a Pod permission**
- **Node Selector/Affinity → Chooses the Node**
- **Taint + Toleration ≠ Guaranteed Scheduling**
- **Guaranteed Scheduling = Toleration + Node Selector/Affinity**