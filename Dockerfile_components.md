# Dockerfile Components

A **Dockerfile** is a text file that contains instructions to build a Docker Image.

---

# Common Dockerfile Instructions

## 1. FROM

Used to pull a base image from a Docker Registry.

### Syntax

```dockerfile
FROM imagename:tag
```

### Example

```dockerfile
FROM node:18
```

> Every Dockerfile typically starts with a `FROM` instruction.

---

## 2. LABEL

Used to add metadata/description to the image.

### Syntax

```dockerfile
LABEL key=value
```

### Example

```dockerfile
LABEL project="ecommerce-app"
LABEL version="1.0"
```

---

## 3. MAINTAINER

Used to specify the author of the Dockerfile.

### Syntax

```dockerfile
MAINTAINER username
```

### Example

```dockerfile
MAINTAINER Sujith
```

> Interview Note: `MAINTAINER` is deprecated. Modern Docker uses `LABEL maintainer="name"`.

---

## 4. EXPOSE

Used to document the port on which the application runs inside the container.

### Syntax

```dockerfile
EXPOSE port
```

### Example

```dockerfile
EXPOSE 3000
```

---

## 5. COPY

Copies files from the local system to the container.

### Syntax

```dockerfile
COPY source destination
```

### Example

```dockerfile
COPY . /app
```

---

## 6. ADD

Copies files or downloads content from a URL.

### Syntax

```dockerfile
ADD source/url destination
```

### Example

```dockerfile
ADD app.zip /app
```

```dockerfile
ADD https://example.com/file.zip /app
```

### COPY vs ADD

| COPY                    | ADD                                |
| ----------------------- | ---------------------------------- |
| Copies local files      | Copies local files + URLs          |
| Preferred in most cases | Used when URL extraction is needed |

---

## 7. RUN

Executes commands while building the Docker Image.

### Syntax

```dockerfile
RUN command
```

### Example

```dockerfile
RUN npm install
```

```dockerfile
RUN yum install git -y
```

> Executes during `docker build`.

---

## 8. CMD

Defines the default command executed when a container starts.

### Syntax

```dockerfile
CMD ["command"]
```

### Example

```dockerfile
CMD ["npm","start"]
```

### Common Examples

#### React

```dockerfile
CMD ["npm","run","dev"]
```

#### Node.js Backend

```dockerfile
CMD ["npm","start"]
```

> Executes during `docker run`.

---

## 9. ENTRYPOINT

Similar to CMD but used for fixed executable commands.

### Syntax

```dockerfile
ENTRYPOINT ["command"]
```

### Example

```dockerfile
ENTRYPOINT ["npm"]
CMD ["start"]
```

### How It Works

Container execution:

```bash
npm start
```

### CMD vs ENTRYPOINT

| CMD                        | ENTRYPOINT            |
| -------------------------- | --------------------- |
| Can be overridden easily   | Fixed executable      |
| Provides default arguments | Provides main command |
| Flexible                   | More restrictive      |

**Interview Example**

```dockerfile
ENTRYPOINT ["npm"]
CMD ["start"]
```

Result:

```bash
npm start
```

---

## 10. ARG

Used to pass variables during image build time.

### Syntax

```dockerfile
ARG VERSION
```

### Example

```dockerfile
ARG VERSION=1.0
```

Build:

```bash
docker build --build-arg VERSION=2.0 -t myapp .
```

> Can be modified during image build.

---

## 11. ENV

Used to define environment variables inside the container.

### Syntax

```dockerfile
ENV key=value
```

### Example

```dockerfile
ENV PORT=3000
ENV DB_HOST=mysql
```

> Available inside the container at runtime.

---

# ARG vs ENV

| ARG                                          | ENV                                    |
| -------------------------------------------- | -------------------------------------- |
| Build-time variable                          | Runtime variable                       |
| Available during image build                 | Available inside running container     |
| Can be overridden using `--build-arg`        | Typically fixed inside image/container |
| Not persisted in final container environment | Persisted in container environment     |

---

# Dockerfile Execution Flow

```text
FROM
 ↓
LABEL
 ↓
COPY / ADD
 ↓
RUN
 ↓
EXPOSE
 ↓
ENV
 ↓
ENTRYPOINT
 ↓
CMD
 ↓
Docker Image
```

---

# Most Commonly Used Instructions

```text
FROM
COPY
RUN
EXPOSE
CMD
ENTRYPOINT
ARG
ENV
```

These are the Dockerfile instructions most frequently asked in DevOps and CI/CD interviews.
