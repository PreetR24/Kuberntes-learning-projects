# Kubernetes RBAC (Role-Based Access Control) - Interview Revision

# What is RBAC?

**RBAC (Role-Based Access Control)** controls **who can perform which actions on which Kubernetes resources**.

Without RBAC:

```
Every User

↓

Can Do Everything ❌
```

With RBAC:

```
Developer

↓

Can View Pods

Cannot Delete Nodes

------------------------

Admin

↓

Full Access
```

RBAC follows the **Principle of Least Privilege**:

> Give users only the permissions they actually need.

---

# Why RBAC?

Imagine a production cluster.

There are different users:

```
Developer

QA

DevOps

Admin
```

Should everyone be able to:

- Delete Pods?
- Delete Namespaces?
- Read Secrets?
- Delete Nodes?

Obviously **No**.

RBAC solves this problem.

---

# RBAC Components

```
User / Group / ServiceAccount

↓

Role / ClusterRole

↓

RoleBinding / ClusterRoleBinding

↓

Permissions Granted
```

---

# RBAC Architecture

```
Request

↓

API Server

↓

Authentication

(Who are you?)

↓

Authorization (RBAC)

(Are you allowed?)

↓

Yes

↓

Perform Action

------------------------

No

↓

Forbidden (403)
```

---

# RBAC Objects

| Object | Purpose |
|----------|----------|
| Role | Namespace permissions |
| ClusterRole | Cluster-wide permissions |
| RoleBinding | Assign Role to User/Group/ServiceAccount |
| ClusterRoleBinding | Assign ClusterRole cluster-wide |

---

# Step 1 - Role

## What is a Role?

A **Role** defines **what actions are allowed inside one Namespace**.

It does **not** grant permissions by itself.

Example:

```
Namespace

↓

development

↓

Role

↓

Read Pods
```

---

## Role Scope

```
Namespace Only
```

Cannot manage resources outside its namespace.

---

## Role YAML

```yaml
kind: Role

rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get","list","watch"]
```

Meaning:

```
Pods

↓

View

List

Watch
```

---

# Step 2 - RoleBinding

## What is RoleBinding?

RoleBinding connects:

```
User

↓

Role

↓

Namespace
```

Without RoleBinding:

```
Role Exists

↓

Nobody Uses It
```

---

## Flow

```
Developer

↓

RoleBinding

↓

Developer Role

↓

Development Namespace
```

Now the developer receives those permissions.

---

# Step 3 - ClusterRole

## What is ClusterRole?

A **ClusterRole** grants permissions across the **entire cluster**.

Example permissions:

- Nodes
- PersistentVolumes
- Namespaces
- Cluster-wide Pods

---

## Cluster Scope

```
Entire Cluster
```

---

## ClusterRole Example

```yaml
kind: ClusterRole

rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["get","list"]
```

Meaning

```
View Nodes

Anywhere
```

---

# Step 4 - ClusterRoleBinding

## What is ClusterRoleBinding?

Connects:

```
User

↓

ClusterRole

↓

Entire Cluster
```

Flow

```
Admin

↓

ClusterRoleBinding

↓

ClusterRole

↓

Full Cluster Permissions
```

---

# Role vs ClusterRole

| Role | ClusterRole |
|------|-------------|
| Namespace only | Entire Cluster |
| Cannot access Nodes | Can access Nodes |
| Used with RoleBinding | Used with ClusterRoleBinding |

---

# RoleBinding vs ClusterRoleBinding

| RoleBinding | ClusterRoleBinding |
|--------------|--------------------|
| Namespace scope | Cluster scope |
| Binds Role or ClusterRole to one Namespace | Binds ClusterRole across the entire cluster |

> **Important:** A **RoleBinding** can bind either a **Role** or a **ClusterRole**, but the permissions apply **only within that namespace**.

---

# Subjects

Permissions can be assigned to:

```
User

Group

ServiceAccount
```

---

# ServiceAccount

Pods do **not** use normal users.

They use:

```
ServiceAccount
```

Flow

```
Pod

↓

ServiceAccount

↓

RBAC

↓

API Access
```

Without proper RBAC:

```
403 Forbidden
```

---

# Common Verbs

| Verb | Meaning |
|-------|----------|
| get | Read one resource |
| list | List resources |
| watch | Watch changes |
| create | Create |
| update | Update |
| patch | Partial Update |
| delete | Delete |
| deletecollection | Delete multiple resources |
| * | All actions |

---

# Common Resources

```
pods

services

deployments

configmaps

secrets

namespaces

nodes

persistentvolumes
```

---

# RBAC Decision Flow

```
User Requests

Delete Pod

↓

API Server

↓

Authenticated?

↓

Yes

↓

RBAC Check

↓

Role/ClusterRole Allows delete?

↓

Yes

↓

Delete Pod

------------------------

No

↓

403 Forbidden
```

---

# Common Commands

View Roles

```bash
kubectl get roles
```

View ClusterRoles

```bash
kubectl get clusterroles
```

View RoleBindings

```bash
kubectl get rolebindings
```

View ClusterRoleBindings

```bash
kubectl get clusterrolebindings
```

Describe Role

```bash
kubectl describe role <role-name>
```

Check Permission

```bash
kubectl auth can-i delete pods
```

Check for Another User

```bash
kubectl auth can-i get pods --as=john
```

---

# Built-in ClusterRoles

Some commonly used built-in ClusterRoles:

| ClusterRole | Purpose |
|--------------|---------|
| cluster-admin | Full cluster access |
| admin | Full namespace administration |
| edit | Read and modify most resources (cannot manage RBAC or some sensitive resources like Secrets depending on configuration) |
| view | Read-only access |

---

# Common Use Cases

## Developer

```
Read Pods

Create Deployments

Cannot Delete Namespace
```

---

## QA

```
View Logs

View Pods

Cannot Modify Resources
```

---

## DevOps

```
Manage Deployments

Services

Ingress

Secrets
```

---

## Cluster Admin

```
Everything
```

---

# Authentication vs Authorization

| Authentication | Authorization |
|----------------|---------------|
| Who are you? | What can you do? |
| Login | Permission Check |
| Happens First | Happens Second |

Flow

```
Authenticate

↓

Authorize (RBAC)

↓

Allow / Deny
```

---

# RBAC vs NetworkPolicy

| RBAC | NetworkPolicy |
|-------|---------------|
| Controls API permissions | Controls Pod network traffic |
| User/ServiceAccount based | Pod communication based |

---

# RBAC vs Admission Controller

| RBAC | Admission Controller |
|-------|----------------------|
| Checks permissions | Validates or mutates requests after authorization |
| Allow/Deny user actions | Enforces policies (e.g., required labels, image restrictions) |

---

# Complete RBAC Flow

```
Developer

↓

Authentication

↓

RBAC

↓

RoleBinding

↓

Role

↓

Allowed Verbs

↓

Access Resource
```

---

# Best Practices

- Follow the Principle of Least Privilege.
- Grant only the required permissions.
- Prefer **Role** over **ClusterRole** whenever possible.
- Use **ServiceAccounts** for Pods instead of broad user credentials.
- Avoid giving `cluster-admin` to regular users.
- Regularly audit Roles and RoleBindings.

---

# Interview Questions

## What is RBAC?

A Kubernetes authorization mechanism that controls who can perform which actions on which resources.

---

## Difference between Role and ClusterRole?

- **Role** → Namespace only.
- **ClusterRole** → Entire cluster.

---

## Difference between RoleBinding and ClusterRoleBinding?

- **RoleBinding** → Grants permissions within one namespace.
- **ClusterRoleBinding** → Grants permissions across the cluster.

---

## Can RoleBinding bind a ClusterRole?

Yes.

The permissions from the ClusterRole are **limited to that namespace**.

---

## What resources require ClusterRole?

Examples:

- Nodes
- Namespaces
- PersistentVolumes
- Cluster-wide resources

---

## What happens if RBAC denies access?

The API Server returns:

```
403 Forbidden
```

---

## How do Pods authenticate to the API Server?

Using a **ServiceAccount**.

---

## How do you check permissions?

```bash
kubectl auth can-i <verb> <resource>
```

---

## What is the Principle of Least Privilege?

Give users only the minimum permissions needed to perform their work.

---

# Memory Trick

```
Role

↓

Namespace

------------------------

ClusterRole

↓

Entire Cluster

------------------------

RoleBinding

↓

Connect User → Role

------------------------

ClusterRoleBinding

↓

Connect User → ClusterRole
```

---

# One-Line Revision

- **RBAC** controls access to Kubernetes resources.
- **Role** = Namespace permissions.
- **ClusterRole** = Cluster-wide permissions.
- **RoleBinding** grants permissions within one namespace.
- **ClusterRoleBinding** grants permissions across the cluster.
- **Authentication** identifies the user; **RBAC authorizes** the action.
- Pods use **ServiceAccounts** to access the Kubernetes API.
- If permission is denied, the API Server returns **403 Forbidden**.