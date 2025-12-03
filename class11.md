# Kubernetes — Conceptual Overview 

Kubernetes is a **container orchestration system** used to run and manage containers at large scale.
It automates deployment, scheduling, scaling, networking, and lifecycle management of containerized applications across many machines.

---

## 1. Main Responsibilities of Kubernetes

Kubernetes works to keep applications in a **desired state** using automation.

### 1.1 Pod Scheduling and Resource Usage

* Containers are not deployed directly.
* Kubernetes deploys **Pods**, which contain one or more containers.
* The scheduler decides:

  * Which node runs a pod
  * How CPU and memory are used
  * How workloads are spread across nodes

### 1.2 Self-Healing and Reliability

* Kubernetes constantly checks pod health.
* If a pod crashes or becomes unhealthy:

  * It is recreated automatically
  * It may be started on another node

The goal is always:

**Desired State = Current State**

### 1.3 Scaling

Kubernetes provides different ways to scale:

1. **Horizontal Pod Autoscaler (HPA)**
   Increases or decreases the number of pods based on metrics like CPU or memory.

2. **Vertical Pod Autoscaler (VPA)**
   Changes CPU and memory requests/limits of existing pods.

3. **Cluster Autoscaler**
   Adds or removes worker nodes depending on demand.

### 1.4 Networking, Load Balancing, and Discovery

* Applications are exposed using **Services**.
* Services give:

  * Stable IP addresses
  * DNS-based discovery
* Traffic is automatically balanced across healthy pods.

### 1.5 Batch and Scheduled Work

* **Jobs** run one-time or finite tasks.
* **CronJobs** run tasks on a schedule, similar to cron in Linux.

### 1.6 Secrets and Configuration

* Kubernetes stores sensitive data such as:

  * Passwords
  * API keys
  * Certificates
* Secrets can be provided to pods using:

  * Environment variables
  * Mounted files

---

## 2. Kubernetes Cluster Architecture

A cluster has two major parts:

1. **Control Plane**
2. **Worker Nodes**

Important:

* Application pods run only on **worker nodes**.
* The control plane manages and coordinates everything.

---

## 3. Control Plane Components

### 3.1 API Server

The API Server is the central entry point.

Functions:

* Receives REST requests from kubectl and other clients
* Performs authentication and authorization
* Validates requests
* Writes and reads data from **etcd**
* Acts as the communication center for the cluster

All Kubernetes actions go through the API Server.

### 3.2 etcd

* Distributed key-value store
* Stores full cluster state:

  * Pods
  * Nodes
  * Deployments
  * Services
  * ConfigMaps and Secrets
* Considered the **source of truth**.

### 3.3 Scheduler

* Chooses which worker node should run a new pod
* Uses:

  * Available CPU and memory
  * Node labels, taints, and tolerations
  * Affinity rules
* Scheduler only selects the node; it does not start pods.

### 3.4 Controller Manager

* Runs controllers that watch cluster state
* Makes sure actual state matches desired state
* Examples:

  * Deployment controller
  * ReplicaSet controller
  * Node controller
  * Job controller
* Recreates or replaces resources when required.

---

## 4. Worker Node Components

Worker nodes execute application workloads.

### 4.1 Kubelet

* Agent running on every worker node
* Talks to API Server
* Tasks:

  * Create pods
  * Delete pods
  * Report health
* Uses CRI to talk to the container runtime.

### 4.2 Kube-Proxy

* Manages networking rules
* Uses iptables or IPVS
* Enables service-to-pod traffic
* Provides internal load balancing.

### 4.3 Container Runtime

* Runs containers inside pods
* Common runtimes:

  * containerd
  * CRI-O

---

## 5. Pod Creation Flow

1. User runs kubectl or applies YAML.
2. kubectl sends request to API Server.
3. API Server authenticates, authorizes, and validates.
4. Pod spec is stored in etcd.
5. Scheduler notices a pod without a node.
6. Scheduler selects a worker node.
7. API Server updates pod with node info.
8. Kubelet on that node sees the assignment.
9. Kubelet tells runtime to create containers.
10. Pod becomes running.
11. Controllers keep monitoring.

If a pod fails, it is recreated.

---

## 6. Pods Explained

* A **Pod** is the smallest unit in Kubernetes.
* A pod can have:

  * One container
  * Multiple closely related containers
* Containers in a pod share:

  * Network
  * Storage
  * IPC

They communicate using localhost.

Pods are temporary. Controllers like Deployments manage replicas.

---

## 7. Declarative Approach

Kubernetes uses declarative configuration.

Example:

```
replicas: 3
```

Means:

* Three pods must always exist.
* If one fails, Kubernetes creates another.
* If a node goes down, pods are moved automatically.

---

## 8. End-to-End Summary

1. User defines desired state.
2. API Server validates and stores it.
3. etcd saves cluster state.
4. Scheduler assigns pods.
5. Kubelet starts containers.
6. Controllers keep reconciling.
7. Cluster stays highly available and reliable.
