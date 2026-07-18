# Kubernetes StorageClass - Interview Revision

# What is a StorageClass?

A **StorageClass** defines **how Kubernetes should dynamically create Persistent Volumes (PVs)**.

It acts as a **template or blueprint** for storage provisioning.

Without StorageClass:

```
Admin Creates PV

↓

PVC

↓

Bind
```

With StorageClass:

```
PVC

↓

StorageClass

↓

PV Created Automatically

↓

Bind
```

---

# Why do we need StorageClass?

Without StorageClass:

- Admin must manually create every PV.
- Difficult to manage in large clusters.

With StorageClass:

- PVs are created automatically.
- Better scalability.
- Cloud-native storage provisioning.

---

# Storage Architecture

```
Application

↓

Pod

↓

PVC

↓

StorageClass

↓

PV (Auto Created)

↓

Physical Storage
(AWS EBS, Azure Disk, GCP PD, NFS, etc.)
```

---

# Responsibilities

- Dynamic PV provisioning
- Storage type selection
- Provisioner selection
- Storage configuration
- Reclaim policy
- Volume expansion (optional)

---

# Provisioning Types

## Static Provisioning

```
Admin

↓

Creates PV

↓

PVC

↓

Bind
```

---

## Dynamic Provisioning ⭐

```
PVC

↓

StorageClass

↓

Automatically Create PV

↓

Bind
```

Preferred in production.

---

# Important YAML Fields

```yaml
provisioner:
parameters:
reclaimPolicy:
volumeBindingMode:
allowVolumeExpansion:
```

---

## provisioner ⭐

Specifies **who creates the storage**.

Examples

AWS

```yaml
provisioner: ebs.csi.aws.com
```

Azure

```yaml
provisioner: disk.csi.azure.com
```

GCP

```yaml
provisioner: pd.csi.storage.gke.io
```

Local

```yaml
provisioner: rancher.io/local-path
```

---

## parameters

Provider-specific configuration.

Examples

AWS

```
SSD

gp3

io1
```

Azure

```
Premium SSD

Standard SSD
```

---

## reclaimPolicy

Defines what happens after PVC deletion.

```
Retain
```

Keep storage.

```
Delete
```

Delete storage.

Default is usually:

```
Delete
```

---

## volumeBindingMode

### Immediate

PV created immediately after PVC creation.

---

### WaitForFirstConsumer ⭐

PV created only when a Pod uses the PVC.

Preferred in cloud environments.

---

## allowVolumeExpansion

```
true
```

Allows increasing PVC size later.

---

# PVC + StorageClass Flow

```
PVC

↓

storageClassName

↓

StorageClass

↓

Provisioner

↓

Create PV

↓

Bind

↓

Pod Uses PVC
```

---

# Matching Process

PVC

```yaml
storageClassName: fast-storage
```

↓

StorageClass

```yaml
metadata:
  name: fast-storage
```

↓

Provisioner

↓

PV Created

↓

Bound

---

# StorageClass vs PV

| StorageClass | Persistent Volume |
|---------------|-------------------|
| Blueprint | Actual Storage |
| Creates PVs | Stores Data |
| Dynamic Provisioning | Storage Resource |

---

# StorageClass vs PVC

| StorageClass | PVC |
|---------------|-----|
| Defines Storage Type | Requests Storage |
| Cluster Resource | Namespace Resource |

---

# Common Commands

List StorageClasses

```bash
kubectl get storageclass
```

or

```bash
kubectl get sc
```

Describe

```bash
kubectl describe sc <name>
```

Create

```bash
kubectl apply -f storageclass.yaml
```

Delete

```bash
kubectl delete sc <name>
```

---

# Default StorageClass

View

```bash
kubectl get sc
```

Example

```
NAME                 PROVISIONER                    DEFAULT

standard (default)   ebs.csi.aws.com               Yes
```

If a PVC doesn't specify a StorageClass,

the default StorageClass is used.

---

# Common Provisioners

| Platform | Provisioner |
|-----------|-------------|
| AWS | ebs.csi.aws.com |
| Azure | disk.csi.azure.com |
| GCP | pd.csi.storage.gke.io |
| NFS | nfs.csi.k8s.io |
| Local Path | rancher.io/local-path |

---

# Common Use Cases

- MySQL
- PostgreSQL
- MongoDB
- Elasticsearch
- Kafka
- File Storage
- Cloud Persistent Disks

---

# Interview Questions

## What is a StorageClass?

A Kubernetes resource that defines how Persistent Volumes are dynamically provisioned.

---

## Why do we need StorageClass?

To automate PV creation and eliminate manual provisioning.

---

## Does StorageClass store data?

No.

It only defines **how storage should be created**.

---

## What creates the actual PV?

The **Provisioner** specified in the StorageClass.

---

## Difference between PV and StorageClass?

```
StorageClass

↓

Creates

↓

Persistent Volume
```

StorageClass = Blueprint

PV = Actual Storage

---

## Difference between Static and Dynamic Provisioning?

### Static

```
Admin Creates PV
```

### Dynamic

```
StorageClass Creates PV Automatically
```

---

## What is the Provisioner?

Component responsible for creating storage.

Examples:

- AWS EBS CSI
- Azure Disk CSI
- GCP PD CSI

---

## What is WaitForFirstConsumer?

Delays PV creation until a Pod actually uses the PVC.

Helps provision storage in the correct availability zone.

---

## Can one StorageClass create multiple PVs?

Yes.

Each PVC requesting that StorageClass can trigger creation of a new PV.

---

# Best Practices

- Prefer Dynamic Provisioning.
- Use CSI drivers instead of legacy provisioners.
- Use `WaitForFirstConsumer` for cloud workloads.
- Enable `allowVolumeExpansion` where supported.
- Use different StorageClasses for SSD, HDD, and premium storage.

---

# Memory Trick

```
Pod

↓

PVC

↓

StorageClass

↓

Provisioner

↓

PV

↓

Physical Disk
```

Remember:

- **StorageClass = Blueprint**
- **Provisioner = Builder**
- **PV = House**
- **PVC = House Request**

---

# One-Line Revision

- StorageClass defines how PVs are dynamically created
- Uses a provisioner (CSI driver) to create storage
- PVC references a StorageClass using `storageClassName`
- StorageClass creates PV automatically when needed
- Supports reclaim policies, volume expansion, and binding modes
- Dynamic provisioning is preferred over static provisioning
- StorageClass is a cluster-scoped resource and does not store data itself