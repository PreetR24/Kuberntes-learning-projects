# Kubernetes Jobs & CronJobs - Interview Revision

# 1. Job

## What is a Job?

A **Job** is a Kubernetes controller that runs a task **until it completes successfully**.

Unlike a Deployment, a Job is **not meant to run forever**.

Examples:

- Database Migration
- Backup
- Report Generation
- Data Import/Export
- Batch Processing

---

# Job Workflow

```
Create Job

↓

Pod Created

↓

Task Executes

↓

Success

↓

Job Completed
```

If the Pod fails:

```
Pod Fails

↓

Job Detects

↓

Creates New Pod

↓

Retries Until Success
```

---

# Job Characteristics

- Runs once (or until completion)
- Retries on failure
- Stops after successful completion
- Suitable for batch workloads

---

# Important YAML Fields

```yaml
completions:
parallelism:
backoffLimit:
template:
```

---

## completions

Number of successful executions required.

```yaml
completions: 1
```

---

## parallelism

Number of Pods that can run simultaneously.

```yaml
parallelism: 3
```

---

## backoffLimit

Maximum retry attempts before marking Job as failed.

```yaml
backoffLimit: 4
```

---

## restartPolicy

Usually:

```yaml
restartPolicy: Never
```

or

```yaml
restartPolicy: OnFailure
```

---

# Job Lifecycle

```
Job

↓

Pod Created

↓

Task Running

↓

Success

↓

Completed
```

---

# Common Commands

Create

```bash
kubectl apply -f job.yaml
```

List

```bash
kubectl get jobs
```

Describe

```bash
kubectl describe job <job-name>
```

View Logs

```bash
kubectl logs job/<job-name>
```

Delete

```bash
kubectl delete job <job-name>
```

---

# 2. CronJob

## What is a CronJob?

A **CronJob** creates Jobs automatically according to a **schedule**.

Think of it as Linux **cron** for Kubernetes.

Examples:

- Daily Backup
- Weekly Cleanup
- Monthly Reports
- Log Rotation
- Scheduled Emails

---

# CronJob Workflow

```
Cron Schedule

↓

CronJob Triggered

↓

Creates Job

↓

Creates Pod

↓

Task Executes

↓

Completed

↓

Wait For Next Schedule
```

---

# Cron Schedule Format

```
* * * * *
│ │ │ │ │
│ │ │ │ └── Day of Week (0-7)
│ │ │ └──── Month (1-12)
│ │ └────── Day of Month (1-31)
│ └──────── Hour (0-23)
└────────── Minute (0-59)
```

---

# Common Schedules

| Schedule | Meaning |
|-----------|---------|
| `* * * * *` | Every minute |
| `*/5 * * * *` | Every 5 minutes |
| `0 * * * *` | Every hour |
| `0 0 * * *` | Every day at midnight |
| `0 2 * * 0` | Every Sunday at 2 AM |
| `0 0 1 * *` | First day of every month |

---

# Important YAML Fields

```yaml
schedule:
jobTemplate:
successfulJobsHistoryLimit:
failedJobsHistoryLimit:
concurrencyPolicy:
```

---

## schedule

Cron expression.

Example

```yaml
schedule: "0 2 * * *"
```

---

## jobTemplate

Template used to create a Job every time the schedule triggers.

---

## successfulJobsHistoryLimit

Number of completed Jobs to keep.

---

## failedJobsHistoryLimit

Number of failed Jobs to keep.

---

## concurrencyPolicy

Controls concurrent executions.

Options:

### Allow (Default)

Allow multiple Jobs simultaneously.

---

### Forbid

Do not start a new Job if the previous one is still running.

---

### Replace

Stop the current Job and start a new one.

---

# Job vs CronJob

| Job | CronJob |
|------|----------|
| Runs once | Runs on a schedule |
| Manually triggered | Automatically triggered |
| One-time task | Recurring task |
| Creates Pods | Creates Jobs → Pods |

---

# Lifecycle Comparison

## Job

```
Job

↓

Pod

↓

Completed
```

---

## CronJob

```
CronJob

↓

Job

↓

Pod

↓

Completed

↓

Wait For Next Schedule
```

---

# Common Commands

Create

```bash
kubectl apply -f cronjob.yaml
```

List

```bash
kubectl get cronjobs
```

Describe

```bash
kubectl describe cronjob <name>
```

Suspend

```bash
kubectl patch cronjob <name> -p '{"spec":{"suspend":true}}'
```

Resume

```bash
kubectl patch cronjob <name> -p '{"spec":{"suspend":false}}'
```

Delete

```bash
kubectl delete cronjob <name>
```

---

# Common Use Cases

## Job

- Database Migration
- Batch Processing
- Data Import
- Data Export
- One-time Backup

---

## CronJob

- Daily Backup
- Weekly Cleanup
- Scheduled Reports
- Email Notifications
- Log Cleanup

---

# Interview Questions

## What is a Job?

A Kubernetes controller that runs a task until it completes successfully.

---

## What is a CronJob?

A Kubernetes controller that creates Jobs automatically according to a schedule.

---

## Difference between Job and Deployment?

| Deployment | Job |
|------------|-----|
| Runs continuously | Runs until completion |
| Never exits | Exits after success |
| Stateless apps | Batch tasks |

---

## Difference between Job and CronJob?

Job = One-time execution

CronJob = Scheduled execution

---

## What happens if a Job Pod fails?

The Job creates a new Pod and retries until success or until `backoffLimit` is reached.

---

## What does backoffLimit do?

Maximum number of retries before marking the Job as failed.

---

## What does concurrencyPolicy do?

Controls overlapping executions.

- Allow
- Forbid
- Replace

---

## Does CronJob create Pods directly?

No.

```
CronJob

↓

Job

↓

Pod
```

---

# Best Practices

- Use **Job** for one-time tasks.
- Use **CronJob** for recurring tasks.
- Set `restartPolicy` to `Never` or `OnFailure`.
- Configure `backoffLimit` appropriately.
- Use `Forbid` for backups to avoid overlapping executions.
- Clean up old Jobs using history limits.

---

# Memory Trick

```
Deployment

↓

Runs Forever

------------------

Job

↓

Runs Once

------------------

CronJob

↓

Runs Repeatedly

↓

Schedule

↓

Job

↓

Pod
```

---

# One-Line Revision

- Job = One-time batch execution until success
- CronJob = Scheduled Job execution
- Job creates Pods; CronJob creates Jobs, which create Pods
- `backoffLimit` controls retry attempts
- `schedule` uses standard cron syntax
- `concurrencyPolicy` controls overlapping executions
- Use Job for one-time tasks and CronJob for recurring tasks