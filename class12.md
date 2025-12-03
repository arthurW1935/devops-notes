# Deep Dive — Kubernetes Pods

## Part I — Conceptual Notes

### What Is a Pod?

**Short version:**
A Pod is a logical wrapper around one or more containers.

**Detailed version:**
A Pod is the smallest unit that Kubernetes can deploy and manage. It groups containers that must run together and closely cooperate. Containers inside a Pod:

* Share the same network namespace (same IP and localhost)
* Can communicate using IPC if configured
* Share mounted volumes for exchanging data
* Are always scheduled on the same node

**Common practice:**
Most Pods contain a single container.
Multiple containers in one Pod are mainly used for the *sidecar pattern*, where helper containers support the main app (logging, proxy, config reload, etc.).

**Key idea:**
Containers in a Pod are:

* **Co-scheduled** — placed together by the scheduler
* **Co-located** — run on the same node and share context

---

### Hands-on — Creating a Basic Cluster

Goal: create a cluster with one control-plane node and two worker nodes.

#### Step 1 — Initialize control plane

```bash
sudo kubeadm init
```

This command:

* Creates certificates and credentials
* Starts API server, scheduler, and controller manager
* Generates cluster config files
* Prints a `kubeadm join` command

Run this only on the control-plane node.

#### Step 2 — Regenerate join command

```bash
kubeadm token create --print-join-command
```

Use this if the original join command is lost or expired.

#### Step 3 — Check nodes

```bash
kubectl get nodes
```

Shows all nodes and their status.

---

### Networking — CNI Plugin

Kubernetes does not provide Pod networking by itself. A CNI plugin is required to:

* Assign Pod IPs
* Set up network namespaces and interfaces
* Allow Pod-to-Pod communication

Example with Weave Net:

```bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

This deploys a DaemonSet so every node runs a network agent.

Watch system Pods:

```bash
watch -n 1 kubectl get pods -n kube-system
```

---

### Checking System Services

Verify node services.

Check kubelet:

```bash
systemctl status kubelet
```

Check container runtime (example: CRI-O):

```bash
systemctl status crio
```

---

### Creating Pods

Quick Pod creation:

```bash
kubectl run testpod --image=nginx --restart=Never
```

Notes:

* Without `--restart=Never`, it may create a Deployment.
* YAML manifests are better for repeatable setups.

View Pods:

```bash
kubectl get pods -o wide
```

Shows Pod IPs and node names.

---

### Namespaces

Namespaces provide logical separation inside a cluster.

Default namespaces:

* `default`
* `kube-system`
* `kube-public`
* `kube-node-lease`

Commands:

```bash
kubectl get ns
kubectl create ns demo
kubectl get pods -n demo
```

---

## Part II — Internal Mechanics

### Pod Definition in etcd

A Pod object contains:

* `metadata.uid` — unique ID
* `metadata.resourceVersion` — versioning
* `spec` — desired state
* `status` — current state

Stored at:

```
/registry/pods/<namespace>/<pod-name>
```

---

### Pod Creation Flow

1. User applies Pod YAML.
2. API server authenticates and authorizes.
3. Admission controllers validate and modify.
4. Pod is saved in etcd.
5. Scheduler chooses a node.
6. Binding is sent to API server.
7. Kubelet on that node notices assignment.
8. Kubelet creates sandbox and containers.
9. Pod status is updated continuously.

---

### Scheduler Role

The scheduler:

* Filters unsuitable nodes
* Scores remaining nodes
* Applies priorities and preemption if needed

Then binds the Pod to the selected node.

---

### Kubelet Pod Startup Steps

1. Validate Pod spec
2. Prepare and mount volumes
3. Configure networking using CNI
4. Create pause container
5. Pull images
6. Start init containers, then app containers
7. Run lifecycle hooks
8. Report status

---

### Health Probes

* **Readiness probe** decides if Pod can receive traffic
* **Liveness probe** decides if container should be restarted

Readiness failure removes Pod from Service endpoints.
Liveness failure restarts the container.

---

### Pod States

Pod phases:

* Pending
* Running
* Succeeded
* Failed
* Unknown

Conditions give more details like `Ready` and `Initialized`.

---

### Debugging Basics

Useful commands:

```bash
kubectl describe pod <pod>
kubectl get pod <pod> -o yaml
kubectl logs <pod>
kubectl get events --sort-by='.lastTimestamp'
```

Common problems:

* `ImagePullBackOff` — bad image or credentials
* `CrashLoopBackOff` — app crash or probe failure
* `Pending` — no resources or taints
* `ContainerCreating` — CNI or volume issues

---

### Best Practices

* Do not use raw Pods in production
* Use Deployments or StatefulSets
* Always set resource requests and limits
* Configure readiness and liveness probes
* Use PodDisruptionBudgets for stability
