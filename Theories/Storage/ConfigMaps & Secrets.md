# Kubernetes ConfigMaps & Secrets - Interview Revision

# Why do we need ConfigMaps & Secrets?

Avoid hardcoding configuration inside container images.

Instead of:

```yaml
image: my-app
```

Hardcoded:

```text
DB_HOST=localhost
DB_USER=root
DB_PASSWORD=root123
```

Use:

- ConfigMap → Non-sensitive configuration
- Secret → Sensitive configuration

---

# Configuration Architecture

```
Application

↓

Pod

↓

ConfigMap / Secret

↓

Environment Variables

or

Mounted Files
```

---

# 1. ConfigMap

## What is a ConfigMap?

A **ConfigMap** stores **non-sensitive configuration** as key-value pairs.

Examples:

- Database Host
- API URL
- Port
- Environment Name
- Feature Flags

---

# ConfigMap Examples

```
DB_HOST=mysql-service

APP_PORT=3000

LOG_LEVEL=debug

ENV=production
```

---

# ConfigMap Characteristics

- Stores plain text
- Not encrypted
- Namespace scoped
- Can be mounted as:
  - Environment Variables
  - Files (Volumes)

---

# ConfigMap Flow

```
ConfigMap

↓

Pod

↓

Application Reads Config
```

---

# ConfigMap Important Fields

```yaml
data:
binaryData:
```

---

# Using ConfigMap

## As Environment Variable

```
ConfigMap

↓

env

↓

Application
```

---

## As Volume

```
ConfigMap

↓

Volume

↓

Container Directory
```

Each key becomes a file.

---

# 2. Secret

## What is a Secret?

A **Secret** stores **sensitive information**.

Examples:

- Database Password
- API Keys
- JWT Secret
- OAuth Tokens
- TLS Certificates

---

# Secret Examples

```
DB_PASSWORD

JWT_SECRET

AWS_ACCESS_KEY

API_TOKEN
```

---

# Secret Characteristics

- Base64 encoded (not encrypted by default)
- Namespace scoped
- Can be mounted as:
  - Environment Variables
  - Files (Volumes)

---

# Secret Flow

```
Secret

↓

Pod

↓

Application
```

---

# Secret Important Fields

```yaml
data:
stringData:
type:
```

---

## data

Stores Base64 encoded values.

Example

```
cm9vdA==
```

↓

```
root
```

---

## stringData

Allows plain text.

Kubernetes automatically converts it to Base64.

---

## type

Common Types

```
Opaque
```

Generic Secret.

```
kubernetes.io/tls
```

TLS Certificates.

```
kubernetes.io/dockerconfigjson
```

Docker Registry Credentials.

---

# ConfigMap vs Secret

| ConfigMap | Secret |
|------------|--------|
| Non-sensitive Data | Sensitive Data |
| Plain Text | Base64 Encoded |
| Configuration | Passwords, Tokens, Keys |
| Not Meant for Secrets | Designed for Secrets |

---

# Environment Variable Injection

```
ConfigMap

↓

env

↓

Container
```

```
Secret

↓

env

↓

Container
```

---

# Volume Mount

```
ConfigMap

↓

Volume

↓

Files
```

```
Secret

↓

Volume

↓

Files
```

Each key becomes a file.

---

# Common Commands

## ConfigMap

Create

```bash
kubectl apply -f configmap.yaml
```

List

```bash
kubectl get configmaps
```

Describe

```bash
kubectl describe configmap <name>
```

Delete

```bash
kubectl delete configmap <name>
```

---

## Secret

Create

```bash
kubectl apply -f secret.yaml
```

List

```bash
kubectl get secrets
```

Describe

```bash
kubectl describe secret <name>
```

View Secret

```bash
kubectl get secret <name> -o yaml
```

Delete

```bash
kubectl delete secret <name>
```

---

# Common Use Cases

## ConfigMap

- Database Host
- Redis Host
- Kafka Brokers
- Feature Flags
- Application Port
- Log Level

---

## Secret

- Database Password
- JWT Secret
- API Keys
- OAuth Credentials
- TLS Certificates
- Docker Registry Credentials

---

# ConfigMap vs Secret vs Environment Variables

| Feature | ConfigMap | Secret | Hardcoded Env |
|-----------|-----------|--------|---------------|
| Sensitive Data | ❌ | ✅ | ❌ |
| Update Without Rebuilding Image | ✅ | ✅ | ❌ |
| Mounted as File | ✅ | ✅ | ❌ |
| Environment Variable | ✅ | ✅ | ✅ |

---

# Are Kubernetes Secrets Encrypted?

**By default, NO.**

Kubernetes Secrets are only **Base64 encoded**, **NOT encrypted**.

Example:

```
Password

↓

root123

↓

Base64 Encode

↓

cm9vdDEyMw==
```

Anyone can decode it easily:

```bash
echo "cm9vdDEyMw==" | base64 --decode
```

Output

```
root123
```

**Base64 is NOT security.**

---

# Why does Kubernetes use Base64?

Main reasons:

- Store binary data safely
- Avoid YAML formatting issues
- Handle special characters consistently

It is **not** intended as a security mechanism.

---

# How to Protect Kubernetes Secrets?

## 1. Encryption at Rest ⭐ (Recommended)

By default:

```
Secret

↓

etcd

↓

Stored as Base64
```

Enable **Encryption at Rest**:

```
Secret

↓

API Server

↓

AES Encryption

↓

etcd
```

Now, even if someone accesses `etcd`, they cannot read the Secret without the encryption key.

This is the **recommended production approach**.

---

## 2. RBAC (Role-Based Access Control)

Limit who can access Secrets.

Example:

```
Developer A

↓

Can Read Pods

Cannot Read Secrets

------------------------

Admin

↓

Can Read Secrets
```

Never give Secret access to everyone.

---

## 3. Restrict etcd Access

Secrets are stored in **etcd**.

Only cluster administrators should have access to it.

Protect:

- etcd backups
- etcd network access
- etcd authentication

---

## 4. Use External Secret Managers ⭐

Instead of storing secrets in Kubernetes, use dedicated secret management systems.

Popular options:

- HashiCorp Vault
- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager

Flow:

```
Application

↓

External Secret Manager

↓

Kubernetes

↓

Pod
```

Secrets remain outside the cluster.

---

## 5. Mount Secrets as Volumes (Preferred)

Instead of:

```
Environment Variables
```

Prefer:

```
Secret

↓

Mounted File

↓

Application Reads File
```

Advantages:

- Easier secret rotation
- Avoids exposing secrets in process environment
- Better for certificates and keys

---

## 6. Rotate Secrets Regularly

Never keep the same password/API key forever.

Example:

```
Old Password

↓

Generate New Password

↓

Update Secret

↓

Restart/Reload Application
```

---

## 7. Don't Store Secrets in Git

❌ Never commit:

```yaml
apiVersion: v1
kind: Secret

data:
  password: cm9vdA==
```

Instead use:

- GitOps + Sealed Secrets
- External Secrets Operator
- CI/CD secret injection

---

# Best Practices

- Use Secrets instead of ConfigMaps for sensitive data.
- Enable **Encryption at Rest**.
- Restrict Secret access using **RBAC**.
- Prefer external Secret Managers for production.
- Rotate Secrets regularly.
- Never commit Secrets to Git repositories.
- Mount Secrets as files when possible.

---

# Interview Questions

## Are Kubernetes Secrets encrypted?

**No.**

By default, they are only **Base64 encoded**.

---

## Is Base64 secure?

No.

Anyone can decode it using:

```bash
echo "<base64>" | base64 --decode
```

---

## How do you secure Kubernetes Secrets?

- Enable Encryption at Rest
- Use RBAC
- Restrict etcd access
- Use External Secret Managers
- Rotate Secrets regularly
- Avoid storing Secrets in Git

---

## Where are Kubernetes Secrets stored?

```
etcd
```

---

## What is the recommended production approach?

- Encryption at Rest
- RBAC
- External Secret Manager (Vault, AWS Secrets Manager, Azure Key Vault, etc.)

---

# Memory Trick

```
Secret

↓

Base64

≠

Encryption

-------------------------

Production

↓

Encryption at Rest

+

RBAC

+

External Secret Manager
```

---

# One-Line Revision

- Kubernetes Secrets are **Base64 encoded, not encrypted**
- Secrets are stored in **etcd**
- Protect them using **Encryption at Rest**, **RBAC**, and **external Secret Managers**
- Never store Secrets in Git
- Prefer mounting Secrets as files over environment variables for sensitive data

---

# Interview Questions

## What is a ConfigMap?

A Kubernetes resource used to store non-sensitive configuration as key-value pairs.

---

## What is a Secret?

A Kubernetes resource used to store sensitive information such as passwords and API keys.

---

## Difference between ConfigMap and Secret?

| ConfigMap | Secret |
|------------|--------|
| Non-sensitive | Sensitive |
| Plain Text | Base64 Encoded |
| App Configuration | Credentials & Keys |

---

## Is a Secret encrypted?

**By default, No.**

Secrets are **Base64 encoded**, not encrypted.

For encryption, enable **Encryption at Rest** in the Kubernetes cluster.

---

## Can ConfigMaps and Secrets be mounted as files?

Yes.

Both can be mounted as:

- Environment Variables
- Volumes

---

## Which is better: Environment Variables or Volume Mounts?

- Environment Variables → Small configuration values.
- Volume Mounts → Certificates, config files, large configuration.

---

## Can a running Pod automatically see ConfigMap changes?

- **Mounted as Volume:** Usually **Yes** (after a short delay).
- **Environment Variables:** **No**, Pod restart is required.

---

## Can Secrets be viewed using kubectl?

Yes.

```bash
kubectl get secret my-secret -o yaml
```

Values are Base64 encoded and can be decoded.

---

# Best Practices

- Never store passwords in ConfigMaps.
- Use Secrets for all sensitive data.
- Use `stringData` when creating Secrets manually.
- Mount certificates as files instead of environment variables.
- Enable Encryption at Rest in production.
- Avoid committing Secrets to Git repositories.

---

# Memory Trick

```
ConfigMap

↓

Configuration

↓

Non-Sensitive

------------------------

Secret

↓

Credentials

↓

Sensitive
```

```
ConfigMap

=

App Settings

-------------------

Secret

=

Passwords / Tokens / Keys
```

---

# One-Line Revision

- ConfigMap stores non-sensitive configuration
- Secret stores sensitive information
- Both are namespace-scoped resources
- Both can be injected as environment variables or mounted as volumes
- Secret values are Base64 encoded by default (not encrypted)
- Use `stringData` for easier Secret creation
- Use ConfigMaps for app configuration and Secrets for credentials
- Restart Pods when environment variable-based ConfigMaps/Secrets change