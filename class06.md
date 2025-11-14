# System Virtualization & Containerization

## 1. Bare Metal Architecture

### Concept

Bare metal architecture means applications run **directly on physical hardware** with no virtualization layer in between.

### Stack Layers

1. **Hardware** – CPU, memory, storage, network, I/O devices
2. **OS Kernel** – Manages processes, memory, and drivers
3. **System Libraries** – Shared libraries used by programs
4. **System Binaries** – Core operating system commands
5. **Application** – User-level software

### Main Characteristics

* No hypervisor
* Best performance
* Only one OS runs at a time
* Less flexible

---

## 2. Hypervisor-Based Virtualization

### What Is a Hypervisor?

A hypervisor is software that allows **multiple virtual machines (VMs)** to run on a single physical system by virtualizing hardware.

Simply put, it divides one physical machine into many virtual computers.

---

## Types of Hypervisors

### Type 1 – Bare Metal Hypervisor

* Installed directly on hardware
* No host OS required
* High performance

Examples:

* VMware ESXi
* Microsoft Hyper-V
* Xen

### Type 2 – Hosted Hypervisor

* Runs on top of a host OS
* Installed like a regular application

Examples:

* VirtualBox
* VMware Workstation

---

## How Hypervisors Run Virtual Machines

Each VM gets:

* Virtual CPU
* Virtual memory
* Virtual disk
* Virtual network card

Each VM runs:

* Its own operating system
* Its own kernel

This allows different operating systems to run at the same time on one machine.

---

## Why Virtual Machines Use More Resources

Every VM performs a **full operating system boot**, which includes:

* BIOS startup
* Bootloader
* Kernel loading
* Starting services

These steps happen separately for every VM.

---

## 3. Virtual Machine Boot Process

### Boot Steps

#### 1. BIOS

* First program to run at startup
* Checks hardware
* Selects boot device

#### 2. MBR (Master Boot Record)

* First 512 bytes of disk
* Contains partition info and boot code

#### 3. Bootloader

* Loads OS into memory

Examples:

* GRUB (Linux)
* Windows Boot Manager

#### 4. Kernel Initialization

* Starts the OS core
* Handles memory, processes, and hardware

#### 5. Init Process

* First user-space process
* Starts system services

Examples:

* systemd
* init

#### 6. Running Processes

* System services
* User applications
* Background tasks

### Conclusion

Each VM repeats this full process, which makes virtualization heavy and slower.

---

## 4. Containers vs Virtual Machines

### Docker Does Not Include

* BIOS
* Bootloader
* Separate kernel
* Full operating system

### Docker Architecture

```
Hardware → Host OS Kernel → Docker Engine → Containers
```

Containers share the **host kernel**, so there is no OS duplication.

---

## 5. Container Startup Flow

When running:

```bash
docker run nginx
```

Docker:

1. Uses the host kernel
2. Creates an isolated filesystem
3. Loads required libraries and binaries
4. Starts the application directly

Startup usually takes **milliseconds**.

---

## 6. Why Containers Are Lightweight

* No OS boot
* One shared kernel
* Very little overhead
* Smaller images
* Fast start and stop

---

## 7. Docker Execution Flow

1. User runs `docker run`
2. Client sends request to daemon
3. Daemon checks for image locally
4. Pulls image if missing
5. Creates container with isolated resources
6. Starts the application

---

## 8. Docker Architecture Components

### Docker Client

Command-line tool:

* docker run
* docker build
* docker pull
* docker ps

Talks to the Docker daemon using REST API.

### Docker Daemon (dockerd)

Handles:

* Images
* Containers
* Networking
* Volumes

### Docker Host

Stores:

* Images
* Containers
* Networks
* Storage

### Docker Registry

Stores images.

Examples:

* Docker Hub
* Private registries

---

## Images vs Containers

* **Image**: Read-only template
* **Container**: Running instance of an image
* One image can create many containers

---

## 9. Docker Installation

```bash
sudo su
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
docker run hello-world
```

---

## 10. Common Docker Commands

### Show Containers

```bash
docker ps
docker ps -a
```

### Run Containers

```bash
docker run nginx
docker run -d nginx
```

### Inspect Container

```bash
docker inspect <container-id>
```

### Open Container Shell

```bash
docker exec -it <container-id> bash
```

### Create Interactive Container

```bash
docker run -it ubuntu bash
```

### Start Stopped Container

```bash
docker start <container-id>
docker exec -it <container-id> bash
```

---

## Docker Registry Actions

### Login

```bash
docker login -u <username>
```

### Create Image from Container

```bash
docker commit -m "message" <container-id> <username>/<image-name>
```

### Push Image

```bash
docker push <username>/<image-name>
```

### Delete Image

```bash
docker rmi <image-id>
```
