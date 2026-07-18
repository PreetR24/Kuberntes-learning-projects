# Kubernetes Namespace - Interview Revision

## What is a Namespace?

A **Namespace** is a logical partition inside a Kubernetes Cluster used to organize, isolate, and manage resources.

Think of it as a **virtual cluster** inside the same Kubernetes cluster.

```
Kubernetes Cluster
│
├── Namespace A
│     ├── Pods
│     ├── Services
│     └── Deployments
│
├── Namespace B
│     ├── Pods
│     ├── Services
│     └── Deployments
│
└── Namespace C
      ├── Pods
      ├── Services
      └── Deployments
```

---

# Why do we need Namespaces?

- Resource isolation
- Organize applications
- Avoid naming conflicts
- Apply RBAC (permissions)
- Apply Resource Quotas
- Separate environments (Dev, QA, Prod)

---

# Default Namespaces

## default

Default namespace for user applications.

---

## kube-system

Contains Kubernetes system components.

Examples:

- CoreDNS
- kube-proxy
- Metrics Server

---

## kube-public

Public namespace readable by everyone.

Rarely used.

---

## kube-node-lease

Stores node heartbeat (Lease objects).

Helps Kubernetes quickly detect node failures.

---

## local-path-storage (Kind/Minikube)

Created by local storage provisioner.

Used for dynamic local Persistent Volumes.

---

# Namespace Architecture

```
Cluster
│
├── default
│     ├── Pod
│     └── Service
│
├── production
│     ├── Pod
│     └── Deployment
│
└── monitoring
      ├── Prometheus
      └── Grafana
```

---

# Resources inside Namespace

Most Kubernetes resources are namespaced.

Examples:

- Pods
- Deployments
- Services
- ConfigMaps
- Secrets
- PVCs
- StatefulSets
- Jobs
- CronJobs

---

# Cluster-Level Resources (Not Namespaced)

Examples:

- Nodes
- PersistentVolumes (PV)
- Namespaces
- StorageClasses
- ClusterRoles
- ClusterRoleBindings

---

# Name Conflict

Allowed:

```
Namespace A

Pod → nginx
```

```
Namespace B

Pod → nginx
```

Both can exist because they belong to different namespaces.

---

# Resource Isolation

```
Namespace A

Pod A
Service A
Deployment A

↓

Cannot affect

↓

Namespace B

Pod B
Service B
Deployment B
```

---

# Communication

Pods in different namespaces **can communicate**.

Use FQDN:

```
<service-name>.<namespace>.svc.cluster.local
```

Example:

```
product-service.backend.svc.cluster.local
```

Within the same namespace:

```
product-service
```

is sufficient.

---

# Common Commands

List Namespaces

```bash
kubectl get ns
```

Create Namespace

```bash
kubectl create namespace dev
```

Delete Namespace

```bash
kubectl delete namespace dev
```

View Resources in Namespace

```bash
kubectl get pods -n dev
```

Create Resource in Namespace

```bash
kubectl apply -f deployment.yaml -n dev
```

Set Current Namespace

```bash
kubectl config set-context --current --namespace=dev
```

---

# What happens if a Namespace is deleted?

Deleting a Namespace deletes **all namespaced resources** inside it.

```
Namespace Deleted

↓

Pods Deleted

Services Deleted

Deployments Deleted

Secrets Deleted

PVCs Deleted
```

Cluster-level resources (e.g., Nodes, PVs) are **not deleted**.

---

# Best Practices

- Create separate namespaces for each application/team/environment.
- Avoid deploying everything in the `default` namespace.
- Use ResourceQuotas and RBAC per namespace.
- Use labels for organization within a namespace.

---

# Interview Questions

## What is a Namespace?

A logical partition inside a Kubernetes cluster used to organize and isolate resources.

---

## Why use Namespaces?

- Isolation
- Organization
- RBAC
- Resource Quotas
- Avoid naming conflicts

---

## Can two Pods have the same name?

Yes, if they are in **different namespaces**.

---

## Are Nodes namespaced?

No.

Nodes are cluster-scoped resources.

---

## Is PersistentVolume namespaced?

No.

PersistentVolume (PV) is cluster-scoped.

PersistentVolumeClaim (PVC) is namespaced.

---

## Can Pods in different namespaces communicate?

Yes.

Using:

```
service-name.namespace.svc.cluster.local
```

---

## What happens when a Namespace is deleted?

All **namespaced resources** inside it are deleted.

---

# Memory Trick

```
Cluster
│
├── Namespace
│      ├── Pods
│      ├── Services
│      ├── Deployments
│      ├── ConfigMaps
│      └── Secrets
│
└── Cluster Resources
       ├── Nodes
       ├── PV
       ├── StorageClass
       └── ClusterRoles
```

---

# One-Line Revision

- Namespace = Logical isolation inside a cluster
- Used for organization, RBAC, quotas, and avoiding naming conflicts
- Most resources are namespaced
- Nodes, PVs, StorageClasses are cluster-scoped
- Pods across namespaces can communicate using FQDN
- Deleting a namespace deletes all namespaced resources inside it