# Kubernetes Probes - Interview Revision

# What are Probes?

**Probes** are health checks performed by **kubelet** to determine the state of a container.

They help Kubernetes answer three questions:

- **Has the application started?** → Startup Probe
- **Can the application receive traffic?** → Readiness Probe
- **Is the application still running correctly?** → Liveness Probe

---

# Types of Probes

| Probe | Purpose | On Failure |
|--------|---------|------------|
| Startup Probe | Checks if the application has started | Restart Container |
| Readiness Probe | Checks if the application is ready to serve traffic | Remove Pod from Service |
| Liveness Probe | Checks if the application is still healthy | Restart Container |

---

# Probe Lifecycle

```
Pod Created
      │
      ▼
Startup Probe
      │
      ├── Fail ❌
      │       │
      │       ▼
      │  Restart Container
      │
      └── Success ✅
              │
              ▼
      Readiness Probe
              │
      ├── Success ✅
      │       │
      │       ▼
      │  Service Sends Traffic
      │
      └── Failure ❌
              │
              ▼
      Remove Pod From Service
      (Container Keeps Running)
              │
              ▼
      Liveness Probe
              │
      ├── Success ✅
      │       │
      │       ▼
      │  Container Continues Running
      │
      └── Failure ❌
              │
              ▼
      Restart Container
```

---

# 1. Startup Probe

## Purpose

Checks whether the application has **started successfully**.

Useful for:

- Spring Boot
- Large Java Applications
- Slow Database Startup
- Applications requiring a long initialization time

### Flow

```
Pod Created

↓

Startup Probe

↓

Success

↓

Enable Readiness & Liveness

----------------------------

Failure

↓

Retry

↓

failureThreshold Reached

↓

Restart Container
```

---

# 2. Readiness Probe

## Purpose

Checks whether the application is **ready to receive traffic**.

If it fails:

- Pod is **NOT restarted**
- Pod is removed from the Service Endpoints
- Existing requests continue if possible
- New traffic is stopped

### Flow

```
Pod Running

↓

Readiness Check

↓

Success

↓

Service Sends Traffic

----------------------------

Failure

↓

Remove Pod From Service

↓

No New Traffic

↓

Container Keeps Running
```

---

# 3. Liveness Probe

## Purpose

Checks whether the application is **still alive and responsive**.

If it fails:

- Kubernetes restarts the container.

Useful for:

- Deadlocks
- Infinite loops
- Hung applications

### Flow

```
Pod Running

↓

Liveness Check

↓

Success

↓

Continue Running

----------------------------

Failure

↓

Retry

↓

failureThreshold Reached

↓

Restart Container
```

---

# Probe Methods

## 1. HTTP GET

Kubernetes sends an HTTP request.

```yaml
httpGet:
  path: /health
  port: 3000
```

Equivalent to:

```
GET http://<pod-ip>:3000/health
```

Suitable for:

- REST APIs
- Spring Boot
- Express.js
- ASP.NET

---

## 2. TCP Socket

Checks whether a TCP connection can be established.

```yaml
tcpSocket:
  port: 3306
```

Suitable for:

- MySQL
- PostgreSQL
- Redis
- MongoDB

---

## 3. Exec

Runs a command inside the container.

```yaml
exec:
  command:
    - cat
    - /tmp/healthy
```

```
Exit Code 0

↓

Success

------------------------

Non-Zero Exit Code

↓

Failure
```

Suitable for:

- Custom health checks
- Internal scripts

---

# Important Probe Parameters

## initialDelaySeconds

Time Kubernetes waits **before the first probe**.

```yaml
initialDelaySeconds: 30
```

---

## periodSeconds

Time between consecutive probe checks.

```yaml
periodSeconds: 10
```

```
10s

↓

20s

↓

30s

↓

40s
```

---

## timeoutSeconds

Maximum time Kubernetes waits for a probe response.

```yaml
timeoutSeconds: 3
```

```
Response in 2s

↓

Success

------------------------

Response in 5s

↓

Failure
```

---

## successThreshold

Number of **consecutive successful probes** required before Kubernetes marks the probe as successful.

Mostly used with **Readiness Probe**.

Default:

```
1
```

Example

```yaml
successThreshold: 2
```

```
Success

↓

Success

↓

Pod Ready
```

---

## failureThreshold

Number of **consecutive failed probes** before Kubernetes performs an action.

Default:

```
3
```

Example

```yaml
failureThreshold: 3
```

```
Fail

↓

Fail

↓

Fail

↓

Action Taken
```

Actions:

- Startup → Restart
- Liveness → Restart
- Readiness → Remove from Service

---

# Parameter Summary

| Parameter | Purpose |
|------------|---------|
| `initialDelaySeconds` | Delay before first probe |
| `periodSeconds` | Interval between probe checks |
| `timeoutSeconds` | Maximum wait time for response |
| `successThreshold` | Consecutive successes required |
| `failureThreshold` | Consecutive failures before action |

---

# Probe Comparison

| Feature | Startup | Readiness | Liveness |
|----------|----------|-----------|-----------|
| Checks Startup | ✅ | ❌ | ❌ |
| Checks Traffic Readiness | ❌ | ✅ | ❌ |
| Checks Application Health | ❌ | ❌ | ✅ |
| Removes Pod From Service | ❌ | ✅ | ❌ |
| Restarts Container | ✅ | ❌ | ✅ |

---

# Common Commands

Describe Pod

```bash
kubectl describe pod <pod-name>
```

View Events

```bash
kubectl get events
```

View Logs

```bash
kubectl logs <pod-name>
```

---

# Interview Questions

## What are Kubernetes Probes?

Health checks performed by kubelet to determine a container's state.

---

## Difference between Startup, Readiness, and Liveness?

- Startup → Has the application started?
- Readiness → Can it receive traffic?
- Liveness → Is it still alive?

---

## Which probe removes a Pod from a Service?

```
Readiness Probe
```

---

## Which probes restart the container?

- Startup Probe
- Liveness Probe

---

## What happens if a Readiness Probe fails?

- Pod remains running.
- Removed from Service Endpoints.
- No new traffic is sent.

---

## What happens if a Liveness Probe fails?

Container is restarted.

---

## Which probe runs first?

```
Startup Probe

↓

Readiness Probe

↓

Liveness Probe
```

---

## Can Startup Probe disable Liveness and Readiness?

Yes.

Until Startup Probe succeeds, Kubernetes does **not** execute Readiness or Liveness probes.

---

# Best Practices

- Use **Startup Probe** for slow-starting applications.
- Use **Readiness Probe** for every production service.
- Use **Liveness Probe** to recover from deadlocks or hangs.
- Avoid aggressive probe timings to prevent unnecessary restarts.
- Use application-specific health endpoints (e.g., `/health`, `/ready`).

---

# Memory Trick

```
Startup

↓

Started?

↓

Restart if No

-----------------------

Readiness

↓

Ready?

↓

Traffic if Yes

-----------------------

Liveness

↓

Alive?

↓

Restart if No
```

---

# One-Line Revision

- **Startup Probe** → Checks application startup
- **Readiness Probe** → Controls whether traffic reaches the Pod
- **Liveness Probe** → Restarts unhealthy containers
- **Startup runs first**, then Readiness and Liveness
- **Readiness never restarts a Pod**
- **Liveness and Startup can restart containers**
- **kubelet executes all probes**