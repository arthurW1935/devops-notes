# Containers and Virtualization

## 1. What Is a Container?

A container is **not**:

* A virtual machine
* A small operating system
* An emulator

A container is best described as:

**A Linux process with isolation and resource limits**

More clearly, a container consists of:

* A normal Linux process
* Namespaces for isolation
* cgroups for limiting resources
* A layered temporary filesystem
* The host machine’s kernel

Main idea:
Containers do **not** start an operating system. They reuse the host kernel and isolate only what is needed.

---

## 2. Hypervisors vs Containers

### 2.1 Hypervisors

A hypervisor is software that allows multiple **virtual machines (VMs)** to run on one physical machine.

Each VM contains:

* A full operating system
* Its own kernel
* System libraries
* Root filesystem
* Boot components (BIOS, bootloader, init system)

A VM behaves like a separate physical computer.

---

### 2.2 Why Virtual Machines Are Heavy

Every VM needs:

1. A full OS installation
2. Dedicated memory
3. Its own kernel
4. A complete boot process
5. Virtualized hardware (CPU, disk, network, RAM)

Because of this:

* Startup is slow
* Resource usage is high

---

### 2.3 Virtual Machine Boot Steps

Each VM goes through these steps on its own:

1. **BIOS**

   * Hardware checks
   * Chooses boot device

2. **MBR**

   * Finds the partition to boot

3. **Bootloader**

   * Loads the kernel (for example, GRUB)

4. **Kernel Initialization**

   * Memory management
   * Scheduling
   * System calls
   * Device drivers

5. **Init System**

   * `systemd` or `init` starts services

6. **User Applications**

   * Your application finally runs

---

### 2.4 Why Containers Skip These Steps

Containers do **not** include:

* BIOS
* MBR
* Bootloader
* Separate kernel
* Separate operating system

Instead:

* The container directly starts its main process
* Startup happens in milliseconds

---

## 3. What Happens When You Run a Container

Command:

```
docker run nginx
```

### 3.1 Docker Execution Path

```
docker client
  → containerd
      → containerd-shim
          → runc
              → nginx process
```

---

### 3.2 What containerd Does

containerd:

* Prepares filesystem layers (OverlayFS)
* Creates namespaces (PID, NET, IPC, UTS, MNT, USER)
* Assigns cgroups
* Prepares root filesystem
* Starts containerd-shim

---

### 3.3 runc and Linux System Calls

runc uses Linux features such as:

* clone
* unshare
* pivot_root / chroot
* mount namespaces
* cgroup attachment

Then it runs the container command:

```
/usr/sbin/nginx
```

Inside the container, this process becomes **PID 1**.
On the host, it is just a normal process with a standard PID.

---

### 3.4 Role of containerd-shim

containerd-shim:

* Becomes the parent process of the container
* Keeps containers running even if containerd restarts
* Reports exit status

---

### 3.5 When a Container Stops

When PID 1 exits:

* shim notices the exit
* Namespaces are removed
* Writable filesystem layer is deleted
* cgroups are freed

The container no longer exists.

---

## 4. Temporary Nature of Containers

Containers use:

* Read-only image layers
* One temporary writable layer

When a container stops:

* Writable data is lost
* Logs and state disappear

To keep data, use:

* Docker volumes
* Bind mounts
* External storage

---

## 5. How Containers Achieve Isolation

Isolation is provided by **Linux namespaces**.

### Namespace Types

1. **PID Namespace**

   * Separate process tree

2. **Network Namespace**

   * Own IP, routes, firewall

3. **Mount Namespace**

   * Separate filesystem view

4. **UTS Namespace**

   * Separate hostname

5. **IPC Namespace**

   * Isolated shared memory

6. **USER Namespace**

   * Root inside container is not root on host

---

## 6. Containers vs Virtual Machines

### Virtual Machines

* Own kernel
* Full OS
* Hardware virtualization
* Strong isolation
* Slow startup

### Containers

* Shared kernel
* Process-level isolation
* Lightweight
* Fast startup
* Efficient use of resources

---

## 7. Port Mapping and Conflicts

Example:

```
docker run -d -p 80:80 nginx
```

Meaning:

* Host port 80 → Container port 80

If another container tries to use the same host port, it fails because:

* A host port can be used by only one service

Containers can reuse internal ports, but host ports must be unique.

---

## 8. Why Containers Are Lightweight

Containers are lightweight because:

* No kernel boot
* No OS startup
* No hardware emulation
* Very small overhead

They are simply isolated Linux processes.

---

## 9. Container Lifecycle (High Level)

1. runc starts the main process
2. containerd-shim monitors it
3. Process exits
4. Writable layer removed
5. Resources released
6. Container destroyed

---

## 10. Common Docker Commands

### Install Docker

```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo su
```

### Run Container

```
docker run -d -p 80:80 nginx
```

### Show Running Containers

```
docker ps
```

### Show All Containers

```
docker ps -a
```

---

## 11. Final Summary

* Containers are processes, not machines
* Isolation comes from namespaces
* Resource limits come from cgroups
* Docker does not start an OS
* Containers are fast and temporary
* Virtual machines offer stronger isolation but cost more resources
