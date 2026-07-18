# Kubernetes Services - Interview Revision

# What is a Service?

A **Service** is a Kubernetes networking resource that provides:

- Stable IP
- Stable DNS
- Load Balancing
- Service Discovery

It exposes one or more Pods through a **single stable endpoint**.

---

# Why do we need Services?

Pods are **ephemeral**.

When a Pod is recreated:

- New Pod
- New IP

Applications should never communicate using Pod IPs.

Instead:

```
Client

↓

Service

↓

Pods
```

---

# Service Architecture

```
Client
     │
     ▼
Service
     │
 ┌───┼───────────┐
 ▼   ▼           ▼
Pod1 Pod2      Pod3
```

Service load balances traffic among matching Pods.

---

# How does Service find Pods?

Using **Selectors**.

Service

```yaml
selector:
  app: nginx
```

Pods

```yaml
labels:
  app: nginx
```

↓

Service automatically discovers matching Pods.

---

# Request Flow

```
Client

↓

Service

↓

One Healthy Pod

↓

Response
```

---

# Service Responsibilities

- Stable IP
- Stable DNS
- Pod Discovery
- Internal Load Balancing
- Abstracts Pod IP changes

---

# Important YAML Fields

```yaml
selector:
ports:
type:
```

---

## selector

Identifies which Pods belong to the Service.

Example

```yaml
selector:
  app: nginx
```

---

## ports.port

Port exposed by the **Service**.

Clients connect here.

Example

```yaml
port: 80
```

---

## ports.targetPort

Port on the **Pod/Container** where the application is listening.

Example

```yaml
targetPort: 3000
```

Flow

```
Client

↓

Service:80

↓

Pod:3000
```

---

## protocol

Default:

```yaml
TCP
```

Other option:

```
UDP
```

---

## type

Determines how the Service is exposed.

---

# Service Types

## 1. ClusterIP (Default)

Internal communication only.

```
Pod

↓

ClusterIP Service

↓

Pods
```

Accessible only inside the cluster.

---

## 2. NodePort

Exposes Service on every node.

```
NodeIP:30080

↓

Service

↓

Pods
```

Accessible outside the cluster.

Port range:

```
30000-32767
```

---

## 3. LoadBalancer

Creates an external cloud load balancer.

```
Internet

↓

Cloud Load Balancer

↓

Service

↓

Pods
```

Supported on:

- AWS
- Azure
- GCP

---

## 4. ExternalName

Maps a Kubernetes Service to an external DNS.

Example

```
db.company.com
```

---

# Service Types Comparison

| Type | Accessible From |
|------|-----------------|
| ClusterIP | Inside Cluster |
| NodePort | Outside via Node IP |
| LoadBalancer | Internet (Cloud) |
| ExternalName | External DNS |

---

# Networking Flow

## Internal Request

```
Pod

↓

Service

↓

Pod
```

---

## External Request

```
Internet

↓

Ingress

↓

Service

↓

Pods
```

---

# Service vs Ingress

| Service | Ingress |
|----------|----------|
| Routes to Pods | Routes to Services |
| Load Balancing | HTTP/HTTPS Routing |
| Uses Selectors | Uses URL/Host Rules |
| Internal Networking | External Entry Point |

---

# Service vs Pod

| Pod | Service |
|------|----------|
| Temporary IP | Stable IP |
| Runs Application | Routes Traffic |
| Can Change | Stable Endpoint |

---

# Service vs Load Balancer

| Service | Cloud Load Balancer |
|----------|---------------------|
| Kubernetes Resource | Cloud Resource |
| Internal LB | External LB |
| Routes to Pods | Routes to Services |

---

# ClusterIP vs NodePort vs LoadBalancer

```
ClusterIP

Pod

↓

Service

↓

Pod

-------------------------

NodePort

Internet

↓

NodeIP:30080

↓

Service

↓

Pods

-------------------------

LoadBalancer

Internet

↓

Cloud LB

↓

Service

↓

Pods
```

---

# Common Commands

Create

```bash
kubectl apply -f service.yaml
```

List

```bash
kubectl get svc
```

Describe

```bash
kubectl describe svc <service-name>
```

Delete

```bash
kubectl delete svc <service-name>
```

Port Forward

```bash
kubectl port-forward service/nginx-service 8080:80
```

---

# Interview Questions

## What is a Service?

A Kubernetes networking resource that provides a stable endpoint and load balances traffic to Pods.

---

## Why not communicate using Pod IP?

Pod IP changes whenever Pods are recreated.

---

## How does a Service find Pods?

Using **Label Selectors**.

---

## What is the difference between port and targetPort?

```
port

↓

Service Port

Client connects here.

------------------------

targetPort

↓

Pod Port

Application listens here.
```

---

## Can Service communicate directly with Pods?

Yes.

Using selectors.

---

## Does Service create Pods?

No.

Service is only a networking resource.

---

## Does Service load balance?

Yes.

Across all healthy matching Pods.

---

## Does Service perform URL routing?

No.

Ingress performs URL/Host routing.

---

## Can Service expose applications to the Internet?

Only:

- NodePort
- LoadBalancer

For production HTTP/HTTPS, use:

```
Ingress

↓

Service

↓

Pods
```

---

## What happens if a Pod crashes?

```
Pod Deleted

↓

Deployment Creates New Pod

↓

Service Automatically Updates Endpoints

↓

Traffic Continues
```

---

# Best Practices

- Never use Pod IPs.
- Always expose applications using Services.
- Use ClusterIP for internal communication.
- Use Ingress for HTTP/HTTPS traffic.
- Use meaningful labels and selectors.
- Keep selectors consistent with Pod labels.

---

# Memory Trick

```
Client

↓

Service

↓

Pod
```

```
Service

↓

Stable IP

↓

Stable DNS

↓

Load Balancing

↓

Service Discovery
```

Remember:

- **Service chooses a Pod**
- **Ingress chooses a Service**

---

# One-Line Revision

- Service provides a stable IP and DNS for Pods
- Uses selectors to discover matching Pods
- Load balances traffic across healthy Pods
- `port` = Service port, `targetPort` = Pod/container port
- Service does not create Pods or perform URL routing
- Types: ClusterIP, NodePort, LoadBalancer, ExternalName
- Use Services instead of Pod IPs
- Ingress routes to Services; Services route to Pods