# Kubernetes Resource Requests, Limits & Resource Quotas - Interview Revision

# Why do we need Resource Management?

A Kubernetes cluster has **limited CPU and Memory**.

Without limits:

```
Pod A

↓

Uses All CPU & Memory

↓

Other Pods Starve ❌
```

Resource management ensures **fair resource allocation**.

---

# Resource Management Components

```
Namespace
      │
      ▼
ResourceQuota

↓

Pod

↓

Requests

+

Limits
```

---

# 1. Requests

## What is a Request?

A **Request** is the **minimum CPU/Memory** a Pod needs.

Scheduler uses Requests to decide **where to place a Pod**.

Example

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "512Mi"
```

Meaning:

```
Scheduler

↓

Needs

0.5 CPU

512Mi RAM
```

---

# Why are Requests Important?

Scheduler checks:

```
Node Available?

↓

Yes

↓

Schedule Pod

-------------------

No

↓

Pod Remains Pending
```

---

# 2. Limits

## What is a Limit?

A **Limit** is the **maximum CPU/Memory** a container can use.

Example

```yaml
resources:
  limits:
    cpu: "1"
    memory: "1Gi"
```

Meaning:

```
Container

↓

Can use

Max 1 CPU

Max 1Gi RAM
```

---

# CPU Limit

If CPU usage exceeds the limit:

```
Container

↓

CPU Throttled

(Not Killed)
```

---

# Memory Limit

If Memory usage exceeds the limit:

```
Container

↓

OOMKilled

(Container Restarted)
```

---

# Requests vs Limits

| Requests | Limits |
|-----------|--------|
| Minimum Guaranteed Resources | Maximum Allowed Resources |
| Used by Scheduler | Enforced by Kubelet |
| Decides Scheduling | Prevents Resource Overuse |

---

# Resource Flow

```
Pod Created

↓

Scheduler

↓

Checks Requests

↓

Select Node

↓

Pod Running

↓

Container Uses Resources

↓

Kubelet Enforces Limits
```

---

# Important YAML

```yaml
resources:
  requests:
    cpu: "500m"
    memory: "512Mi"

  limits:
    cpu: "1"
    memory: "1Gi"
```

---

# CPU Units

```
1000m

=

1 CPU
```

Examples

| Value | Meaning |
|--------|---------|
| 100m | 0.1 CPU |
| 250m | 0.25 CPU |
| 500m | 0.5 CPU |
| 1000m | 1 CPU |

---

# Memory Units

Examples

```
128Mi

512Mi

1Gi

2Gi
```

---

# 3. ResourceQuota

## What is ResourceQuota?

A **ResourceQuota** limits the **total resources a Namespace can consume**.

It prevents one namespace from consuming the entire cluster.

---

# Example

Namespace

```
production
```

Quota

```
CPU

10 Cores

Memory

20Gi

Pods

50
```

No more resources can be created beyond these limits.

---

# ResourceQuota Scope

Applied on:

```
Namespace
```

Not on Pods.

---

# Common ResourceQuota Limits

- CPU
- Memory
- Pods
- Services
- PVCs
- Secrets
- ConfigMaps
- Deployments

---

# Important YAML

```yaml
spec:
  hard:
```

Example

```yaml
hard:
  requests.cpu: "4"
  requests.memory: 8Gi
  limits.cpu: "8"
  limits.memory: 16Gi
  pods: "20"
```

---

# 4. LimitRange

## What is LimitRange?

A **LimitRange** defines **default and minimum/maximum Requests & Limits** for Pods/Containers in a Namespace.
LimitRange is applied to an entire Namespace, not to a specific Deployment.

If a Pod doesn't specify resources:

```
LimitRange

↓

Automatically Applies Defaults
```

---

# Difference

## ResourceQuota

Controls **Namespace Total**.

---

## LimitRange

Controls **Individual Pods/Containers**.

---

# ResourceQuota vs LimitRange

| ResourceQuota | LimitRange |
|---------------|------------|
| Namespace-level | Pod/Container-level |
| Total Resource Limit | Default/Min/Max Resources |
| Prevents Namespace Exhaustion | Prevents Misconfigured Pods |

---

## Easy memory trick:

- Requests/Limits → Per Pod
- LimitRange → Rules for every Pod in a Namespace
- ResourceQuota → Budget for the entire Namespace
- requests & limits	-> Individual Pod/Container (inside Deployment/Pod YAML)	-> Define resources for that specific container
- LimitRange -> Entire Namespace	-> Sets default/min/max requests & limits for all Pods in the namespace
- ResourceQuota	-> Entire Namespace	-> Limits the total resources that all Pods combined can consume

---

# Common Commands

View Quotas

```bash
kubectl get resourcequota
```

or

```bash
kubectl get quota
```

Describe

```bash
kubectl describe quota
```

---

View LimitRanges

```bash
kubectl get limitrange
```

Describe

```bash
kubectl describe limitrange
```

---

# Scheduling Flow

```
Pod Created

↓

Requests Checked

↓

Enough Resources?

↓

Yes

↓

Node Selected

↓

Pod Running

↓

Limits Enforced

↓

ResourceQuota Checked

↓

Namespace Allowed
```

---

# Common Use Cases

- Multi-team clusters
- Prevent noisy neighbors
- Cost control
- Fair resource allocation
- Production clusters

---

# Interview Questions

## What is a Request?

Minimum CPU/Memory guaranteed for a container.

Used by the Scheduler.

---

## What is a Limit?

Maximum CPU/Memory a container can use.

Enforced by Kubelet.

---

## Difference between Request and Limit?

| Request | Limit |
|----------|--------|
| Scheduling | Runtime Enforcement |
| Minimum | Maximum |

---

## What happens if CPU exceeds Limit?

CPU is throttled.

Container continues running.

---

## What happens if Memory exceeds Limit?

Container is OOMKilled and usually restarted.

---

## What happens if Node doesn't have enough requested resources?

Pod remains:

```
Pending
```

---

## What is ResourceQuota?

Limits the **total resources** available to a Namespace.

---

## What is LimitRange?

Defines default/min/max Requests & Limits for Pods and Containers.

---

## Difference between ResourceQuota and LimitRange?

```
ResourceQuota

↓

Namespace Limit

-----------------------

LimitRange

↓

Pod/Container Limit
```

---

## Does Scheduler use Requests or Limits?

```
Requests
```

---

## Who enforces Limits?

```
kubelet
```

---

# Best Practices

- Always define Requests and Limits.
- Keep Requests close to actual usage.
- Avoid setting Limits much higher than Requests without reason.
- Use ResourceQuota in shared clusters.
- Use LimitRange to enforce defaults.
- Monitor usage with Metrics Server/Prometheus.

---

# Memory Trick

```
Requests

=

Minimum Needed

↓

Scheduler

-----------------------

Limits

=

Maximum Allowed

↓

kubelet

-----------------------

ResourceQuota

=

Namespace Budget

-----------------------

LimitRange

=

Per Pod Rules
```

---

# One-Line Revision

- **Request** = Minimum guaranteed CPU/Memory (used by Scheduler)
- **Limit** = Maximum CPU/Memory a container can use (enforced by kubelet)
- CPU limit exceeded → **Throttled**
- Memory limit exceeded → **OOMKilled**
- **ResourceQuota** limits total resources for a Namespace
- **LimitRange** defines default/min/max Requests & Limits for Pods/Containers
- Scheduler uses **Requests**, not Limits
- Best practice: Always configure Requests and Limits for production workloads