# Docker Volumes

## 1. Why Docker Volumes Are Needed

Docker containers are **temporary by nature**.

Each container has:

* Read-only **image layers**
* One thin **writable layer** on top

Any data written inside the container, such as:

```
/var/lib/mysql
/usr/share/nginx/html
/app/logs
```

is stored only in this writable layer.

If the container is **deleted**, this layer is removed and the data is lost.

Stopping and starting a container does **not** remove data, because the container still exists.
Deleting the container removes everything.

Docker volumes are used to keep data safe outside the container.

---

## 2. How Container Storage Works

When a container starts:

1. Image layers are mounted as read-only
2. A writable layer is added
3. All file changes go to this writable layer

This writable layer:

* Survives `docker stop` and `docker start`
* Is deleted when running `docker rm`

---

## 3. Common Confusion About Persistence

**Stop and start a container**

```
docker stop <container_id>
docker start <container_id>
```

Data stays.

**Remove a container**

```
docker rm <container_id>
```

All container data is lost.

Persistence depends on whether the container exists, not whether it is running.

---

## 4. What Is a Docker Volume?

A Docker volume is a **directory managed by Docker**.

Properties:

* Stored outside the container layer
* Not deleted when container is removed
* Can be reused by other containers
* Can be mounted into many containers

Docker stores volumes at:

```
/var/lib/docker/volumes/
```

---

## 5. Types of Storage in Docker

Docker provides four ways to mount storage:

1. Anonymous volumes
2. Named volumes
3. Bind mounts
4. tmpfs mounts

---

## 6. Anonymous Volumes

Created automatically without a name.

Example:

```
docker run -d -v /data nginx
```

Features:

* Docker assigns a random name
* Deleted when container is removed (unless reused)
* Not good for long-term data

---

## 7. Named Volumes

Created and managed explicitly.

Create volume:

```
docker volume create appdata
```

Use it:

```
docker run -it -v appdata:/data ubuntu bash
```

If the container is recreated using the same volume, the data remains.

Typical uses:

* Databases
* File uploads
* Persistent application storage

---

## 8. Bind Mounts

Bind mounts map a **host folder** into the container.

Syntax:

```
-v /host/path:/container/path
```

Example:

```
docker run -d   -v $(pwd)/site:/usr/share/nginx/html   -p 8080:80   nginx
```

Properties:

* Data lives on the host
* Changes are reflected instantly
* Powerful but risky in production

---

## 9. tmpfs Mounts

tmpfs stores data only in memory.

Example:

```
docker run -d --tmpfs /cache nginx
```

Features:

* No disk usage
* Very fast
* Data lost when container stops
* Good for secrets and temporary data

---

## 10. Comparison

| Type      | Persistent | Stored Where        | Best Use Case         |
| --------- | ---------- | ------------------- | --------------------- |
| Anonymous | Yes        | Docker volumes dir  | Temporary data        |
| Named     | Yes        | Named Docker volume | Databases, production |
| Bind      | Yes        | Host filesystem     | Development, configs  |
| tmpfs     | No         | RAM                 | Cache, sensitive data |

---

## 11. Where Data Is Stored

Docker volumes:

```
/var/lib/docker/volumes/
```

Bind mounts:

* Stored at the exact host path you choose

---

## 12. Practical Uses

### Named Volumes

* MySQL / PostgreSQL
* MongoDB
* Uploads
* Persistent services

### Bind Mounts

* Local development
* Config files
* Logs
* Certificates

### tmpfs

* Sessions
* Temporary secrets
* Fast in-memory storage

---

## 13. Checking Persistence

Create container:

```
docker run -it ubuntu bash
mkdir demo
echo "hello" > demo/file.txt
```

Stop and start:

```
docker stop <id>
docker start <id>
```

File still exists.

Remove container:

```
docker rm <id>
```

File is gone.

Use a volume:

```
docker run -it -v appdata:/persist ubuntu bash
```

Files in `/persist` remain even after container deletion.

---

## 14. Sharing Data Across Containers

Multiple containers can mount the same volume.

Example:

```
docker run -it -v shared:/data ubuntu bash
docker run -it -v shared:/data ubuntu bash
```

Both containers see the same files.

---

## 15. Example Commands

Anonymous volume:

```
docker run -d -v /data nginx
```

Named volume:

```
docker volume create dbvol
docker run -d -v dbvol:/var/lib/mysql mysql:8
```

Bind mount:

```
docker run -d -v $(pwd)/web:/usr/share/nginx/html nginx
```

tmpfs:

```
docker run -d --tmpfs /secret busybox
```

---

## 16. Key Points

* Containers are short-lived by default
* Data exists only while container exists
* Volumes separate data from container lifecycle
* Named volumes are best for production
* Bind mounts are best for development
* tmpfs is for temporary or sensitive data
