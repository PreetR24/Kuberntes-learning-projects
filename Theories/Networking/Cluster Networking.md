# Kubernetes Cluster Networking - Interview Revision

# What is Cluster Networking?

Cluster Networking is the communication mechanism that allows Kubernetes components, Pods, Services, and external clients to communicate with each other.

It ensures seamless connectivity inside and outside the cluster.

---

# Kubernetes Networking Model

Kubernetes guarantees:

- Every Pod gets its own unique IP.
- Pods can communicate without NAT.
- Containers inside a Pod communicate using `localhost`.
- Services provide stable access to Pods.
- Ingress exposes Services to external users.

---

# Networking Architecture

```
Internet
     │
     ▼
Ingress
     │
     ▼
Service
     │
     ▼
Pod
     │
     ▼
Container
```

---

# Communication Types

## 1. Container → Container (Same Pod)

Uses:

```
localhost
```

Reason:

Containers in the same Pod share the same network namespace.

---

## 2. Pod → Pod

Uses:

```
Pod IP
```

Example

```
Pod A

↓

10.244.2.5

↓

Pod B
```

Pods communicate directly without NAT.

---

## 3. Pod → Service

Recommended approach.

```
Pod

↓

Service DNS

↓

Service

↓

Target Pod
```

Example

```
http://product-service
```

---

## 4. External User → Cluster

```
Internet

↓

Ingress / LoadBalancer

↓

Service

↓

Pods
```

---

# Pod Networking

Each Pod receives:

- Unique IP
- Shared network namespace
- Shared localhost

Example

```
Pod A

IP: 10.244.1.5

Pod B

IP: 10.244.2.8
```

---

# Pod IP Characteristics

- Unique within cluster
- Changes when Pod is recreated
- Never use Pod IP in applications

Always use:

```
Service
```

---

# Service Networking

Purpose:

- Stable IP
- Stable DNS
- Load Balancing

Example

```
Client

↓

product-service

↓

Pod1

Pod2

Pod3
```

---

# Service Types

## ClusterIP (Default)

Internal communication only.

```
Pod

↓

Service

↓

Pod
```

---

## NodePort

Exposes Service on every Node.

```
NodeIP:30080
```

---

## LoadBalancer

Creates cloud load balancer.

```
Internet

↓

Cloud Load Balancer

↓

Service

↓

Pods
```

---

## ExternalName

Maps Service to an external DNS.

---

# Ingress

Purpose:

Route HTTP/HTTPS traffic to Services.

```
Internet

↓

Ingress

↓

Service

↓

Pods
```

Ingress does **not** connect directly to Pods.

---

# DNS

Every Service automatically gets DNS.

Within same namespace:

```
product-service
```

Across namespaces:

```
product-service.backend.svc.cluster.local
```

---

# kube-proxy

Runs on every Worker Node.

Responsibilities:

- Implements Services
- Load balances traffic
- Manages iptables/IPVS rules

---

# CNI (Container Network Interface)

Responsible for Pod networking.

Common CNI Plugins:

- Calico
- Cilium
- Flannel
- Weave Net

Responsibilities:

- Pod IP allocation
- Pod routing
- Network connectivity

---

# Network Flow

## Internal Request

```
Pod

↓

Service DNS

↓

kube-proxy

↓

Target Pod
```

---

## External Request

```
Browser

↓

Ingress

↓

Service

↓

kube-proxy

↓

Pod
```

---

# Important Networking Components

| Component | Responsibility |
|------------|----------------|
| Pod | Runs application |
| Service | Stable endpoint & load balancing |
| Ingress | HTTP/HTTPS routing |
| kube-proxy | Service networking |
| CNI Plugin | Pod networking |
| CoreDNS | DNS resolution |

---

# Common Commands

View Services

```bash
kubectl get svc
```

View Endpoints

```bash
kubectl get endpoints
```

View Pods with IP

```bash
kubectl get pods -o wide
```

DNS Test

```bash
kubectl exec -it <pod> -- nslookup product-service
```

---

# Interview Questions

## Can Pods communicate directly?

Yes.

Using Pod IP.

Best practice:

Use Services.

---

## Why not use Pod IP?

Pod IP changes whenever Pod is recreated.

---

## Why do we need Services?

- Stable IP
- Stable DNS
- Load Balancing

---

## Can Services communicate directly with Pods?

Yes.

Services use label selectors to discover Pods.

---

## Does Ingress communicate with Pods?

No.

```
Ingress

↓

Service

↓

Pods
```

---

## What is kube-proxy?

Node component responsible for implementing Service networking and load balancing.

---

## What is CNI?

Container Network Interface.

Responsible for Pod networking.

---

## What provides DNS in Kubernetes?

```
CoreDNS
```

---

## How do Pods communicate across Nodes?

Through the CNI plugin, which routes Pod traffic between nodes.

---

# Best Practices

- Never use Pod IP directly.
- Always communicate using Services.
- Use Ingress for HTTP/HTTPS exposure.
- Use ClusterIP for internal communication.
- Use DNS instead of hardcoded IPs.
- Keep networking handled by CNI plugins.

---

# Memory Trick

```
Container

↓

Pod

↓

Service

↓

Ingress

↓

Internet
```

```
CoreDNS
=
Name Resolution

kube-proxy
=
Service Routing

CNI
=
Pod Networking
```

---

# One-Line Revision

- Every Pod gets a unique IP
- Containers inside a Pod communicate using `localhost`
- Pods communicate using Pod IP (prefer Services)
- Services provide stable IP, DNS, and load balancing
- Ingress routes external HTTP/HTTPS traffic to Services
- kube-proxy implements Service networking
- CNI provides Pod-to-Pod networking
- CoreDNS resolves Service names
- Never hardcode Pod IPs; always use Services