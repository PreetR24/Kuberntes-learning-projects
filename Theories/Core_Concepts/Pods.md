# Kubernetes Pods - Interview Revision

## What is a Pod?

A **Pod** is the **smallest deployable unit** in Kubernetes.

A Pod is a wrapper around one or more containers that share:

- Network
- Storage (Volumes)
- Lifecycle

```
Pod
│
├── Container 1
├── Container 2 (Optional)
└── Shared Network & Storage
```

---

# Why Pods?

Kubernetes does **not** deploy containers directly.

Instead:

```
Deployment

↓

ReplicaSet

↓

Pod

↓

Container(s)
```

Pods provide:

- Networking
- Shared Storage
- Lifecycle Management

---

# Pod Architecture

```
Pod
│
├── IP Address
├── Volumes
├── Containers
└── Shared Network Namespace
```

---

# Pod Characteristics

- Smallest deployable unit
- Usually contains **one container**
- Can contain multiple tightly coupled containers
- Gets **one unique IP**
- Containers inside a Pod communicate using **localhost**
- Pods are **ephemeral** (temporary)

---

# Pod Networking

Each Pod gets its own IP.

```
Pod1

IP → 10.244.1.2

Pod2

IP → 10.244.2.4
```

Containers inside the same Pod share:

```
localhost

127.0.0.1
```

---

# Pod Lifecycle

```
Pending

↓

ContainerCreating

↓

Running

↓

Succeeded

or

Failed

↓

Terminating
```

---

# Pod States

| State | Meaning |
|--------|---------|
| Pending | Pod accepted but not running yet |
| ContainerCreating | Images are being pulled & container is starting |
| Running | Pod is healthy |
| Succeeded | Completed successfully (Jobs) |
| Failed | Pod exited with failure |
| Terminating | Pod is being deleted |

---

# Pod Creation Flow

```
kubectl apply

↓

API Server

↓

Scheduler

↓

Selected Node

↓

kubelet

↓

Container Runtime

↓

Pod Running
```

---

# Multi-Container Pod

```
Pod
│
├── App Container
├── Sidecar Container
└── Shared Volume
```

Use Cases:

- Log Collector
- Proxy
- Monitoring Agent

---

# Pod Restart

Pods themselves are **not restarted**.

The **container inside the Pod** is restarted.

If the Pod is deleted:

```
Deployment

↓

ReplicaSet

↓

Creates NEW Pod
```

---

# Pod Deletion

```
Delete Pod

↓

Pod Removed

↓

ReplicaSet Detects

↓

Creates New Pod
```

---

# Pod Communication

## Same Pod

```
Container A

↓

localhost

↓

Container B
```

---

## Different Pods

```
Pod A

↓

Pod IP

or

Service

↓

Pod B
```

Best Practice:

Use **Service**, not Pod IP.

---

# Pod Storage

Default storage is **ephemeral**.

```
Pod Deleted

↓

Data Lost
```

Persistent storage requires:

- PersistentVolume (PV)
- PersistentVolumeClaim (PVC)

---

# Important Fields

```yaml
containers:
image:
ports:
env:
volumeMounts:
volumes:
resources:
```

---

# Common Commands

Create Pod

```bash
kubectl apply -f pod.yaml
```

List Pods

```bash
kubectl get pods
```

Detailed Info

```bash
kubectl describe pod <pod-name>
```

View Logs

```bash
kubectl logs <pod-name>
```

Execute Command

```bash
kubectl exec -it <pod-name> -- sh
```

Delete Pod

```bash
kubectl delete pod <pod-name>
```

---

# Interview Questions

## What is a Pod?

Smallest deployable unit in Kubernetes containing one or more containers.

---

## Can a Pod have multiple containers?

Yes.

Containers share:

- Network
- Volumes
- Lifecycle

---

## Does each container get its own IP?

No.

Entire Pod gets **one IP**.

All containers share it.

---

## How do containers inside the same Pod communicate?

Using

```
localhost
```

---

## Can Pods communicate directly?

Yes.

Using Pod IP.

Best practice:

Use **Service**.

---

## Are Pods permanent?

No.

Pods are ephemeral.

---

## What happens when a Pod dies?

If managed by a Deployment/ReplicaSet:

```
Old Pod Deleted

↓

New Pod Created
```

with a **new IP**.

---

## Why shouldn't applications use Pod IPs?

Pod IPs change whenever Pods are recreated.

Use a **Service** for a stable endpoint.

---

# Memory Trick

```
Deployment

↓

ReplicaSet

↓

Pod

↓

Container
```

Remember:

- Pod wraps Container(s)
- Pod gets IP
- Containers share localhost
- Pod is temporary
- Service provides stable access

---

# One-Line Revision

- Pod = Smallest deployable Kubernetes unit
- Pod contains one or more containers
- One Pod = One IP
- Containers communicate via localhost
- Pods are ephemeral
- Deployments create Pods
- Services expose Pods
- Pod deletion → New Pod with new IP
- Use Service instead of Pod IP
- Default Pod storage is ephemeral