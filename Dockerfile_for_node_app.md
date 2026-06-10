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

```dockerfile
FROM node:20-alpine AS build

WORKDIR /app

COPY package*.json .

RUN npm install

COPY . .
```

### Stage 2 - Runtime Stage

```dockerfile
FROM node:20-alpine AS final

WORKDIR /app

COPY --from=build /app .

RUN npm install --production

EXPOSE 3000

ENTRYPOINT ["npm"]
CMD ["start"]
```

### Benefits

* Separates build and runtime environments
* Reduces final image size
* Improves security
* Faster image transfer and deployment
* Removes unnecessary development dependencies

---

## Additional Size Reduction Techniques

### Use Production Dependencies Only

```dockerfile
RUN npm install --production
```

or

```dockerfile
RUN npm ci --omit=dev
```

Removes development dependencies from the final image.

---

### Use .dockerignore

Example:

```text
node_modules
.git
.gitignore
README.md
.env
```

Prevents unnecessary files from being copied into the image.

---

## Updated Dockerfile Execution Flow

```text
FROM
 ↓
WORKDIR
 ↓
LABEL
 ↓
COPY / ADD
 ↓
ARG
 ↓
ENV
 ↓
RUN
 ↓
EXPOSE
 ↓
ENTRYPOINT
 ↓
CMD
 ↓
Docker Image
```

---

## Most Common Docker Image Optimization Techniques

* Use Alpine Images
* Use Slim Images
* Use Multi-Stage Builds
* Use WORKDIR
* Use .dockerignore
* Install only Production Dependencies
* Reduce Docker Layers
* Remove Temporary Files
* Copy Only Required Files

```
```
