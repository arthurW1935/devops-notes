# Class 14 — Kubernetes Deployment Deep Dive

---

## Section 1 — Study Notes

### Understanding Deployments

A **Deployment** is a Kubernetes controller used to manage applications in a production-safe manner.

Main ideas:

* A Deployment **creates and manages ReplicaSets**
* ReplicaSets **create and manage Pods**
* Pods **run containers**

Hierarchy:

```
Deployment
 └─ ReplicaSet
     └─ Pod
         └─ Container
```

In practice, Pods are not managed directly.
You define the desired state in a Deployment, and Kubernetes keeps the system in that state.

---

### Deployment vs ReplicaSet

Deployment and ReplicaSet YAML files look almost the same.

Main difference:

```yaml
kind: ReplicaSet
```

vs

```yaml
kind: Deployment
```

Reason:

* Deployment acts as a **wrapper over ReplicaSet**
* It adds features such as:

  * rolling updates
  * rollback
  * revision history
  * pause and resume

ReplicaSets alone cannot manage versioned updates.

---

### Reliability Inheritance

#### Containers Provide

* Lightweight execution
* Fast startup
* Process isolation

⬇

#### Pods Add

* Automatic container restarts
* Shared network and storage

⬇

#### ReplicaSets Add

* Fixed number of Pods
* Pod recreation on failure
* Pod movement when a node fails
* Horizontal scaling

⬇

#### Deployments Add

* Everything above
* Plus:

  * controlled rolling updates
  * rollbacks
  * version tracking
  * declarative management

---

### Why Deployments Matter

* Kubelet handles container crashes
* ReplicaSets handle Pod crashes
* Higher-level controllers handle node failures

Deployments ensure:

* Desired state is maintained
* Failures are fixed automatically
* Applications stay available

---

## Section 2 — Cluster Initialization

### Environment

* One control-plane node
* One worker node

---

### Set Hostname for Control Plane

```bash
hostnamectl set-hostname controlplane
hostname
```

---

### Initialize Control Plane

```bash
kubeadm init
```

This command:

* Sets up control plane
* Starts:

  * API Server
  * Scheduler
  * Controller Manager
  * etcd
* Prints join command for worker nodes

---

### Check Nodes

```bash
kubectl get nodes
```

At first, nodes show **NotReady** because networking is missing.

---

### Check System Pods

```bash
kubectl get pods -n kube-system
```

Observation:

* CoreDNS is not running

Reason:

* No CNI installed yet

---

### Install CNI (Weave)

```bash
kubectl apply -f https://github.com/weaveworks/weave/release/download/v2.8.1/weave-daemonset-k8s.yaml
```

This:

* Installs Weave CNI
* Runs one network Pod per node

Monitor:

```bash
watch -n 1 kubectl get pods -n kube-system
```

Cluster is ready when CoreDNS is running.

---

## Section 3 — ReplicaSet Operations

### Create ReplicaSet

```bash
kubectl apply -f replica-set.yaml
```

---

### Scale ReplicaSet

#### Declarative Way

Edit YAML:

```yaml
replicas: 5
```

Apply:

```bash
kubectl apply -f replica-set.yaml
```

#### Imperative Way

```bash
kubectl scale rs <replicaset-name> --replicas=5
```

---

## Section 4 — Working with Deployments

### Create Deployment

```bash
kubectl apply -f deploy-ng.yaml
```

Watch resources:

```bash
watch -n 1 kubectl get deploy,rs,po
```

---

### Scale Deployment

```bash
kubectl scale deployment nginx-deployment --replicas=3
```

Flow:

Deployment → ReplicaSet → Pods

---

### Kubernetes Scaling Types

1. **Horizontal Pod Autoscaler (HPA)**

   * Changes number of Pods

2. **Vertical Pod Autoscaler (VPA)**

   * Changes CPU and memory values

3. **Cluster Autoscaler (CA)**

   * Adds or removes nodes

---

## Section 5 — Instructor Notes

### Purpose of Deployments

* Raw Pods are not self-healing
* ReplicaSets provide availability but not update strategy
* Deployments allow safe upgrades

---

### Deployment Internal Flow

1. YAML sent to API Server
2. Validation and checks
3. Stored in etcd
4. Deployment Controller creates ReplicaSet
5. ReplicaSet Controller creates Pods
6. Scheduler assigns nodes
7. Kubelet runs containers

---

### Example Deployment YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: app
        image: nginx:1.27
        ports:
        - containerPort: 80
```

---

### Rolling Update Strategy

Default:

```yaml
strategy:
  type: RollingUpdate
  rollingUpdate:
    maxUnavailable: 25%
    maxSurge: 25%
```

Alternative:

* **Recreate**

  * Stops all old Pods before starting new ones

---

### Update Deployment Image

```bash
kubectl set image deployment/webapp app=nginx:1.28
```

Effects:

* New ReplicaSet created
* Old ReplicaSet scaled down
* Revision number increased

---

### Rollout Commands

```bash
kubectl rollout status deployment/webapp
kubectl rollout history deployment/webapp
kubectl rollout undo deployment/webapp
kubectl rollout undo deployment/webapp --to-revision=2
```

---

### Pause and Resume Rollout

```bash
kubectl rollout pause deployment/webapp
kubectl rollout resume deployment/webapp
```

Used for controlled updates and troubleshooting.

---

### etcd Paths

Deployment:

```
/registry/deployments/default/webapp
```

ReplicaSet:

```
/registry/replicasets/default/webapp-xxxxx
```

Pod:

```
/registry/pods/default/webapp-xxxxx-yyyyy
```

Rollback points the Deployment back to an older ReplicaSet.

---

## Final Takeaways

* Pods run containers
* ReplicaSets keep Pods running
* Deployments manage updates and lifecycle
* Declarative YAML is preferred
* Kubernetes always reconciles desired vs actual state
