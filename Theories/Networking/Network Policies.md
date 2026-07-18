# Kubernetes Network Policies - Interview Revision

# What is a NetworkPolicy?

A **NetworkPolicy** is a Kubernetes resource used to **control network traffic** between Pods.

It acts like a **firewall for Pods**.

By default, Kubernetes allows **all Pod-to-Pod communication**.

NetworkPolicies are used to restrict:

- Incoming traffic (Ingress)
- Outgoing traffic (Egress)

---

# Why do we need NetworkPolicies?

Without NetworkPolicy:

```
Pod A

↓

Pod B ✅

↓

Pod C ✅

↓

Pod D ✅
```

Every Pod can communicate with every other Pod.

This is insecure.

---

With NetworkPolicy:

```
Pod A

↓

Pod B ✅

↓

Pod C ❌

↓

Pod D ❌
```

Only allowed communication is permitted.

---

# NetworkPolicy Architecture

```
Pod
 │
 ▼
NetworkPolicy
 │
 ├── Ingress Rules
 └── Egress Rules
```

---

# Important Requirement

**NetworkPolicies only work if your CNI plugin supports them.**

Supported CNIs:

- Calico ✅
- Cilium ✅
- Weave Net ✅

Not all CNIs enforce NetworkPolicies.

---

# Types of Traffic

## 1. Ingress

Incoming traffic **to a Pod**.

```
Pod A

↓

Pod B
```

Traffic entering Pod B.

---

## 2. Egress

Outgoing traffic **from a Pod**.

```
Pod A

↓

Database
```

Traffic leaving Pod A.

---

# Default Behaviour

Without NetworkPolicy:

```
All Traffic Allowed
```

Once a Pod is selected by a NetworkPolicy:

```
Everything Denied

↓

Only Explicitly Allowed Rules Work
```

---

# How does NetworkPolicy select Pods?

Using Labels.

Example:

```yaml
podSelector:
  matchLabels:
    app: backend
```

Only Pods with

```
app=backend
```

are affected.

---

# Important YAML Fields

```yaml
podSelector:
policyTypes:
ingress:
egress:
```

---

## podSelector

Selects Pods to protect.

Example

```yaml
podSelector:
  matchLabels:
    app: backend
```

---

## policyTypes

Defines which traffic is controlled.

Options:

```yaml
Ingress
```

```yaml
Egress
```

or

```yaml
Ingress
Egress
```

---

## ingress

Defines allowed incoming traffic.

---

## egress

Defines allowed outgoing traffic.

---

# Ingress Example

```
Frontend Pod

↓

Backend Pod
```

Allowed

```
Frontend

↓

Backend ✅
```

Blocked

```
Database

↓

Backend ❌
```

---

# Egress Example

```
Backend

↓

Database ✅

↓

Internet ❌
```

---

# Common Rules

## Allow from Specific Pods

```
Frontend

↓

Backend
```

---

## Allow from Namespace

```
Namespace A

↓

Namespace B
```

---

## Allow Specific Port

```
TCP

↓

3306
```

Only MySQL traffic allowed.

---

## Allow CIDR Block

Example

```
192.168.1.0/24
```

---

# Default Deny Policy

```
NetworkPolicy

↓

Select Pod

↓

No Rules

↓

All Traffic Blocked
```

Common security practice.

---

# Communication Flow

Without Policy

```
Pod A

↓

Pod B ✅

↓

Pod C ✅

↓

Pod D ✅
```

With Policy

```
Pod A

↓

Backend ✅

↓

Database ❌

↓

Redis ❌
```

---

# Common Commands

Create

```bash
kubectl apply -f networkpolicy.yaml
```

List

```bash
kubectl get networkpolicies
```

or

```bash
kubectl get netpol
```

Describe

```bash
kubectl describe networkpolicy <name>
```

Delete

```bash
kubectl delete networkpolicy <name>
```

---

# NetworkPolicy vs Service

| Service | NetworkPolicy |
|----------|---------------|
| Routes traffic | Controls traffic |
| Load Balancing | Firewall Rules |
| Discovers Pods | Restricts Pod Communication |

---

# NetworkPolicy vs Ingress

| Ingress | NetworkPolicy |
|----------|---------------|
| External HTTP Routing | Internal Traffic Security |
| Routes to Services | Allows/Denies Pod Traffic |

---

# Common Use Cases

## Backend Isolation

```
Frontend

↓

Backend ✅

Database ❌
```

---

## Database Protection

```
Backend

↓

MySQL ✅

Everything Else ❌
```

---

## Namespace Isolation

```
Dev Namespace

↓

Prod Namespace ❌
```

---

## Zero Trust Networking

Default

```
Block Everything

↓

Allow Only Required Traffic
```

---

# Interview Questions

## What is a NetworkPolicy?

A Kubernetes resource that controls network communication to and from Pods.

---

## What does NetworkPolicy work on?

Pods.

Selected using Labels.

---

## What are the two policy types?

- Ingress
- Egress

---

## What happens without any NetworkPolicy?

All Pods can communicate with each other.

---

## What happens when a Pod is selected by a NetworkPolicy?

Default becomes:

```
Deny All

↓

Only Explicitly Allowed Traffic
```

---

## Does NetworkPolicy affect Services?

No.

It controls **Pod traffic**, not Service creation or routing.

---

## Can NetworkPolicy block Internet access?

Yes.

Using **Egress Rules**.

---

## Can NetworkPolicy allow only MySQL traffic?

Yes.

Example:

```
TCP

3306
```

---

## Does Kubernetes enforce NetworkPolicies by itself?

No.

A compatible **CNI plugin** (e.g., Calico, Cilium) is required.

---

# Best Practices

- Use **Default Deny** policies.
- Allow only required communication.
- Protect databases using Ingress rules.
- Restrict Internet access with Egress rules.
- Use Labels for fine-grained control.
- Verify your CNI supports NetworkPolicies.

---

# Memory Trick

```
Service

↓

Routes Traffic

--------------------

NetworkPolicy

↓

Controls Traffic

--------------------

Ingress

↓

Incoming Traffic

--------------------

Egress

↓

Outgoing Traffic
```

---

# One-Line Revision

- NetworkPolicy is a firewall for Pods
- Controls Ingress (incoming) and Egress (outgoing) traffic
- Uses Labels (`podSelector`) to select Pods
- Without NetworkPolicy, all Pod communication is allowed
- Once a Pod is selected, only explicitly allowed traffic is permitted
- Requires a CNI that supports NetworkPolicies (e.g., Calico, Cilium)
- Implements Zero Trust networking by allowing only necessary communication