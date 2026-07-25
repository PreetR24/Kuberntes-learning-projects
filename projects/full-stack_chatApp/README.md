# 🚀 Full Stack Real-Time Chat Application

A production-style full-stack real-time chat application built with modern web technologies and deployed on Kubernetes using industry-standard practices. The project demonstrates containerization, orchestration, networking, ingress routing, and scalable deployment.

---

# ✨ Features

## 💬 Real-Time Communication

* Real-time one-to-one messaging using Socket.IO
* Instant message delivery
* Live online/offline user status

## 🔐 Authentication & Security

* JWT-based authentication
* Protected API routes
* Secure user authorization
* Password hashing using bcrypt

## 👤 User Management

* User registration and login
* Profile picture upload
* Profile management
* Logout functionality

## 🎨 Modern Frontend

* React-based Single Page Application
* Tailwind CSS + DaisyUI
* Responsive UI
* Zustand for state management

## ⚙️ Backend

* RESTful API using Express.js
* MongoDB database integration
* Socket.IO server
* Environment-based configuration
* Production-ready Nginx frontend

---

# 🐳 Docker Implementation

The application is fully containerized using Docker.

### Backend

* Multi-stage Docker build
* Optimized production image
* Environment variable support

### Frontend

* React production build
* Served using Nginx
* Custom Nginx configuration
* SPA routing support

### Database

* MongoDB containerized deployment

---

# ☸️ Kubernetes Implementation

The complete application has been deployed on a local Kubernetes cluster using **Kind (Kubernetes in Docker)**.

## Kubernetes Resources

### Namespace

* Dedicated namespace (`chat-app`) for application isolation

### Deployments

* Frontend Deployment
* Backend Deployment
* MongoDB Deployment

### Services

* Frontend ClusterIP Service
* Backend ClusterIP Service
* MongoDB ClusterIP Service

### ConfigMaps

* Backend configuration
* Frontend configuration
* Environment management

### Secrets

* MongoDB credentials
* JWT Secret
* Secure environment variables

### Persistent Storage

* PersistentVolume (PV)
* PersistentVolumeClaim (PVC)
* Persistent MongoDB storage

### Networking

* Internal service-to-service communication
* Kubernetes DNS-based service discovery
* ClusterIP networking

### Ingress

* NGINX Ingress Controller
* Host-based routing
* Frontend routing
* Backend API routing

Example routing:

```
chat.local/
        │
        ▼
NGINX Ingress
        │
 ┌──────┴────────┐
 │               │
 ▼               ▼
Frontend      Backend
(Service)     (Service)
 │               │
 ▼               ▼
Frontend Pod   Backend Pod
                    │
                    ▼
              MongoDB Service
                    │
                    ▼
               MongoDB Pod
```

---

# 📦 Container Orchestration

Implemented Kubernetes best practices including:

* Declarative YAML manifests
* Replica-based deployments
* Rolling updates
* Label selectors
* Service discovery
* Internal DNS resolution
* Resource isolation using namespaces
* Production-style ingress routing

---

# 🛠️ Tech Stack

### Frontend

* React
* Tailwind CSS
* DaisyUI
* Zustand

### Backend

* Node.js
* Express.js
* Socket.IO
* JWT
* bcrypt

### Database

* MongoDB

### DevOps

* Docker
* Docker Compose
* Kubernetes
* Kind
* NGINX Ingress Controller
* Nginx

---

# 📚 Learning Outcomes

This project demonstrates practical experience with:

* Full-stack application development
* Docker containerization
* Kubernetes deployments
* Kubernetes Services
* ConfigMaps & Secrets
* Persistent Volumes
* Persistent Volume Claims
* Kubernetes networking
* Ingress Controller configuration
* Service discovery
* Production deployment architecture
* Container orchestration
