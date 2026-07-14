# Dockerfile for Python Application

## Dockerfile

```dockerfile
FROM python:3.12-alpine

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

ENTRYPOINT ["python"]

CMD ["app.py"]
```

---

## Explanation

### FROM

Uses a lightweight Python base image.

```dockerfile
FROM python:3.12-alpine
```

Alternative:

```dockerfile
FROM python:3.12-slim
```

* Alpine → Smallest image size
* Slim → Better package compatibility

---

### WORKDIR

Sets the working directory inside the container.

```dockerfile
WORKDIR /app
```

---

### COPY requirements.txt

Copies dependency file first.

```dockerfile
COPY requirements.txt .
```

### Why?

Docker caches layers.

If application code changes but dependencies don't, Docker reuses the cached dependency layer, making builds faster.

---

### Install Dependencies

```dockerfile
RUN pip install -r requirements.txt
```

> Correction: `pip install requirements.txt` is incorrect. Use `pip install -r requirements.txt`.

---

### Copy Application Files

```dockerfile
COPY . .
```

Copies the entire application into the container.

---

### EXPOSE

```dockerfile
EXPOSE 5000
```

Documents that the application runs on port 5000.

Common ports:

* Flask → 5000
* Django → 8000
* FastAPI → 8000

---

### ENTRYPOINT

Defines the executable.

```dockerfile
ENTRYPOINT ["python"]
```

---

### CMD

Provides arguments to ENTRYPOINT.

```dockerfile
CMD ["app.py"]
```

Container execution becomes:

```bash
python app.py
```

---

## Interview Question

### Why Copy requirements.txt Before COPY . . ?

❌ Bad

```dockerfile
COPY . .

RUN pip install -r requirements.txt
```

Every code change forces dependency reinstallation.

✅ Good

```dockerfile
COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .
```

Docker reuses cached dependency layers, reducing build time.

---

## Build & Run

### Build Image

```bash
docker build -t python-app:v1 .
```

### Run Container

```bash
docker run -itd --name python-container -p 5000:5000 python-app:v1
```
