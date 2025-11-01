# Microservices & Distributed System Basics

## What Are Microservices?

Microservices architecture designs an application as a group of small and independent services. Each service focuses on one business function.

Each service:

* Runs on its own
* Is deployed separately
* Can be scaled without touching other services
* Talks to other services using APIs

---

## Monolithic Architecture

A monolithic application is built as a single large system where everything is tightly connected.

Characteristics:

* Frontend, backend, and business logic are in one place
* One deployment updates the whole application
* If one part fails, it can affect the entire system

### Drawbacks

* Hard to scale only one feature
* Slower release cycles
* Becomes difficult to manage as it grows

---

## Moving from Monolith to Microservices

With microservices:

* The application is split into many small services
* Each service handles one responsibility
* Services communicate using REST APIs or gRPC

### Example: E-Commerce System

* User Service handles user data
* Order Service handles orders
* Payment Service handles payments

All services run independently but communicate through APIs.

---

## DNS (Domain Name System)

### What Is DNS?

DNS converts website names into IP addresses.

Example:
google.com → 142.250.183.68

### Why DNS Is Important

* People remember names, computers use numbers
* Helps with:

  * Easy website access
  * Load balancing
  * High availability
  * Changing infrastructure without affecting users

---

## Load Balancer

After DNS resolves a domain, requests usually go through a load balancer.

### What It Does

* Spreads traffic across servers
* Prevents any single server from being overloaded
* Improves system reliability and speed

### Load Balancing Methods

1. Path-Based Routing
   /users → User Service
   /orders → Order Service

2. Instance-Based Routing
   Traffic is shared among multiple copies of the same service

---

## Authentication vs Authorization

| Term           | Meaning                      | Example                    |
| -------------- | ---------------------------- | -------------------------- |
| Authentication | Confirms who you are         | Logging in                 |
| Authorization  | Confirms what you can access | Accessing a protected page |

Authentication always happens before authorization.

### Authorization Models

* ACL (Access Control List)
* RBAC (Role-Based Access Control)
* Policy-based access

---

## Database per Service Rule

* Each microservice has its own database
* Databases are not shared
* A service can use more than one database internally

### Advantages

* Loose coupling
* Clear ownership of data
* Services can scale and change schemas independently

---

## Service Discovery

### What Is Service Discovery?

A way for services to find each other automatically without fixed IP addresses.

### Why It Is Needed

In container-based systems:

* Services start and stop often
* IP addresses change

### How It Works

1. Service registers when it starts
2. Registry stores its location
3. Other services query the registry

### Common Tools

* Consul
* Eureka
* etcd
* Apache Zookeeper
* Kubernetes (DNS-based discovery)

---

## Microservice Communication

### Synchronous Communication

* Request and response
* Caller waits for a reply

Technologies: REST, gRPC
Best For: Login, payment confirmation

Pros:

* Simple
* Immediate result

Cons:

* Tight coupling
* Risk of chain failures

---

### Asynchronous Communication

* Non-blocking
* Uses message brokers

Technologies: Kafka, RabbitMQ, AWS SQS
Best For: Notifications, background jobs

Pros:

* Scales well
* Fault tolerant
* Loose coupling

Cons:

* More complex
* No immediate response

---

### Asynchronous Patterns

1. Queue-Based
   One consumer handles each message

2. Publish–Subscribe
   Many consumers receive the same event

Load balancers are not required because the message broker handles distribution.

---

## Event Sinking and Observability

Event sinking means collecting logs, metrics, and traces from services.

Used for:

* Debugging
* Performance monitoring
* Auditing

Common Tools:

* ELK Stack
* Prometheus
* Grafana

---

## The 12-Factor App Method

A set of guidelines for building scalable and cloud-friendly applications.

### The 12 Principles

1. One codebase in version control
2. Manage dependencies explicitly
3. Store config in environment variables
4. Treat external services as attached resources
5. Separate build, release, and run
6. Use stateless processes
7. Expose services through ports
8. Scale with multiple processes
9. Fast startup and graceful shutdown
10. Keep dev and prod similar
11. Send logs as event streams
12. Run admin tasks as one-off jobs

---

## Why This Is Important

Following these ideas leads to:

* Scalable systems
* Easier maintenance
* Faster releases
* Microservices ready for cloud environments
