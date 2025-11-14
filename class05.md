# Docker Fundamentals on AWS EC2

## 1. Connecting to an AWS EC2 Instance Using SSH

After launching an EC2 instance, you must connect to it using SSH.

### Steps

1. Open the AWS Management Console and go to **EC2 → Instances**.
2. Select your instance and click **Connect**.
3. Choose **SSH Client**.
4. Copy the SSH command shown by AWS (it contains the DNS name and key file).
5. On your local machine, move to the folder that has your `.pem` key file:

```bash
cd /path/to/pem-file
```

6. Run the SSH command:

```bash
ssh -i your-key.pem ubuntu@ec2-xx-xx-xx-xx.compute.amazonaws.com
```

7. You are now connected to the Ubuntu EC2 instance.

---

## 2. Running Your First Docker Container

To verify Docker is working, run:

```bash
docker run hello-world
```

If Docker is not installed, this command will fail.
If Docker is installed, it will:

* Download the image if missing
* Create a container
* Run the container
* Show a success message

Example output:

```
Unable to find image 'hello-world:latest' locally
...
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

---

## 3. What `docker run` Means

Every time you run:

```bash
docker run <image>
```

Docker creates a **new container**, even if the image already exists.

---

## 4. Docker Images

* Containers are created from images.
* Images are reusable templates.
* Simple analogy:

  * Image = Class
  * Container = Object
* Images can come from:

  * Local machine
  * Docker Hub

If the image is not found locally, Docker pulls it from Docker Hub automatically.

---

## 5. Docker Containers

* Containers are lightweight environments where applications run.
* They are isolated but share the host OS kernel.
* Each container has:

  * Its own filesystem
  * Its own processes

Because of this, containers are faster and use fewer resources than virtual machines.

---

## 6. Docker Client and Docker Daemon

### Docker Client

* Command-line tool (`docker`)
* Used to send commands
* Talks to the Docker daemon

### Docker Daemon

* Background service on the host machine
* Responsible for:

  * Pulling images
  * Creating containers
  * Starting and stopping containers
  * Managing containers

Execution flow:

```
Client → Daemon → Image Pull → Container Creation → Container Run
```

---

## 7. Linux Daemons and Docker Service

In Linux, background services are called **daemons**.

Check all services:

```bash
systemctl status
```

Check Docker service:

```bash
systemctl status docker
```

---

## 8. Docker Installation Components

Installing Docker gives:

1. Docker Client
2. Docker Daemon

Verify installation:

```bash
docker version
```

---

## 9. Entering a Container Shell

To start a container and open its shell:

```bash
docker run -it ubuntu bash
```

### Flags

* `-i` → Interactive mode
* `-t` → Terminal access
* `ubuntu` → Image name
* `bash` → Shell inside container

Inside the container, you will see:

```
root@container-id:/#
```

---

## 10. Viewing Processes Inside a Container

Inside the container, run:

```bash
ps -ef
```

This shows only processes running inside that container.

---

## Docker Execution Flow (Summary)

1. User runs `docker run`.
2. Client sends request to daemon.
3. Daemon checks for image locally.
4. If missing, image is downloaded.
5. Container is created.
6. Container runs.
7. Output is shown in terminal.

---

## Key Points

* Images are templates.
* Containers are running instances.
* `docker run` always creates a new container.
* Docker uses client–daemon architecture.
* Containers provide isolated environments for applications.
