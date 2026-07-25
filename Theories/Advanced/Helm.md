# Kubernetes Helm - Interview Revision

# What is Helm?

**Helm** is the **package manager for Kubernetes**.

Just like:

- apt → Ubuntu
- npm → Node.js
- pip → Python

Helm manages Kubernetes applications.

---

# Why Helm?

Without Helm, a complex application may require many YAML files.

Example:

```
Deployment.yaml

Service.yaml

Ingress.yaml

ConfigMap.yaml

Secret.yaml

HPA.yaml

PVC.yaml
```

Managing them individually becomes difficult.

With Helm:

```
helm install my-app
```

Everything is deployed automatically.

---

# What is a Helm Chart?

A **Helm Chart** is a package containing all Kubernetes manifests required to deploy an application.

Think of it as:

```
Chart

↓

Templates

Values

Metadata

↓

Deploy Application
```

---

# Helm Architecture

```
Developer

↓

Helm Chart

↓

helm install

↓

Helm

↓

Kubernetes API

↓

Deployment

Service

ConfigMap

Ingress

PVC

...
```

---

# Components of a Helm Chart

```
my-chart/

│

├── Chart.yaml

├── values.yaml

├── templates/

├── charts/

└── .helmignore
```

---

## Chart.yaml

Contains chart metadata.

Example:

- Name
- Version
- Description
- App Version

---

## values.yaml

Stores configurable values.

Example:

```yaml
replicaCount: 2

image:
  repository: nginx
```

Instead of editing YAML files directly, change values here.

---

## templates/

Contains Kubernetes manifest templates.

Example:

```
deployment.yaml

service.yaml

ingress.yaml
```

These use values from `values.yaml`.

---

## charts/

Stores dependency charts.

Example:

```
My Application

↓

Redis Chart

↓

MySQL Chart
```

---

# Helm Workflow

```
Create Chart

↓

Edit values.yaml

↓

helm install

↓

Release Created

↓

Upgrade

↓

Rollback
```

---

# What is a Release?

A **Release** is a deployed instance of a Helm Chart.

Example:

Chart

```
apache-helm
```

Release

```
dev-apache
```

You can install the same chart multiple times:

```
apache-helm

↓

dev-apache

↓

prod-apache

↓

test-apache
```

Each is an independent release.

---

# Install Flow

```
Helm Chart

↓

helm install

↓

Release

↓

Resources Created
```

---

# Upgrade Flow

```
Modify values.yaml

↓

helm upgrade

↓

Update Existing Release
```

---

# Rollback Flow

```
Version 1

↓

Upgrade

↓

Version 2

↓

Problem Found

↓

helm rollback

↓

Back to Version 1
```

---

# Helm Repository

A Helm Repository stores Helm Charts.

Examples:

- Bitnami
- Prometheus Community
- Grafana

Flow

```
Repository

↓

Chart

↓

helm install
```

---

# Helm vs kubectl

| Helm | kubectl |
|------|----------|
| Package manager | Kubernetes CLI |
| Deploys entire applications | Manages individual resources |
| Supports versioning | No release history |
| Supports rollback | Manual rollback |
| Uses Charts | Uses YAML files |

---

# Helm Advantages

- Reusable Charts
- Easy upgrades
- Rollbacks
- Parameterized deployments
- Dependency management
- Version control
- Simplified application deployment

---

# Common Use Cases

- Deploy Prometheus
- Deploy Grafana
- Deploy NGINX
- Deploy ArgoCD
- Deploy Redis
- Deploy MySQL
- Deploy Istio

---

# Interview Questions

## What is Helm?

A package manager for Kubernetes that deploys and manages applications using Charts.

---

## What is a Helm Chart?

A package containing Kubernetes manifests, templates, and configuration values.

---

## What is a Release?

A deployed instance of a Helm Chart.

---

## Difference between Chart and Release?

- **Chart** → Blueprint/package.
- **Release** → Running deployment created from the Chart.

---

## What is values.yaml?

Stores configurable values used by chart templates.

---

## Can one Chart have multiple Releases?

Yes.

Example:

```
apache-helm

↓

dev-apache

↓

prod-apache

↓

test-apache
```

---

## What is the purpose of helm upgrade?

Updates an existing release without reinstalling it.

---

## What is the purpose of helm rollback?

Restores a previous release revision.

---

## Why use Helm instead of plain YAML?

- Easier management
- Reusability
- Rollbacks
- Versioning
- Parameterization

---

# Best Practices

- Store configurable values in `values.yaml`.
- Version Helm Charts properly.
- Use separate releases for different environments.
- Test charts before production.
- Prefer upgrades over deleting and reinstalling.

---

# Memory Trick

```
Chart

↓

Install

↓

Release

↓

Upgrade

↓

Rollback
```

---

# One-Line Revision

- **Helm is the package manager for Kubernetes**
- **Chart = Application package**
- **Release = Installed instance of a Chart**
- **values.yaml stores configurable values**
- **Templates generate Kubernetes manifests**
- **Helm supports upgrades, rollbacks, and versioning**