# ReplicaSets and Pods — Kubernetes

## Part I — Cluster Setup and Pod Basics

### Cluster Topology

The cluster has:

* 1 Control Plane node
* 1 Worker node

The control plane manages cluster operations.
The worker node runs application Pods.

---

### Step 1: Connect to a Node Using SSH

```bash
ssh -i <key-name>.pem ubuntu@<public-ip>
```

**Purpose**: Connect from your local system to a cloud VM.
**Where to run**: Local terminal.
**Note**: Username depends on OS image (usually `ubuntu`).

---

### Step 2: Initialize the Control Plane

```bash
kubeadm init
```

**What it does**:

* Sets up control-plane components
* Generates certificates
* Starts API server, scheduler, and controller manager
* Creates admin kubeconfig

**Where**: On the control-plane node.

**Common options**:

* `--apiserver-advertise-address=<IP>`
* `--pod-network-cidr=<CIDR>`

After completion, a `kubeadm join` command is shown for worker nodes.

---

### Step 3: Generate Join Command Again

```bash
kubeadm token create --print-join-command
```

Used when the original join command is lost.
Run on the control-plane node.

---

### Step 4: Install CNI Plugin

```bash
kubectl apply -f https://github.com/weaveworks/weave/release/download/v2.8.1/weave-daemonset-k8s.yaml
```

**Why**:

* Enables Pod networking
* Assigns Pod IPs

**When**: After `kubeadm init`
**Other options**: Calico, Flannel, Cilium

---

### Step 5: Watch System Pods

```bash
watch -n 1 kubectl get pods -n kube-system
```

Used to observe CoreDNS, kube-proxy, and CNI Pods starting.

---

## Creating and Managing Pods

### Create Pod from CLI

```bash
kubectl run demo-nginx --image=nginx --restart=Never
```

Creates a single Pod.
`--restart=Never` forces Pod creation instead of Deployment.

---

### List Pods with Details

```bash
kubectl get pods -o wide
```

Shows Pod IP and node name.

---

### Delete All Pods in Namespace

```bash
kubectl delete pod --all
```

Deletes all Pods in current namespace.
If controlled by a controller, they are recreated.

---

### Check Container Runtime on Worker Node

```bash
ps -ef | grep -E "crio|runc|crun"
crictl ps
```

Used to see runtime processes and running containers.

---

### Failure Behavior

* **Container crash** → Kubelet restarts it on the same node.
* **Node failure** → Controllers recreate Pods on healthy nodes.

---

## Pod YAML Example

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      ports:
        - containerPort: 80
```

---

## YAML Basics

* Key-value pairs
* Lists
* Nested structures

---

## Part II — ReplicaSet Concepts

### What Is a ReplicaSet?

A ReplicaSet ensures a fixed number of identical Pods are always running.
It continuously checks actual state against desired state.

---

### ReplicaSet YAML Example

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx-rs
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx
```

**Important**:
Selector labels must match Pod template labels exactly.

---

### Control Plane Flow After Apply

1. kubectl sends request to API server
2. API server validates and stores in etcd
3. ReplicaSet controller sees replica difference
4. New Pods are created
5. Scheduler assigns nodes
6. Kubelet starts containers

---

### ReplicaSet Reconciliation

* If Pods < desired → create more
* If Pods > desired → delete extra

Runs continuously.

---

### Failure Handling

* Pod deleted → new Pod created
* Pod crashes → restarted or replaced
* Node down → Pods recreated on other nodes

---

### Deleting a ReplicaSet

```bash
kubectl delete rs nginx-rs
```

Deletes ReplicaSet and its Pods.

Create orphan Pods:

```bash
kubectl delete rs nginx-rs --cascade=orphan
```

---

### Ownership and Garbage Collection

Pods created by a ReplicaSet have `ownerReferences` pointing to the ReplicaSet.
This allows Kubernetes to track and clean up resources.

---

## Useful Commands

```bash
kubectl get rs
kubectl describe rs nginx-rs
kubectl get pods -l app=web
kubectl describe pod <pod-name>
kubectl get pod <pod-name> -o yaml
```

---

## Key Points

* Pod is the smallest unit
* ReplicaSet keeps Pod count correct
* Control plane coordinates using etcd
* ReplicaSets recover from failures
* YAML makes setups repeatable
