# Kubernetes Custom Resource Definition (CRD) - Interview Revision

# What is a CRD?

A **Custom Resource Definition (CRD)** allows you to create **your own Kubernetes resource types**.

Think of it as:

> "Adding a new object type to Kubernetes."

---

# Why do we need CRDs?

Kubernetes already provides built-in resources like:

- Pod
- Deployment
- Service
- ConfigMap

But suppose your company wants to manage:

- Database
- Kafka Cluster
- Redis Cluster
- Backup
- Certificate

Kubernetes doesn't know these resources.

CRDs allow us to create them.

---

# Without CRD

```
Kubernetes

↓

Pod

Deployment

Service

Secret

ConfigMap
```

No resource called:

```
Database
```

exists.

---

# With CRD

```
Kubernetes

↓

Pod

Deployment

Service

Database   ✅

Kafka      ✅

Redis      ✅
```

Now Kubernetes understands these new resource types.

---

# Real World Example

Suppose your company deploys MySQL frequently.

Instead of creating:

- Deployment
- Service
- PVC
- Secret

every time,

you simply create:

```yaml
kind: Database
```

An Operator creates everything automatically.

---

# CRD Architecture

```
Developer

↓

Database.yaml

↓

API Server

↓

CRD Exists?

↓

Yes

↓

Custom Resource Created

↓

Operator Watches It

↓

Creates Pods

PVC

Service

Secrets
```

---

# CRD Components

```
CRD

↓

Defines New Resource

↓

Custom Resource

↓

Actual Object Created

↓

Operator

↓

Business Logic
```

---

# CRD vs Custom Resource

## CRD

Creates a **new resource type**.

Example

```
Database
```

---

## Custom Resource

An instance of that CRD.

Example

```yaml
kind: Database

metadata:
  name: mysql-prod
```

Think of it like:

```
Deployment

↓

nginx-deployment

------------------------

Database (CRD)

↓

mysql-prod
```

---

# What is an Operator?

An Operator is a controller that watches Custom Resources.

Flow

```
Database Created

↓

Operator Detects It

↓

Creates Deployment

↓

Creates Service

↓

Creates PVC

↓

Keeps Everything Healthy
```

Without an Operator,

the CRD is just data.

---

# CRD Flow

```
Install CRD

↓

New Resource Available

↓

Create Custom Resource

↓

Operator Watches

↓

Creates Kubernetes Resources
```

---

# Example

CRD defines

```
kind: Database
```

Now you can create

```yaml
apiVersion: company.com/v1

kind: Database

metadata:
  name: mysql-prod
```

instead of manually creating many YAML files.

---

# CRD vs Built-in Resources

| Built-in Resource | CRD |
|-------------------|-----|
| Provided by Kubernetes | Created by Developers |
| Pod | Database |
| Deployment | Kafka |
| Service | Redis |
| Secret | Certificate |

---

# CRD vs Operator

| CRD | Operator |
|------|----------|
| Defines a new resource type | Implements the logic |
| Extends Kubernetes API | Watches Custom Resources |
| Stores data | Takes actions |

---

# Common Use Cases

- Prometheus
- ArgoCD
- Istio
- Cert Manager
- Crossplane
- Kafka Operator
- MongoDB Operator
- MySQL Operator

Almost every Kubernetes Operator uses CRDs.

---

# Interview Questions

## What is a CRD?

A CRD extends the Kubernetes API by allowing developers to define new resource types.

---

## What is a Custom Resource?

An instance of a CRD.

---

## Does a CRD create Pods automatically?

No.

It only defines a new resource.

An Operator performs the actual work.

---

## What is the relationship between CRD and Operator?

```
CRD

↓

Defines Resource

------------------------

Operator

↓

Acts on Resource
```

---

## Can a CRD work without an Operator?

Yes.

But it only stores data.

No automation happens.

---

## Why are CRDs useful?

They allow Kubernetes to manage application-specific resources like Databases, Kafka, Certificates, etc.

---

# Best Practices

- Use Operators with CRDs.
- Keep CRDs versioned.
- Use meaningful API groups.
- Validate CRDs using schemas.
- Follow Kubernetes naming conventions.

---

# Memory Trick

```
CRD

↓

New Resource Type

↓

Custom Resource

↓

Operator

↓

Automation
```

---

# One-Line Revision

- **CRD extends the Kubernetes API**
- **Custom Resource is an instance of a CRD**
- **Operator watches Custom Resources**
- **CRD defines the resource, Operator performs the work**
- **Most Kubernetes Operators are built using CRDs**