# Docker Basics for DevOps & CI/CD

## Install Docker (Amazon Linux / CentOS)

```bash
yum install docker -y
```

### Start and Enable Docker

```bash
systemctl start docker
systemctl enable docker
systemctl status docker
```

---

# Docker Image

## Build an Image

If a `Dockerfile` is present in the current directory:

```bash
docker build -t <image-name>:<tag> .
```

### Example

```bash
docker build -t myapp:v1 .
```

### Syntax

```bash
docker build -t imagename:tagname .
```

| Parameter | Description                             |
| --------- | --------------------------------------- |
| `-t`      | Tag the image                           |
| `.`       | Current directory containing Dockerfile |

---

# Docker Container

## Run a Container

```bash
docker run -itd --name <container-name> -p <host-port>:<container-port> <image-name>
```

### Example

```bash
docker run -itd --name myapp-container -p 8080:8080 myapp:v1
```

### Parameters

| Parameter | Description                       |
| --------- | --------------------------------- |
| `-i`      | Interactive mode                  |
| `-t`      | Allocate terminal                 |
| `-d`      | Detached mode (run in background) |
| `--name`  | Container name                    |
| `-p`      | Port mapping                      |

---

# Frequently Used Commands

## Pull an Image

```bash
docker pull <image-name>
```

### Example

```bash
docker pull nginx
```

---

## List Running Containers

```bash
docker ps
```

---

## List All Containers

```bash
docker ps -a
```

---

## View Container Logs

```bash
docker logs <container-name>
```

### Example

```bash
docker logs myapp-container
```

---

## Stop a Container

```bash
docker stop <container-name>
```

### Example

```bash
docker stop myapp-container
```

---

## Start a Container

```bash
docker start <container-name>
```

### Example

```bash
docker start myapp-container
```

---

# Bulk Operations

## Stop All Running Containers

```bash
docker stop $(docker ps -q)
```

> **Interview Note:** `-q` returns only container IDs.

---

## Stop All Containers

```bash
docker stop $(docker ps -aq)
```

---

# Quick Interview Questions

### What is Docker?

Docker is a containerization platform used to package applications and their dependencies into portable containers.

### Difference Between Image and Container

| Docker Image              | Docker Container             |
| ------------------------- | ---------------------------- |
| Blueprint/Template        | Running instance of an image |
| Read-only                 | Executable                   |
| Used to create containers | Created from images          |

### Docker Workflow

```text
Dockerfile
    ↓
docker build
    ↓
Docker Image
    ↓
docker run
    ↓
Docker Container
```
