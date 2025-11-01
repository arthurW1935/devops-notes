
# CI/CD, Testing, and Deployment – DevOps Notes

## Understanding CI/CD

**CI/CD** refers to a set of DevOps practices that automate how software is **built, tested, and released**.

- **CI (Continuous Integration):** Focuses on integrating code changes frequently.
- **CD (Continuous Delivery / Continuous Deployment):** Focuses on reliably releasing software.

The main objective is to enable **fast, repeatable, and low-risk software delivery**.

---

## Continuous Integration (CI)

### Definition
Continuous Integration is a workflow where developers regularly push code to a shared repository, triggering **automated builds and tests** to verify correctness.

### Why CI Matters
- Identifies defects early
- Prevents integration conflicts
- Improves code quality
- Reduces manual effort
- Enables faster feedback to developers

### Standard CI Workflow
1. Developer commits code to a version control system.
2. CI server pulls the latest code.
3. Dependencies are installed automatically.
4. Application is built or compiled.
5. Code quality checks and linting are executed.
6. Unit tests are run.
7. Security scans (SAST) are performed.
8. Dependency vulnerability checks (SCA).
9. Application is packaged as a Docker image.
10. Build artifacts are pushed to an artifact repository.

**Primary Goal:** Catch issues as early as possible in the development lifecycle.

---

## Continuous Delivery (CD)

### Definition
Continuous Delivery ensures that software is always in a **deployable state**. All steps up to staging deployment are automated, but production deployment requires manual approval.

### CD Process
1. Retrieve build artifacts from the repository.
2. Deploy to SIT (System Integration Testing) environment.
3. Validate interaction between system components.
4. Execute performance tests.
5. Perform security testing using DAST.
6. Manual approval before production release.

**Key Idea:** Release readiness at all times.

---

## Continuous Deployment

Continuous Deployment extends Continuous Delivery by **automatically deploying** every successful build to production without human intervention.

### Benefits
- Faster feature releases
- Minimal deployment errors
- Immediate user feedback
- Easier rollback mechanisms

**Popular Tools:** Jenkins, GitHub Actions, GitLab CI/CD, ArgoCD, Spinnaker, CircleCI

---

## CI/CD Pipeline Flow

```
Source Code Repository
        |
        v
Continuous Integration
 - Build
 - Unit Tests
 - Static Analysis
 - Security Scans
 - Docker Image Creation
        |
        v
Continuous Delivery
 - Deploy to SIT
 - Integration Testing
 - Performance Testing
 - DAST
        |
        v
Continuous Deployment
 - Production Release
 - Monitoring and Alerts
```

---

## Testing Strategies in CI/CD

| Test Category | Objective | Tools |
|--------------|-----------|-------|
| Unit Testing | Validate individual units of code | JUnit, pytest |
| Integration Testing | Verify module interactions | Postman, REST Assured |
| System Testing | End-to-end validation | Selenium, Cypress |
| Load & Performance | Measure performance under stress | JMeter, Locust |
| Security Testing | Identify vulnerabilities | OWASP ZAP |
| SAST | Analyze source code security | SonarQube |
| DAST | Analyze running application | OWASP ZAP |
| SCA | Dependency vulnerability scan | Black Duck, Xray |

---

## Unit Testing and Mocking

### Unit Testing
Unit testing focuses on verifying **small, isolated pieces of logic** such as functions or classes.

### Mocking Concept
Mocking replaces dependent components with simulated versions.

Example:
- While testing function `A`, dependent function `B` is mocked.
- Assumption: `B` works correctly.
- Focus remains on testing `A` only.

### Mocking Frameworks
- Java: Mockito
- Python: unittest.mock
- JavaScript: Sinon.js

---

# Linux Fundamentals

## Historical Background

| Period | System | Description |
|------|--------|-------------|
| 1969–1990 | UNIX | Multi-user OS developed at Bell Labs |
| 1985–1995 | Windows | GUI-based OS for personal computers |
| 1991 | Linux | Open-source kernel by Linus Torvalds |

Linux dominates modern server infrastructure worldwide.

---

## What is Linux?
Linux is an **open-source operating system kernel** that manages communication between software and hardware.

### Kernel Responsibilities
- Process scheduling
- Memory management
- File system handling
- Device and I/O control
- Security enforcement

### Kernel Models
- Monolithic Kernel: Services run in kernel space
- Microkernel: Minimal kernel with user-space services

---

## Key Linux Components

| Component | Role |
|---------|------|
| Shell | Command-line interface |
| File System | Hierarchical structure starting at / |
| Process Manager | Handles running programs |
| User Management | Controls permissions and access |

---

# AWS IP Address Types

AWS EC2 instances support multiple IP address types:

| IP Type | Details |
|-------|---------|
| Private IP | Used internally within a VPC |
| Public IP | Temporary public address |
| Elastic IP | Static public IP for long-term use |

Private IPs enable internal networking, while public and elastic IPs allow internet access.