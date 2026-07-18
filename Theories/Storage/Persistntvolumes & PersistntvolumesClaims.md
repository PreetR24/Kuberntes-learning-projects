# Kubernetes Persistent Volumes (PV) & Persistent Volume Claims (PVC) - Interview Revision

# Why do we need Persistent Storage?

By default, Pods use **ephemeral storage**.

```
Pod Deleted

↓

Data Lost ❌
```

For databases and applications requiring data persistence, we use **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)**.

---

# Storage Architecture

```
Application

↓

Pod

↓

PVC

↓

PV

↓

Physical Storage
(AWS EBS, Azure Disk, NFS, Local Disk, etc.)
```

---

# What is a Persistent Volume (PV)?

A **PersistentVolume (PV)** is the **actual storage resource** available in the Kubernetes cluster.

It can be backed by:

- Local Disk
- NFS
- AWS EBS
- Azure Disk
- GCP Persistent Disk
- Ceph
- etc.

Created by:

- Administrator (Static Provisioning)
- StorageClass (Dynamic Provisioning)

---

# What is a Persistent Volume Claim (PVC)?

A **PersistentVolumeClaim (PVC)** is a **request for storage** made by a Pod.

Think of it as:

```
PV

=

Actual Hard Disk

---------------------

PVC

=

Request for Hard Disk
```

Pods **never use a PV directly**.

They always use a PVC.

---

# Complete Flow

```
Application

↓

Deployment

↓

Pod

↓

PVC

↓

PV

↓

Physical Storage
```

---

# Binding Process

```
PVC Created

↓

Find Matching PV

↓

Bind

↓

Pod Uses PVC

↓

Reads/Writes Data
```

---

# Important YAML Fields (PV)

```yaml
capacity:
accessModes:
storageClassName:
persistentVolumeReclaimPolicy:
hostPath:
```

---

## capacity

Storage available.

Example

```yaml
storage: 10Gi
```

---

## accessModes

Defines how the volume can be mounted.

### ReadWriteOnce (RWO)

- One node can mount as Read-Write.
- Multiple Pods on the **same node** may share it.

---

### ReadOnlyMany (ROX)

- Multiple nodes can mount.
- Read Only.

---

### ReadWriteMany (RWX)

- Multiple nodes can mount.
- Read & Write.

Common with:

- NFS
- CephFS
- Azure Files

---

### ReadWriteOncePod (RWOP)

- Only **one Pod** in the entire cluster can mount it as Read-Write.

---

# storageClassName

Groups storage types.

Example

```yaml
storageClassName: local-storage
```

PVC will bind only to PVs with the same StorageClass.

---

# persistentVolumeReclaimPolicy

Defines what happens after PVC is deleted.

### Retain

Keep data.

PV remains.

---

### Delete

Delete underlying storage automatically.

---

### Recycle (Deprecated)

Old feature.

---

# hostPath

Uses a directory on the Node.

Mostly for:

- Kind
- Minikube
- Local Development

Not recommended for production.

---

# Important YAML Fields (PVC)

```yaml
accessModes:
resources:
storageClassName:
```

---

## resources.requests.storage

Amount of storage requested.

Example

```yaml
requests:
  storage: 5Gi
```

---

# PV-PVC Matching Rules

PVC binds only if:

- StorageClass matches
- Requested size ≤ PV size
- AccessMode matches

Example

```
PV

10Gi

↓

PVC

5Gi

↓

Bind ✅
```

---

# Access Modes Summary

| Mode | Meaning |
|------|---------|
| ReadWriteOnce (RWO) | Read/Write from one node |
| ReadOnlyMany (ROX) | Read-only from many nodes |
| ReadWriteMany (RWX) | Read/Write from many nodes |
| ReadWriteOncePod (RWOP) | Read/Write by only one Pod |

---

# Reclaim Policies

| Policy | Result |
|---------|--------|
| Retain | Keep Storage |
| Delete | Delete Storage |
| Recycle | Deprecated |

---

# Static vs Dynamic Provisioning

## Static Provisioning

```
Admin Creates PV

↓

PVC

↓

Bind
```

---

## Dynamic Provisioning

```
PVC

↓

StorageClass

↓

Automatically Creates PV

↓

Bind
```

Preferred in production.

---

# Can one PV bind to multiple PVCs?

❌ No.

One PV can be bound to **only one PVC** at a time.

Example

```
PV

10Gi

↓

PVC

1Gi

↓

Remaining 9Gi

❌ Cannot be claimed separately
```

The entire PV is reserved for that PVC.

---

# Common Commands

List PVs

```bash
kubectl get pv
```

List PVCs

```bash
kubectl get pvc
```

Describe PV

```bash
kubectl describe pv <pv-name>
```

Describe PVC

```bash
kubectl describe pvc <pvc-name>
```

Delete PVC

```bash
kubectl delete pvc <pvc-name>
```

---

# Common Use Cases

- MySQL
- PostgreSQL
- MongoDB
- Redis Persistence
- Elasticsearch
- File Uploads
- Shared Storage

---

# Interview Questions

## What is a Persistent Volume?

A cluster storage resource that provides persistent storage.

---

## What is a PVC?

A request for storage made by a Pod.

---

## Does a Pod use a PV directly?

No.

```
Pod

↓

PVC

↓

PV
```

---

## Can one PV bind to multiple PVCs?

No.

One PV → One PVC.

---

## What happens if PVC requests 2Gi and PV has 10Gi?

PVC binds successfully.

The remaining 8Gi cannot be claimed by another PVC.

---

## What happens if the Pod is deleted?

Data remains because it is stored on the PV.

---

## What happens if the PVC is deleted?

Depends on `persistentVolumeReclaimPolicy`.

- Retain → Data kept
- Delete → Storage deleted

---

## Difference between PV and PVC?

| PV | PVC |
|----|-----|
| Actual Storage | Request for Storage |
| Cluster Resource | Namespace Resource |
| Created by Admin/StorageClass | Created by User/Application |

---

## What is StorageClass?

A template that defines how storage should be dynamically provisioned.

---

## Why use volumeClaimTemplates in StatefulSet?

To automatically create one PVC for each Pod.

---

# Best Practices

- Never mount a PV directly to a Pod.
- Use PVCs instead of hardcoding PV names.
- Prefer Dynamic Provisioning with StorageClasses.
- Use `Retain` for critical databases.
- Use `Delete` for temporary workloads.
- Choose the correct AccessMode based on the storage backend.

---

# Memory Trick

```
Physical Disk

↓

Persistent Volume (PV)

↓

Persistent Volume Claim (PVC)

↓

Pod

↓

Application
```

Remember:

- **PV = Actual Storage**
- **PVC = Storage Request**
- **StorageClass = Storage Template**
- **Pod always uses PVC, never PV directly**

---

# One-Line Revision

- PV = Actual persistent storage in the cluster
- PVC = Request for storage made by a Pod
- Pod mounts a PVC, not a PV
- PV and PVC bind based on StorageClass, size, and AccessMode
- One PV can bind to only one PVC
- Access Modes: RWO, ROX, RWX, RWOP
- Reclaim Policies: Retain, Delete
- Dynamic provisioning uses StorageClass to create PVs automatically
- StatefulSets use `volumeClaimTemplates` to create one PVC per Pod