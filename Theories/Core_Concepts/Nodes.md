# Kubernetes Nodes - Interview Revision

## What is a Node?

A **Node** is a physical machine or virtual machine that is part of a Kubernetes Cluster and is responsible for **running Pods (applications).**

```
Cluster
    │
 ┌──┴─────────────┐
 │                │
Control Plane   Worker Nodes
                    │
                  Pods
```

---

# Types of Nodes

## 1. Control Plane Node

Responsible for managing the cluster.

Runs:

- API Server
- Scheduler
- Controller Manager
- etcd

Normally **does not run application Pods** in production.

---

## 2. Worker Node

Responsible for running applications.

Runs:

- kubelet
- kube-proxy
- Container Runtime
- Pods

---

# Worker Node Architecture

```
Worker Node
│
├── kubelet
├── kube-proxy
├── Container Runtime
└── Pods
```

---

# Components

## kubelet

- Node Agent
- Communicates with API Server
- Starts/Stops Pods
- Executes probes
- Reports Node & Pod health

---

## kube-proxy

- Handles Pod networking
- Implements Kubernetes Services
- Load balances traffic to Pods
- Manages iptables/IPVS rules

---

## Container Runtime

Responsible for running containers.

Examples:

- containerd ⭐
- CRI-O
- Docker (older versions)

---

# Node Lifecycle

```
Cluster Created
       │
       ▼
Node Joins Cluster
       │
       ▼
Scheduler Assigns Pod
       │
       ▼
kubelet Starts Pod
       │
       ▼
Application Running
```

---

# Node Status

```
Ready
```

Node is healthy and can run Pods.

---

```
NotReady
```

Node cannot run new Pods.

---

```
SchedulingDisabled
```

Node exists but Scheduler will not place new Pods.

---

# Common Node Operations

View Nodes

```bash
kubectl get nodes
```

Detailed Information

```bash
kubectl describe node <node-name>
```

Node Resource Usage

```bash
kubectl top node
```

---

# Cordon

Marks a node as **unschedulable**.

Existing Pods remain.

```bash
kubectl cordon <node>
```

```
New Pods ❌

Existing Pods ✅
```

---

# Drain

Safely removes Pods from a node.

Used before maintenance.

```bash
kubectl drain <node> --ignore-daemonsets
```

```
Pods Moved

↓

Other Nodes
```

---

# Uncordon

Makes the node schedulable again.

```bash
kubectl uncordon <node>
```

---

# Node Labels

Used to classify nodes.

Example:

```text
gpu=true

zone=us-east-1a

disk=ssd
```

Used with:

- Node Selector
- Node Affinity

---

# Node Taints

Applied on Nodes.

Used to **repel Pods**.

Example:

```bash
kubectl taint nodes worker3 gpu=true:NoSchedule
```

---

# Node Capacity

Each node has finite resources.

```
CPU

Memory

Storage

Pods
```

Scheduler places Pods only if resources are available.

---

# Scheduling Flow

```
Deployment

↓

Scheduler

↓

Select Best Node

↓

kubelet

↓

Container Runtime

↓

Pod Running
```

---

# Kind Cluster Example

```
Control Plane

↓

Worker1

Worker2

Worker3
```

Pods are distributed across worker nodes.

---

# Interview Questions

## What is a Node?

A machine (physical or virtual) that runs Pods in a Kubernetes Cluster.

---

## Difference between Control Plane Node and Worker Node?

| Control Plane | Worker Node |
|---------------|-------------|
| Manages cluster | Runs applications |
| Runs API Server, Scheduler, etcd | Runs kubelet, kube-proxy, Pods |

---

## What does kubelet do?

- Registers node
- Starts Pods
- Executes probes
- Reports health

---

## What does kube-proxy do?

Handles Service networking and load balancing between Pods.

---

## What is Container Runtime?

Software responsible for running containers.

Example:

- containerd
- CRI-O

---

## Can one Node run multiple Pods?

Yes.

A node can run many Pods depending on available resources.

---

## Can multiple Pods from the same Deployment run on one Node?

Yes.

Unless restricted using affinity/anti-affinity rules.

---

# Memory Trick

```
Node
│
├── kubelet
│     └── Manages Pods
│
├── kube-proxy
│     └── Networking
│
├── Container Runtime
│     └── Runs Containers
│
└── Pods
```

---

# One-Line Revision

- Node = Machine that runs Pods
- Control Plane = Manages Cluster
- Worker Node = Runs Applications
- kubelet = Pod Manager
- kube-proxy = Networking & Services
- Container Runtime = Runs Containers
- Cordon = Stop scheduling new Pods
- Drain = Move existing Pods away
- Uncordon = Resume scheduling
- Labels = Classify Nodes
- Taints = Restrict Nodes
- Scheduler = Chooses Node