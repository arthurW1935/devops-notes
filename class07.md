# Docker Image Building & Dockerfile

## 1. Docker Image Overview

A Docker image is a **read-only template made of layers** that is used to create containers.

When you run:

```bash
docker run <image-name>
```

Docker starts a **container**, which is a running copy of that image.

Key points:

* Images are built from multiple layers.
* Each Dockerfile instruction creates one layer.
* Containers add a small writable layer on top of the image.

---

## 2. What Is a Dockerfile

A **Dockerfile** is a text file that contains ordered instructions for building a Docker image.

Docker reads these instructions one by one to create the final image.

---

## 3. FROM Instruction

### Purpose

`FROM` defines the **base image** used to build your image.

### Rules

* Required in every Dockerfile.
* Only `ARG` can appear before `FROM`.
* You can have multiple `FROM` lines (for multi-stage builds).

### Example

```dockerfile
FROM ubuntu:22.04
```

### Why the Base Image Is Important

The base image provides:

* OS environment
* Preinstalled tools
* Core dependencies

### Best Practices

* Use small images like alpine or slim.
* Avoid `latest`; always specify a version.

---

## 4. Building Docker Images

### With a custom Dockerfile name

```bash
docker build -t imagename -f CustomDockerfile .
```

### With default Dockerfile

```bash
docker build -t imagename .
```

Notes:

* Image names must be lowercase.
* `-t` assigns a name and tag.
* `.` is the build context.

---

## 5. What Happens During Image Build

When you run:

```bash
docker build -t myimage .
```

Docker:

1. Reads the Dockerfile.
2. Pulls base image if missing.
3. Runs instructions in order.
4. Creates a layer for each instruction.
5. Combines all layers into one image.

Docker stores images at:

```bash
/var/lib/docker
```

---

## 6. Build-Time vs Run-Time Instructions

Docker instructions run in two phases.

### Build-Time Instructions

Executed while creating the image:

* FROM
* RUN
* COPY
* ADD
* ENV
* WORKDIR

They define how the image is built.

### Run-Time Instructions

Executed when container starts:

* CMD
* ENTRYPOINT

They define what the container runs.

---

## 7. RUN Instruction

`RUN` executes commands **during image build**.

Examples:

```dockerfile
RUN apt-get update && apt-get install -y curl
RUN pip install flask
```

Best practices:

* Combine commands to reduce layers.
* Clean cache to keep image small.

```dockerfile
RUN apt-get update && apt-get install -y curl     && rm -rf /var/lib/apt/lists/*
```

---

## 8. CMD Instruction

`CMD` defines the **default command** for the container.

Example:

```dockerfile
CMD ["python", "app.py"]
```

Notes:

* Only the last CMD is used.
* CMD can be overridden when running the container.

```bash
docker run myimage ls
```

---

## 9. ENTRYPOINT Instruction

`ENTRYPOINT` sets the **main executable** of the container.

Example:

```dockerfile
ENTRYPOINT ["python", "app.py"]
```

Behavior:

* Always runs.
* Extra arguments are appended.

---

## 10. Using ENTRYPOINT with CMD

Example:

```dockerfile
ENTRYPOINT ["echo"]
CMD ["Hello World"]
```

Run:

```bash
docker run myimage
```

Output:

```text
Hello World
```

Override CMD:

```bash
docker run myimage Goodbye
```

Output:

```text
Goodbye
```

---

## 11. COPY vs ADD

| Feature               | COPY        | ADD           |
| --------------------- | ----------- | ------------- |
| Copy local files      | Yes         | Yes           |
| Download from URL     | No          | Yes           |
| Auto extract archives | No          | Yes           |
| Recommended           | General use | Special cases |

### COPY Example

```dockerfile
COPY requirements.txt /app/
COPY . /app
```

### ADD Example

```dockerfile
ADD app.tar.gz /app/
ADD https://example.com/file.tar.gz /tmp/
```

Best practice: Use `COPY` unless you need ADD features.

---

## 12. Commands Used in Practice

```bash
sudo su
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

git clone https://github.com/vilasvarghesescaler/docker-k8s/
cd docker-k8s/dockerfiles

cat Dockerfile
docker build -t myfrom .
docker images
docker run -it myfrom
docker build -t myrun -f 2.Runfile .
```

---

## 13. Sample Dockerfile Explained

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENTRYPOINT ["python"]
CMD ["app.py"]
```

Explanation:

* Uses lightweight Python image.
* Installs dependencies efficiently.
* ENTRYPOINT defines main program.
* CMD provides default argument.

---

## 14. Inspecting Docker Images

Show image layers:

```bash
docker history <image>
```

View image details:

```bash
docker inspect <image>
```

Check vulnerabilities:

```bash
docker scan <image>
```

---

## 15. Key Points

1. FROM sets base image.
2. RUN executes build-time commands.
3. COPY and ADD move files.
4. CMD sets default runtime command.
5. ENTRYPOINT defines main command.
6. ENTRYPOINT + CMD separate command and arguments.
7. docker build creates images.
8. docker run starts containers.
