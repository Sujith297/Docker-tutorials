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

## 2. WORKDIR

Used to set the working directory inside the container.

All subsequent instructions such as `COPY`, `RUN`, `CMD`, and `ENTRYPOINT` execute from this directory.

### Syntax

```dockerfile
WORKDIR /path
```

### Example

```dockerfile
WORKDIR /app
```

### Benefit

Instead of repeatedly using:

```dockerfile
RUN cd /app && npm install
RUN cd /app && npm start
```

Use:

```dockerfile
WORKDIR /app

RUN npm install
CMD ["npm","start"]
```

---

## 3. LABEL

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

## 4. MAINTAINER

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

## 5. EXPOSE

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

## 6. COPY

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

## 7. ADD

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

## 8. RUN

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

## 9. CMD

Defines the default command executed when a container starts.

### Syntax

```dockerfile
CMD ["command"]
```

### Example

```dockerfile
CMD ["npm","start"]
```

> Executes during `docker run`.

---

## 10. ENTRYPOINT

Similar to CMD but used for fixed executable commands.

### Example

```dockerfile
ENTRYPOINT ["npm"]
CMD ["start"]
```

Result:

```bash
npm start
```

---

## 11. ARG

Used to pass variables during image build time.

```dockerfile
ARG VERSION=1.0
```

---

## 12. ENV

Used to define environment variables inside the container.

```dockerfile
ENV PORT=3000
ENV DB_HOST=mysql
```

---

# Dockerfile Execution Flow

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

# Most Commonly Used Instructions

```text
FROM
WORKDIR
COPY
RUN
EXPOSE
CMD
ENTRYPOINT
ARG
ENV
```


These are the Dockerfile instructions most frequently asked in DevOps and CI/CD interviews.
