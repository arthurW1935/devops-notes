# DevOps Fundamentals

## Overview of DevOps

DevOps is a **practice, culture, and set of tools** that brings **development (Dev)** and **operations (Ops)** teams together.
The goal is to deliver software **faster, more reliably, and with better quality**.

**Definition:**
DevOps is a collaborative way of working where people, processes, and tools are combined to **continuously deliver software with speed and stability**.

---

## Benefits of DevOps

* Faster software releases
* Continuous feedback and improvement
* Better system stability and uptime
* Automation of repetitive work
* Strong teamwork between departments
* Faster recovery from failures

---

## Core DevOps Principles

1. **Shared Ownership** – Dev and Ops work as one team.
2. **Automation First** – CI/CD automates build, test, and deploy steps.
3. **Continuous Integration (CI)** – Code is merged frequently with automated tests.
4. **Continuous Delivery / Deployment (CD)** – Releases are automated and reliable.
5. **Monitoring and Feedback** – Systems are constantly observed and improved.

---

# Software Development Life Cycle (SDLC)

## SDLC Phases

1. **Requirement Gathering**

   * Understand business needs and limitations.
   * Define scope and expectations.

2. **System Design**

   * Create architecture and design plans.

3. **Implementation**

   * Write code based on the design.

4. **Testing (Quality Assurance)**

   * Run unit, integration, and system tests.

5. **Release and Deployment**

   * Push to production using methods like **Canary** or **Blue-Green** deployment.

6. **Monitoring and Support**

   * Track system health and fix issues after release.

---

# DevOps Lifecycle Mapped to SDLC

| Stage      | Purpose                           | Common Tools        |
| ---------- | --------------------------------- | ------------------- |
| Planning   | Define work, goals, and timelines | Jira, Asana         |
| Coding     | Write and version code            | Git, GitHub         |
| Building   | Compile and package software      | Maven, Gradle       |
| Testing    | Check quality and behavior        | JUnit, Selenium     |
| Release    | Prepare artifacts                 | Jenkins             |
| Deployment | Deploy applications               | Docker, Kubernetes  |
| Operations | Manage infrastructure             | Ansible, Puppet     |
| Monitoring | Track logs and metrics            | Prometheus, Grafana |

---

# DevSecOps: Adding Security Early

DevSecOps means **security is part of every stage of DevOps**, not a final step.
This is also known as **Shift-Left Security**.

## Security Techniques and Tools

* **Static Application Security Testing (SAST)**

  * Scans source code for security issues.
  * Tools: SonarQube, Checkmarx

* **Dynamic Application Security Testing (DAST)**

  * Tests running applications for vulnerabilities.
  * Tools: OWASP ZAP, Burp Suite

* **Software Composition Analysis (SCA)**

  * Finds risks in third-party libraries.
  * Tools: Black Duck, JFrog Xray

* **Code Linting**

  * Enforces coding rules and best practices.

* **Container Image Scanning**

  * Checks Docker images for vulnerabilities before release.

---

# Infrastructure as Code (IaC)

Infrastructure as Code lets teams **create and manage infrastructure using configuration files** instead of manual setup.

## Common IaC Tools

* Terraform – Works across cloud providers
* AWS CloudFormation – AWS-specific automation
* Ansible, Chef, Puppet – Configuration management
* Boto3 – Python library for AWS services

---

# Amazon VPC Basics

## What Is a VPC?

A **Virtual Private Cloud (VPC)** is a private network inside AWS where cloud resources are deployed securely.

## Main VPC Components

| Component              | Purpose                               |
| ---------------------- | ------------------------------------- |
| VPC                    | Private network                       |
| Subnet                 | Network section inside VPC            |
| Public Subnet          | Has internet access                   |
| Private Subnet         | No direct internet access             |
| Internet Gateway (IGW) | Connects VPC to internet              |
| NAT Gateway            | Outbound internet for private subnets |
| Route Table            | Defines traffic paths                 |
| Security Group         | Firewall for instances                |
| Network ACL            | Firewall for subnets                  |

**Layered Security:**

* Security Groups protect instances
* NACLs protect subnets
* Routing controls VPC-level access

---

# Sample VPC Architecture

**Important Points:**

* Resources in the same VPC can talk to each other.
* Public subnets use an Internet Gateway.
* Private subnets use NAT Gateways for outgoing traffic.

---

# Artifacts and Release Strategies

## Artifact Management

* Stores build outputs like binaries and container images.
* Examples: Nexus Repository, JFrog Artifactory.

## Deployment Methods

* **Blue-Green Deployment** – Two environments with instant switch.
* **Canary Deployment** – Slowly release to a small group first.
* **Rolling Deployment** – Update in steps without downtime.
