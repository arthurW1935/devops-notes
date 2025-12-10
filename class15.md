# Class 15 — Kubernetes Services

---

## PART 1 — Conceptual Notes

### What Is a Kubernetes Service?

A **Kubernetes Service** is an abstraction that gives **stable networking and load balancing** for a changing set of Pods.

In simple terms, a Service acts like:

* A **load balancer**
* A **service discovery mechanism**

Any load balancer must provide:

1. **Traffic distribution**

   * Requests are spread across multiple instances.
2. **Backend health awareness**

   * Automatically adapts when instances start, stop, or fail.

Kubernetes Services provide both.

---

### Example Scenario (Blue Service → Red Service)

Assume:

* A cluster with **three nodes**
* Node 1 runs a **Blue service**
* Node 2 runs a **Red service**
* Blue needs to talk to Red

#### Can Blue directly talk to a Red Pod?

Yes, using the Pod IP.

#### Should it?

No.

#### Reasons

* Pod IPs are **temporary**
* All traffic may hit a single Pod
* No built-in load balancing
* If the Pod crashes, communication breaks

---

### Why a Load Balancer Is Needed

Better approach:

* Blue sends traffic to a **load balancer**
* Load balancer distributes traffic to **multiple Red Pods**
* Kubernetes updates routing automatically when Pods:

  * Start
  * Stop
  * Become unhealthy

In Kubernetes, this abstraction is called a **Service**.

---

### ClusterIP — Default Service Type

* **ClusterIP** is the default
* Provides:

  * Stable virtual IP
  * DNS name
  * Internal load balancing

Each ClusterIP Service has:

* One virtual IP
* One or more ports

Pods expose application ports called **targetPorts**.
The Service forwards traffic to these ports.

---

### Why Direct Pod IP Usage Is Bad

Problems:

1. Pod IPs change
2. No load distribution
3. No failover
4. Single-Pod bottlenecks

Services fix all of these.

---

### Service Ports

* **port** → Port exposed by the Service
* **targetPort** → Port inside the container

Flow:

```
Client → ServiceIP:port → PodIP:targetPort
```

---

### Common kubectl Commands

```bash
kubectl get svc
```

List Services.

```bash
kubectl create ns batch2027
kubectl get ns
```

Create and list namespaces.

```bash
kubectl run mypod --image=nginx -n batch2027
```

Create Pod in a namespace.

```bash
kubectl get pods -n batch2027 -o wide
```

Show Pod IPs and nodes.

```bash
curl <pod-ip>
```

Test direct Pod access (learning purpose).

---

### Types of Kubernetes Services

1. ClusterIP
2. NodePort
3. LoadBalancer
4. ExternalName

---

### NodePort Service

Problem:

How can external users reach a Service?

Solution:

Use **NodePort**.

How it works:

* Kubernetes opens a port in range **30000–32767**
* The port is opened on **all nodes**
* Traffic is forwarded internally

Flow:

```
External Client → NodeIP:NodePort → ClusterIP → Pod
```

Notes:

* Every NodePort Service also has a ClusterIP
* ClusterIP does internal load balancing

---

## PART 2 — Instructor-Level Notes

### Why Services Are Required

Pods are **temporary**:

* IPs change
* Scaling creates and deletes Pods
* Controllers recreate Pods often

So Pod IPs cannot be relied on.
A stable layer is needed, which is the **Service**.

---

### What Services Provide

* Stable virtual IP
* DNS-based discovery
* Built-in load balancing
* Works with kube-proxy
* Controlled internal or external access

---

### How a Service Works Internally

1. Service is created with a **selector**
2. Kubernetes finds matching Pods
3. kube-proxy programs rules using:

   * iptables or
   * IPVS
4. Traffic to Service IP is routed to Pods

---

### Service Types in Detail

#### 1. ClusterIP

For internal traffic only.

Use cases:

* Microservices
* Internal APIs
* Backend to database

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-svc
spec:
  type: ClusterIP
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 8080
```

Access inside cluster:

```bash
curl http://backend-svc
```

Full DNS:

```
backend-svc.default.svc.cluster.local
```

---

#### 2. NodePort

Exposes Service on each node.

Range:

```
30000–32767
```

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-nodeport
spec:
  type: NodePort
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 8080
      nodePort: 32080
```

Access:

```
http://<NodeIP>:32080
```

Limitations:

* Open on all nodes
* No TLS by default
* Not best for production

---

#### 3. LoadBalancer

Mostly used in cloud setups.

Flow:

```
Internet → Cloud Load Balancer → NodePort → ClusterIP → Pod
```

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: backend-lb
spec:
  type: LoadBalancer
  selector:
    app: backend
  ports:
    - port: 80
      targetPort: 8080
```

Check external IP:

```bash
kubectl get svc backend-lb
```

---

#### 4. ExternalName

Maps Service to external DNS.

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: external-db
spec:
  type: ExternalName
  externalName: database.company.com
```

No Pods or selectors.

---

### Service DNS Format

```
<service-name>.<namespace>.svc.cluster.local
```

Handled by CoreDNS.

---

### kube-proxy Role

* Runs on every node
* Programs networking rules
* Uses iptables or IPVS
* Does not directly proxy traffic

---

### Choosing Service Type

| Use Case                   | Service Type |
| -------------------------- | ------------ |
| Internal services          | ClusterIP    |
| Local or bare-metal access | NodePort     |
| Cloud production traffic   | LoadBalancer |
| External DNS mapping       | ExternalName |
| Direct Pod addressing      | Headless     |
