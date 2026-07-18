# Kubernetes Cluster - Interview Revision

## What is a Kubernetes Cluster?

A **Kubernetes Cluster** is a collection of machines (Nodes) that work together to run, manage, scale, and recover containerized applications.

```
Kubernetes Cluster
        │
 ┌──────┴──────┐
 │             │
Control Plane  Worker Nodes
```

---

# Cluster Components

## 1. Control Plane (Master)

Responsible for **managing the cluster**.

Components:

- API Server
- Scheduler
- Controller Manager
- etcd
- Cloud Controller Manager (Cloud Only)

Never runs application workloads (except small/local clusters like Kind or Minikube).

---

## 2. Worker Node

Responsible for **running Pods**.

Components:

- kubelet
- kube-proxy
- Container Runtime (containerd, CRI-O, etc.)

Runs:

- Pods
- Deployments
- StatefulSets
- DaemonSets
- Jobs

---

# Cluster Architecture

```
                 Kubernetes Cluster

        ┌───────────────────────────────┐
        │                               │
        │        Control Plane          │
        │                               │
        └──────────────┬────────────────┘
                       │
     ┌─────────────────┼──────────────────┐
     │                 │                  │
     ▼                 ▼                  ▼
 Worker1          Worker2           Worker3
     │                 │                  │
  Pods             Pods              Pods
```

---

# Responsibilities

## Control Plane

- Maintains desired state
- Scheduling Pods
- Cluster management
- Self-healing
- Scaling
- API handling

---

## Worker Nodes

- Run containers
- Execute Pods
- Report health to Control Plane

---

# Cluster Communication Flow

```
kubectl

↓

API Server

↓

Scheduler

↓

Selected Worker Node

↓

kubelet

↓

Container Runtime

↓

Pod Running
```

---

# Important Terminologies

| Term | Meaning |
|------|---------|
| Cluster | Collection of Nodes managed by Kubernetes |
| Control Plane | Manages the entire cluster |
| Worker Node | Runs application Pods |
| Node | Physical or Virtual Machine in cluster |
| Pod | Smallest deployable Kubernetes unit |

---

# Types of Clusters

## Local Development

- Kind
- Minikube
- k3d
- Docker Desktop Kubernetes

---

## Production

- Amazon EKS
- Azure AKS
- Google GKE
- OpenShift
- Rancher
- Self-managed Kubernetes

---

# Cluster Lifecycle

```
Create Cluster

↓

Add Nodes

↓

Deploy Applications

↓

Scheduler Places Pods

↓

Services Expose Pods

↓

Ingress Exposes Services

↓

Users Access Application
```

---

# Common Commands

Create Kind Cluster

```bash
kind create cluster --name my-cluster
```

List Clusters

```bash
kind get clusters
```

Delete Cluster

```bash
kind delete cluster --name my-cluster
```

Cluster Info

```bash
kubectl cluster-info
```

View Nodes

```bash
kubectl get nodes
```

Detailed Node Info

```bash
kubectl describe node <node-name>
```

---

# Interview Questions

## What is a Kubernetes Cluster?

A collection of Control Plane and Worker Nodes that work together to run and manage containerized applications.

---

## Difference between Cluster and Node?

Cluster = Collection of Nodes

Node = Single machine inside the cluster

---

## Can a Cluster have multiple Worker Nodes?

Yes.

---

## Can a Cluster have multiple Control Plane Nodes?

Yes.

Production clusters usually have **3 or 5 Control Plane Nodes** for High Availability (HA).

---

## Does Kind create a real Kubernetes Cluster?

Yes.

Kind creates Kubernetes Nodes as Docker containers instead of Virtual Machines.

---

# Memory Trick

```
Cluster
│
├── Control Plane
│      ├── API Server
│      ├── Scheduler
│      ├── Controller Manager
│      └── etcd
│
└── Worker Nodes
       ├── kubelet
       ├── kube-proxy
       ├── Container Runtime
       └── Pods
```

---

# One-Line Revision

- Cluster = Collection of Nodes
- Control Plane = Manages the Cluster
- Worker Node = Runs Pods
- Scheduler = Decides Pod Placement
- kubelet = Runs Pods on Node
- etcd = Stores Cluster State
- API Server = Entry Point to Kubernetes