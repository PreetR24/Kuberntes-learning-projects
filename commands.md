# version testing

```bash
kind version
```

```bash
kubectl version
```

---

# Cluster

### creating cluster
```bash
kind create cluster --name tws-cluster --config config.yml
```
   
### cluster info
```bash
kubectl cluster-info 
kubectl cluster-info --context kind-tws-cluster    
```       

### setting default context 
```bash
kubectl config use-context kind-tws-cluster    
```

---

# General

### Get all the things
```bash
kubectl get all
```

### Steps to start any project in k8s
```bash
Image -> Dockerhub -> Namespace -> Deployment -> Service -> Port forwarding
```

---

# Nodes

### getting all the nodes
```bash
kubectl get nodes
```

### checking all the images inside a node
```bash
docker exec tws-cluster-worker crictl images
```

---

# Pods

### getting pods of default namespace
```bash
kubectl get pods
```

### getting pods with namespace
```bash
kubectl get pods -n kube-system
```

### getting pods with node details
```bash
kubectl get pods -o wide -n nginx
```

### running a pod by its image
```bash
kubectl run nginx --image=nginx   
```

### running a pod by its image inside a namespace
```bash
kubectl run nginx --image=nginx -n nginx
```

### deleting a pod
```bash
kubectl delete pod nginx
```

### entering inside a pod
```bash
kubectl exec -it nginx-pod -n nginx -- bash
```

### describing each metadata of a pod
```bash
kubectl describe pod/nginx-pod -n nginx
```

### getting the logs of a pod (done using echo via sh, like done in job.yml)
```bash
kubectl logs pod/demo-job-ptfb6 -n nginx
```

---

# Namesapce

### getting all the namespace
```bash
kubectl get ns
```

### creating a new namespace
```bash
kubectl create ns nginx
```

### deleting a namespace
```bash
kubectl delete ns nginx
```
 
---

# Files

### applying the changes from yml file
```bash
kubectl apply -f <filename.yml>
```

---

# Deployment

### getting all the deployments
```bash
kubectl get deployment
```

### creating a new deployment
```bash
kubectl apply -f deployment.yml
```

### scaling the number of pods in deployment
```bash
kubectl scale deployment/nginx-deployment -n nginx --replicas=3
```

### rolling updates example by changing image
```bash
kubectl set image deployment/nginx-deployment -n nginx nginx=nginx:1.27.3
```

### deleting a deployment
```bash
kubectl delete -f deployment.yml
```

--- 

# ReplicaSets

### getting all the replicasets
```bash
kubectl get replicasets
```

### creating a new replicaset
```bash
kubectl apply -f replicaseta.yml
```

### scaling the number of pods in replicaset
```bash
kubectl scale replicaset/nginx-replicasets -n nginx --replicas=3
```

### deleting a replicasets
```bash
kubectl delete -f replicasets.yml
```

---

# DaemonSets

### Points to remember
- Ensures that in each node, atmost 1 pod must run
- If any node already has a pod, it will not create another pod in that node
- For each eligible node: Does this node already have my Pod?
    - If Yes ->  Do nothing
    - If No -> Create one Pod

### getting all the daemonsets
```bash
kubectl get daemonsets
```

### creating a new daemonset
```bash
kubectl apply -f daemonsets.yml
```

### deleting a daemonsets
```bash
kubectl delete -f daemonsets.yml
```

---

# Job

### Points to remember
- To again run the job, we need to delete and again apply the yml file

### getting all the jobs
```bash
kubectl get job
```

### creating a new job
```bash
kubectl apply -f job.yml
```

### deleting a job
```bash
kubectl delete -f job.yml
```

---

# CronJob

### Points to remember
- Schedule format is " a b c d e "
    - a : minute ( 0 - 59 )
    - b : hour ( 0 - 23 )
    - c : day of month ( 1 - 31 )
    - d : month ( 1 – 12 )
    - e : day of week ( 0 – 7 ) (0 or 7 = Sunday)

### getting all the cron-jobs
```bash
kubectl get cronjob
```

### creating a new cron-job
```bash
kubectl apply -f cron-job.yml
```

### deleting a job
```bash
kubectl delete -f cron-job.yml
```

---

# PersistentVolume

### Points to remember

AccessModes
- ReadWriteOnce (RWO) → One Node can read/write.
- ReadOnlyMany (ROX) → Many Nodes can read only.
- ReadWriteMany (RWX) → Many Nodes can read/write.
- ReadWriteOncePod (RWOP) → One Pod only can read/write.

### getting all the PersistentVolumes
```bash
kubectl get pv
```

### creating a new PersistentVolume
```bash
kubectl apply -f persistentVolume.yml
```

### deleting a PersistentVolume
```bash
kubectl delete pv/local-pv
```

---

# PersistentVolumeClaim

### Points to remember
- A PersistentVolume can be bound to only one PersistentVolumeClaim at a time. Even if the PVC requests less storage than the PV's capacity, the entire PV is reserved for that single PVC. Any unused space remains available only to that PVC and cannot be allocated to another PVC.

- 1. Create appropriately sized PVs (Static Provisioning)
- 2. Dynamic Provisioning 
    - PV's are created based on the need of PVC's, and not prior to PVC

- In static provisioning, storageClassName is primarily used as a matching criterion between a PV and a PVC. In dynamic provisioning, it refers to a StorageClass resource, which defines how and where Kubernetes should automatically provision storage (for example, using AWS EBS, Azure Disk, or NFS).

### getting all the PersistentVolumeClaims
```bash
kubectl get pvc
```

### creating a new PersistentVolumeClaim
```bash
kubectl apply -f persistentVolumeClaim.yml
```

### deleting a PersistentVolumeClaim
```bash
kubectl delete pvc/local-pvc
```

---

# Service

### getting all the services
```bash
kubectl get service
```

### creating a new service
```bash
kubectl apply -f service.yml
```

### deleting a service
```bash
kubectl delete service/nginx-service
```

### Port forwarding in local machine
```bash
kubectl port-forward service/nginx-service -n nginx 81:80 --address=0.0.0.0
```

---

# Ingress

### getting all the ingress
```bash
kubectl get ing
```

### creating a new ingress
```bash
kubectl apply -f ingress.yml
```

### deleting a ingress
```bash
kubectl delete ingress/nginx-notes-ingress
```

---

# StatefulSet

### getting all the statefulset
```bash
kubectl get statefulset
```

### creating a new statefulset
```bash
kubectl apply -f statefulset.yml
```

### deleting a statefulset
```bash
kubectl delete statefulset/mysql-statefulset
```

---

# ConfigMap

### getting all the configmap
```bash
kubectl get configmap
```

### creating a new configmap
```bash
kubectl apply -f configmap.yml
```

### deleting a configmap
```bash
kubectl delete configmap/mysql-configmap
```

---

# Secrets

### getting all the secret
```bash
kubectl get secret
```

### creating a new secret
```bash
kubectl apply -f secret.yml
```

### deleting a secret
```bash
kubectl delete secret/mysql-secret
```

---

# Metrics Server

### Downloading from github
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

---

# Horiontal Pod Autoscaling

### getting all the hpa
```bash
kubectl get hpa
```

### creating a new hpa
```bash
kubectl apply -f hpa.yml
```

### deleting a hpa
```bash
kubectl delete -f hpa.yml
```

---

# Autoscaler

git clone https://github.com/kubernetes/autoscaler.git

Inside git bash 
```bash
cd autoscaler/vertical-pod-autoscaler
MSYS_NO_PATHCONV=1 ./hack/vpa-up.sh
```

---

# Vertical Pod Autoscaling

### getting all the vpa
```bash
kubectl get vpa
```

### creating a new vpa
```bash
kubectl apply -f vpa.yml
```

### deleting a vpa
```bash
kubectl delete -f vpa.yml
```

---

# RBAC (Important Commands)

### To apply cluster level role and cluster role bindings, file to be downloaded
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

### Get all Roles
```bash
kubectl get roles
```

### Get all RoleBindings
```bash
kubectl get rolebindings
```

### Get all ClusterRoles
```bash
kubectl get clusterroles
```

### Get all ClusterRoleBindings
```bash
kubectl get clusterrolebindings
```

---

### Create RBAC resources
```bash
kubectl apply -f role.yml
```

### Delete RBAC resources
```bash
kubectl delete -f role.yml
```

---

### Check if you have permission
```bash
kubectl auth can-i <verb> <resource>
```

Example:
```bash
kubectl auth can-i delete pods
```

---

### Check another user's permission
```bash
kubectl auth can-i <verb> <resource> --as=<username>
```

Example:
```bash
kubectl auth can-i get pods --as=john
```

---

# ServiceAccount

### Get all ServiceAccounts
```bash
kubectl get serviceaccounts
```

### Describe a ServiceAccount
```bash
kubectl describe serviceaccount <serviceaccount-name>
```

### Create a ServiceAccount
```bash
kubectl apply -f service-account.yml
```

### Delete a ServiceAccount
```bash
kubectl delete -f service-account.yml
```

---

# RoleBinding

### Get all RoleBindings
```bash
kubectl get rolebindings
```

### Describe a RoleBinding
```bash
kubectl describe rolebinding <rolebinding-name> -n <namespace>
```

### Create a RoleBinding
```bash
kubectl apply -f rolebinding.yml
```

### Delete a RoleBinding
```bash
kubectl delete -f rolebinding.yml
```


---

# Kubernetes Dashboard

### Deploy the Dashboard Admin User (ServiceAccount + RoleBinding)
```bash
kubectl apply -f dashboard-admin-user.yml
```

### Generate Login Token
```bash
kubectl -n kubernetes-dashboard create token admin-user
```

### Start Kubernetes Proxy
```bash
kubectl proxy --address=0.0.0.0 --accept-hosts='.*'
```

### Open Kubernetes Dashboard
```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

---

# Custom Resource Definition (CRD)

### Get all CRDs
```bash
kubectl get crd
```

### Describe a CRD
```bash
kubectl describe crd <crd-name>
```

### Create a CRD
```bash
kubectl apply -f crd.yml
```

### Delete a CRD
```bash
kubectl delete -f crd.yml
```

---

# Custom Resources

### Get all Custom Resources
```bash
kubectl get <resource-name>
```

Example:
```bash
kubectl get databases
```

### Describe a Custom Resource
```bash
kubectl describe <resource-name> <resource-instance>
```

Example:
```bash
kubectl describe database mysql-prod
```

### Create a Custom Resource
```bash
kubectl apply -f custom-resource.yml
```

### Delete a Custom Resource
```bash
kubectl delete -f custom-resource.yml
```