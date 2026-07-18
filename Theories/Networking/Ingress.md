# Kubernetes Ingress - Interview Revision

# What is an Ingress?

An **Ingress** is a Kubernetes resource that manages **external HTTP/HTTPS traffic** and routes it to the appropriate **Service** based on **hostnames** or **URL paths**.

Ingress **does NOT communicate directly with Pods**.

---

# Why do we need Ingress?

Without Ingress:

```
Internet

↓

Service A

Internet

↓

Service B

Internet

↓

Service C
```

Every Service needs its own external exposure.

With Ingress:

```
Internet

↓

Ingress

↓

Service A

Service B

Service C
```

Single entry point for the entire cluster.

---

# Ingress Architecture

```
Internet
      │
      ▼
Ingress Controller
      │
      ▼
Ingress Rules
      │
 ┌────┴────────────┐
 ▼                 ▼
Service A      Service B
     │              │
     ▼              ▼
Pods           Pods
```

---

# Important Components

## 1. Ingress Resource

Stores routing rules.

Example:

```yaml
kind: Ingress
```

Contains:

- URL rules
- Host rules
- Backend Services

It **does not process traffic**.

---

## 2. Ingress Controller

Actual software that processes requests.

Examples:

- NGINX Ingress Controller
- Traefik
- Kong
- HAProxy

Without an Ingress Controller, an Ingress resource does nothing.

---

# Request Flow

```
Browser

↓

Ingress Controller

↓

Ingress Rules

↓

Service

↓

Pod
```

Remember:

```
Ingress

↓

Service

↓

Pod
```

Never

```
Ingress

↓

Pod
```

---

# Responsibilities

- URL Path Routing
- Hostname Routing
- SSL/TLS Termination
- Single Entry Point
- Reverse Proxy

---

# Important YAML Fields

```yaml
rules:
paths:
backend:
service:
annotations:
tls:
```

---

## rules

Defines routing rules.

Example

```yaml
rules:
```

---

## paths

Defines URL paths.

Example

```yaml
path: /products
```

---

## pathType

Types:

```
Prefix

Exact

ImplementationSpecific
```

Most commonly used:

```
Prefix
```

---

## backend

Destination Service.

Example

```yaml
backend:
  service:
```

---

## service

Target Kubernetes Service.

Example

```yaml
service:
  name: product-service
```

---

## port

Service Port.

Example

```yaml
port:
  number: 80
```

---

## annotations

Controller-specific configuration.

Example

```yaml
nginx.ingress.kubernetes.io/rewrite-target: /
```

Used by NGINX Ingress Controller.

---

## tls

Configure HTTPS certificates.

Example

```yaml
tls:
```

---

# Path Routing

```
/products

↓

product-service

-----------------------

/orders

↓

order-service

-----------------------

/

↓

frontend-service
```

---

# Host Routing

```
api.example.com

↓

api-service

---------------------

admin.example.com

↓

admin-service
```

---

# Understanding `nginx.ingress.kubernetes.io/rewrite-target`

## Why do we need Rewrite Target?

Ingress can expose an application under a **different URL path** than the application actually serves.

Sometimes:

- User accesses:
  ```
  http://example.com/nginx
  ```

- But the application (inside the Pod) only understands:
  ```
  /
  ```

If we directly forward `/nginx` to the application, it may return **404 Not Found** because it doesn't have a route called `/nginx`.

This is where **rewrite-target** is used.

---

# Real Example

Suppose you have an Nginx Pod.

Your Deployment:

```
Nginx Pod

↓

Listening on

http://localhost:80/
```

It serves:

```
/

index.html
```

It **does not** serve:

```
/nginx
```

---

## Ingress without Rewrite

Ingress

```yaml
paths:
- path: /nginx
  backend:
    service:
      name: nginx-service
      port:
        number: 80
```

Request Flow

```
Browser

GET /nginx

↓

Ingress

↓

Service

↓

Nginx Pod

Receives

GET /nginx
```

Now Nginx searches for

```
/usr/share/nginx/html/nginx
```

instead of

```
/usr/share/nginx/html/index.html
```

Result

```
404 Not Found
```

because `/nginx` doesn't exist.

---

# Ingress with Rewrite

Ingress

```yaml
metadata:
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
```

Request

```
Browser

GET /nginx
```

Ingress changes it to

```
GET /
```

before forwarding.

Complete Flow

```
Browser

GET /nginx

↓

Ingress

Rewrite

↓

GET /

↓

Service

↓

Nginx Pod

↓

Returns index.html
```

Now the application works perfectly.

---

# Visual Comparison

## Without Rewrite

```
Browser

GET /nginx

↓

Ingress

↓

Service

↓

Pod

GET /nginx

↓

404 Not Found
```

---

## With Rewrite

```
Browser

GET /nginx

↓

Ingress

↓

Rewrite

↓

GET /

↓

Service

↓

Pod

↓

200 OK
```

---

# Your Example

Suppose your Ingress is:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress

metadata:
  name: nginx-ingress

  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /

spec:
  rules:
  - http:
      paths:
      - path: /nginx
        pathType: Prefix

        backend:
          service:
            name: nginx-service
            port:
              number: 80
```

Now when the browser opens:

```
http://localhost/nginx
```

Internally Kubernetes performs:

```
Browser

GET /nginx

↓

Ingress

↓

Rewrite

↓

GET /

↓

nginx-service

↓

Nginx Pod
```

The user **still sees `/nginx` in the browser**, but the Pod receives `/`.

---

# Another Real-World Example

Suppose your company has three applications:

```
Frontend

↓

/

----

API

↓

/api

----

Admin

↓

/admin
```

But internally:

```
Frontend listens on /

API listens on /

Admin listens on /
```

Ingress configuration:

```yaml
/       → frontend-service
/api    → api-service
/admin  → admin-service
```

With rewrite enabled:

```
Browser

GET /api/users

↓

Ingress

↓

Rewrite

↓

GET /users

↓

api-service

↓

API Pod
```

The API doesn't need to know that users are accessing it through `/api`; it simply handles `/users`.

---

# When should you use Rewrite?

Use `rewrite-target` when:

- Your application serves content from `/`.
- You want to expose it under a different path (e.g., `/nginx`, `/api`, `/admin`).
- The application is **not aware** of the external prefix.

Do **not** use it if your application already understands the full incoming path.

Example:

If your application already has routes like:

```
/api/users

/api/orders
```

then rewriting `/api` to `/` would break routing.

---

# Interview Questions

### Why is `rewrite-target` used?

To modify the incoming request path before forwarding it to the backend Service, allowing applications to be exposed under different URL prefixes without changing the application itself.

---

### Does `rewrite-target` change the URL in the browser?

**No.**

It only changes the request **inside the cluster** before it reaches the backend.

---

### Where does rewriting happen?

```
Browser

↓

Ingress Controller (NGINX)

↓

Rewrite URL

↓

Service

↓

Pod
```

The Service and Pod never know the original external path after it has been rewritten.

---

# Memory Trick

```
Browser URL

↓

/nginx

↓

Ingress

↓

Rewrite

↓

/

↓

Application
```

**Browser sees:** `/nginx`

**Application receives:** `/`

---

# Ingress vs Service

| Ingress | Service |
|----------|----------|
| Routes to Services | Routes to Pods |
| URL/Host Routing | Load Balancing |
| External Entry Point | Internal Networking |
| HTTP/HTTPS Only | TCP/UDP Support |

---

# Ingress vs LoadBalancer

| Ingress | LoadBalancer |
|----------|--------------|
| One IP for many Services | Usually one Service per LoadBalancer |
| URL/Host Routing | No URL Routing |
| Application Layer (L7) | Network Layer (L4) |

---

# Ingress vs API Gateway

| Ingress | API Gateway |
|----------|-------------|
| URL Routing | Business Routing |
| TLS | Authentication |
| Reverse Proxy | Rate Limiting |
| Host Routing | JWT Validation |
| Basic Routing | Request Transformation |

Ingress is **not** a replacement for an API Gateway.

---

# Common Commands

Create

```bash
kubectl apply -f ingress.yaml
```

List

```bash
kubectl get ingress
```

Describe

```bash
kubectl describe ingress <name>
```

Delete

```bash
kubectl delete ingress <name>
```

---

# Interview Questions

## What is an Ingress?

A Kubernetes resource that routes external HTTP/HTTPS traffic to Services.

---

## Does Ingress communicate directly with Pods?

No.

```
Ingress

↓

Service

↓

Pods
```

---

## What is an Ingress Controller?

Software that reads Ingress resources and implements the routing.

Examples:

- NGINX
- Traefik
- Kong

---

## Can Ingress work without an Ingress Controller?

No.

Ingress rules require an Ingress Controller to process traffic.

---

## Why do we need Ingress?

To expose multiple Services through a single external entry point using host- and path-based routing.

---

## Difference between Service and Ingress?

```
Ingress

↓

Chooses Service

------------------------

Service

↓

Chooses Pod
```

---

## What is rewrite-target?

Rewrites the incoming URL path before forwarding it to the backend Service.

Example:

```
/nginx

↓

/

↓

Backend
```

---

## What is pathType?

Defines how URL matching is performed.

- Prefix
- Exact
- ImplementationSpecific

---

## Can Ingress expose TCP services?

Generally **No**.

Ingress is mainly for **HTTP/HTTPS (Layer 7)** traffic.

---

## Can one Ingress route to multiple Services?

Yes.

Example:

```
/products

↓

product-service

-------------------

/orders

↓

order-service

-------------------

/users

↓

user-service
```

---

# Best Practices

- Use Ingress as the single external entry point.
- Use meaningful URL paths and hostnames.
- Always deploy an Ingress Controller.
- Configure TLS for production.
- Keep Services as ClusterIP behind Ingress.
- Use annotations only when supported by the chosen Ingress Controller.

---

# Memory Trick

```
Internet

↓

Ingress

↓

Service

↓

Pod
```

Remember:

- **Ingress chooses the Service**
- **Service chooses the Pod**

```
Ingress

↓

URL / Host Routing

--------------------

Service

↓

Load Balancing
```

---

# One-Line Revision

- Ingress routes external HTTP/HTTPS traffic to Services
- Requires an Ingress Controller (e.g., NGINX, Traefik)
- Supports path-based and host-based routing
- Does not communicate directly with Pods
- `rewrite-target` modifies the request path before forwarding
- Ingress = URL/Host routing; Service = Pod discovery and load balancing
- Use Ingress as the single entry point for web applications