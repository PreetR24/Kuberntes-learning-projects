# Kubernetes HPA & VPA - Interview Revision

# What is Autoscaling?

Autoscaling allows Kubernetes to automatically adjust resources based on workload.

There are two main types:

- **HPA (Horizontal Pod Autoscaler)** → Changes the **number of Pods**
- **VPA (Vertical Pod Autoscaler)** → Changes the **CPU/Memory of a Pod**

---

# Horizontal vs Vertical Scaling

```
Horizontal Scaling

1 Pod

↓

3 Pods

↓

5 Pods

(Add More Pods)
```

```
Vertical Scaling

1 Pod

↓

More CPU

More Memory

(Same Pod)
```

---

# HPA (Horizontal Pod Autoscaler)

## What is HPA?

HPA automatically **increases or decreases the number of Pod replicas** based on metrics such as CPU or Memory utilization.

---

# How HPA Works

```
Users

↓

Traffic Increases

↓

CPU Usage Increases

↓

HPA Detects High Usage

↓

Increase Replicas

↓

Load Distributed
```

---

## Example

Initial State

```
3 Pods

CPU Usage

90%
```

Target CPU

```
50%
```

HPA scales

```
3 Pods

↓

6 Pods
```

Now traffic is shared among more Pods.

---

## Scale Down

```
Traffic Drops

↓

CPU Usage

15%

↓

HPA Detects Low Usage

↓

6 Pods

↓

3 Pods
```

---

# HPA Flow

```
Pod Metrics

↓

Metrics Server

↓

HPA Controller

↓

Above Target?

↓

Yes

↓

Increase Replicas

------------------------

Below Target?

↓

Decrease Replicas
```

---

# HPA YAML

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler

spec:
  minReplicas: 2
  maxReplicas: 10

  metrics:
  - type: Resource
    resource:
      name: cpu

      target:
        type: Utilization
        averageUtilization: 50
```

---

# Important HPA Fields

| Field | Meaning |
|--------|---------|
| minReplicas | Minimum number of Pods |
| maxReplicas | Maximum number of Pods |
| averageUtilization | Target CPU/Memory usage |
| metrics | Metric used for scaling |

---

# Requirements for HPA

HPA requires:

- Metrics Server
- CPU/Memory Requests configured
- Deployment/ReplicaSet/StatefulSet

Without the Metrics Server:

```
HPA

↓

Cannot Read Metrics

↓

No Scaling
```

---

# Vertical Pod Autoscaler (VPA)

## What is VPA?

VPA automatically **adjusts CPU and Memory requests/limits** of Pods based on actual usage.

Instead of adding Pods, it gives **more or less resources to existing Pods**.

---

# How VPA Works

```
Application

↓

Needs More Memory

↓

VPA Detects Usage

↓

Increase Memory Request

↓

Restart Pod (Usually)

↓

Pod Starts With New Resources
```

---

## Example

Initial Pod

```
CPU

500m

Memory

512Mi
```

Application grows.

VPA recommends:

```
CPU

1

Memory

2Gi
```

The Pod is recreated with the new resources.

---

# VPA Flow

```
Pod Metrics

↓

VPA Recommender

↓

Recommend CPU/Memory

↓

Updater

↓

Restart Pod

↓

Apply New Resources
```

---

# VPA Modes

| Mode | Meaning |
|------|---------|
| Off | Only provides recommendations |
| Initial | Sets resources only when Pod is first created |
| Auto | Automatically updates resources (usually by recreating Pods) |

---

# HPA vs VPA

| Feature | HPA | VPA |
|----------|-----|-----|
| Changes | Number of Pods | CPU & Memory |
| Scaling Type | Horizontal | Vertical |
| Requires Pod Restart | ❌ No | ✅ Usually Yes |
| Handles Traffic Spikes | ✅ Excellent | ❌ Limited |
| Best For | Stateless apps | Stateful or memory-intensive apps |

---

# Can HPA and VPA be Used Together?

**Generally, avoid using both on the same CPU/Memory resource.**

Reason:

```
CPU Increases

↓

HPA Adds Pods

↓

VPA Increases CPU

↓

Both Try To Solve

Same Problem
```

This can cause conflicting scaling decisions.

However:

- HPA can scale using **custom metrics** (e.g., requests per second, queue length).
- VPA can manage CPU/Memory requests.

This combination is commonly used.

---

# Common Commands

View HPA

```bash
kubectl get hpa
```

Describe HPA

```bash
kubectl describe hpa
```

View Metrics

```bash
kubectl top pods
```

```bash
kubectl top nodes
```

---

# Common Use Cases

## HPA

- Web Applications
- REST APIs
- Microservices
- E-commerce Websites
- Traffic-based workloads

---

## VPA

- Databases
- JVM Applications
- Analytics Jobs
- Long-running applications
- Memory-intensive services

---

# Interview Questions

## What is HPA?

Automatically changes the **number of Pod replicas** based on metrics.

---

## What is VPA?

Automatically changes **CPU and Memory requests/limits** for Pods.

---

## Which one adds more Pods?

```
HPA
```

---

## Which one increases CPU and Memory?

```
VPA
```

---

## Does HPA restart Pods?

No.

It only changes the replica count.

---

## Does VPA restart Pods?

Usually **Yes**, because new resource requests/limits often require Pod recreation.

---

## What does HPA depend on?

```
Metrics Server
```

---

## What metrics can HPA use?

- CPU
- Memory
- Custom Metrics
- External Metrics

---

## Can HPA and VPA work together?

Yes, but **not on the same CPU/Memory scaling decision**.

A common approach is:

- HPA → Custom or external metrics
- VPA → CPU/Memory recommendations

---

# Best Practices

- Install Metrics Server before using HPA.
- Always define CPU and Memory requests for HPA.
- Use HPA for stateless applications.
- Use VPA for applications with changing resource needs.
- Avoid running HPA and VPA against the same CPU/Memory metrics.

---

# Memory Trick

```
HPA

↓

More Pods

(Horizontal)

------------------------

VPA

↓

More CPU/Memory

(Vertical)
```

---

# One-Line Revision

- **HPA** scales the **number of Pods**
- **VPA** scales the **CPU/Memory of Pods**
- HPA requires the **Metrics Server**
- HPA does **not** restart Pods
- VPA usually **restarts Pods** to apply new resources
- HPA is ideal for **traffic spikes**
- VPA is ideal for **changing resource requirements**
- Avoid using HPA and VPA to control the same CPU/Memory metrics