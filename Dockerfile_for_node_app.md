# Dockerfile Optimization & Multi-Stage Builds

## Why Optimize Docker Images?

Benefits of smaller Docker images:

* Faster image builds
* Faster image pull/push
* Reduced storage usage
* Improved security (smaller attack surface)
* Faster deployments in CI/CD pipelines

---

# Image Size Reduction Techniques

## 1. Use Alpine Images

Alpine Linux is a lightweight Linux distribution.

### Example

```dockerfile id="utx9gh"
FROM node:20-alpine
```

### Benefits

* Very small image size
* Faster downloads
* Reduced attack surface

---

## 2. Use Slim Images

Slim images remove unnecessary packages while keeping compatibility.

### Example

```dockerfile id="gn6j4i"
FROM node:20-slim
```

### Benefits

* Smaller than standard images
* Better compatibility than Alpine

---

## 3. Multi-Stage Docker Builds

Used to separate build dependencies from runtime dependencies.

### Benefits

* Removes unnecessary files from final image
* Smaller production images
* Better security
* Faster deployment

---

# Normal Dockerfile vs Multi-Stage Dockerfile

## Normal Dockerfile

```dockerfile id="svjvgr"
FROM node:20

WORKDIR /app

COPY package*.json .
RUN npm install

COPY . .

EXPOSE 3000

ENTRYPOINT ["npm"]
CMD ["run","dev"]
```

### Drawbacks

* Includes build dependencies in final image
* Larger image size
* More security vulnerabilities

---

## Multi-Stage Dockerfile

### Stage 1 - Build Stage

```dockerfile id="v71n1x"
FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json .
RUN npm install

COPY . .
```

### Stage 2 - Runtime Stage

```dockerfile id="7j9z2y"
FROM node:20-alpine AS final

WORKDIR /app

COPY --from=build /app .

ENTRYPOINT ["npm"]
CMD ["run","dev"]
```

---

# Multi-Stage Build Flow

```text id="5ezv1h"
Stage 1 (Build)
├── Install Dependencies
├── Compile Application
└── Generate Artifacts
          ↓
Stage 2 (Runtime)
├── Copy Required Files
└── Run Application
```

---

# Better Production Example

For Node.js applications:

```dockerfile id="e7k3xf"
FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json .
RUN npm install

COPY . .

FROM node:20-alpine

WORKDIR /app

COPY --from=build /app .

EXPOSE 3000

ENTRYPOINT ["npm"]
CMD ["start"]
```

> Interview Note: Production containers usually use `npm start` instead of `npm run dev`.

---

# Additional Image Optimization Techniques

## Use .dockerignore

Avoid copying unnecessary files.

### Example

```text id="5afn0l"
node_modules
.git
.gitignore
README.md
.env
```

---

## Reduce Layers

❌ Bad

```dockerfile id="i5w2f3"
RUN apt update
RUN apt install git -y
RUN apt install curl -y
```

✅ Good

```dockerfile id="s7v2nz"
RUN apt update && \
    apt install -y git curl
```

---

## Copy Only Required Files

❌ Bad

```dockerfile id="y5z55v"
COPY . .
```

✅ Better

```dockerfile id="q7xv4v"
COPY package*.json .
COPY src/ src/
```

---

## Remove Temporary Files

```dockerfile id="rsl14z"
RUN apt-get clean && rm -rf /var/lib/apt/lists/*
```

---

# Interview Questions

### What is a Multi-Stage Dockerfile?

A Dockerfile that uses multiple `FROM` statements to separate build and runtime environments, reducing the final image size.

---

### Why Use Alpine Images?

* Smaller image size
* Faster download and deployment
* Reduced attack surface

---

### Difference Between Alpine and Slim Images

| Alpine                                 | Slim                       |
| -------------------------------------- | -------------------------- |
| Smallest size                          | Slightly larger            |
| Uses musl libc                         | Uses glibc                 |
| Faster downloads                       | Better compatibility       |
| Sometimes package compatibility issues | Fewer compatibility issues |

---

### Most Common Docker Image Optimization Techniques

* Use Alpine/Slim base images
* Use Multi-Stage Builds
* Use `.dockerignore`
* Reduce Docker layers
* Remove temporary files
* Copy only required files
* Avoid unnecessary dependencies

These are the Docker optimization concepts most frequently asked in DevOps, Docker, Jenkins, and CI/CD interviews.
