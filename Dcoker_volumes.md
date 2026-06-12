# Docker Volumes - Persisting Container Data

## Why Do We Need Docker Volumes?

Containers are temporary in nature.

Suppose we run a database container:

```bash
docker run -itd --name mysql-db mysql
```

The database stores data inside the container filesystem.

If:

* Container crashes
* Container is removed
* Container is recreated

all the data stored inside the container will be lost.

For applications like:

* MySQL
* PostgreSQL
* MongoDB
* Redis
* Elasticsearch

losing data is not acceptable.

To solve this problem Docker provides **Volumes**.

---

# Understanding the Problem

Create an nginx container:

```bash
docker run -itd --name nginx-test nginx
```

Enter the container:

```bash
docker exec -it nginx-test bash
```

Create a file:

```bash
echo "Hello Docker Volumes" > test.txt
```

Verify:

```bash
cat test.txt
```

Now exit and remove the container:

```bash
docker rm -f nginx-test
```

Create a new container:

```bash
docker run -itd --name nginx-test nginx
```

Enter the container:

```bash
docker exec -it nginx-test bash
```

Check for the file:

```bash
cat test.txt
```

Output:

```text
No such file or directory
```

The data is gone because it was stored inside the container.

---

# Creating a Docker Volume

Create a volume:

```bash
docker volume create mysql-data
```

List volumes:

```bash
docker volume ls
```

Inspect volume:

```bash
docker volume inspect mysql-data
```

Example output:

```text
Mountpoint:
/var/lib/docker/volumes/mysql-data/_data
```

Docker stores volume data on the host machine.

---

# Using Volume with a Container

Create a container and attach the volume:

```bash
docker run -itd \
--name nginx-vol \
-v mysql-data:/usr/share/nginx/html \
nginx
```

Here:

```text
mysql-data                  -> Docker Volume
/usr/share/nginx/html       -> Path inside container
```

---

# Testing Persistence

Enter the container:

```bash
docker exec -it nginx-vol bash
```

Create a file:

```bash
echo "Volume Data" > /usr/share/nginx/html/index.html
```

Verify:

```bash
cat /usr/share/nginx/html/index.html
```

Now remove the container:

```bash
docker rm -f nginx-vol
```

Create another container using the same volume:

```bash
docker run -itd \
--name nginx-vol2 \
-v mysql-data:/usr/share/nginx/html \
nginx
```

Enter:

```bash
docker exec -it nginx-vol2 bash
```

Check:

```bash
cat /usr/share/nginx/html/index.html
```

Output:

```text
Volume Data
```

Even though the first container was deleted, the data still exists because it is stored in the Docker volume.

---

# Real-Time Database Example

MySQL:

```bash
docker volume create mysql-data
```

```bash
docker run -itd \
--name mysql \
-e MYSQL_ROOT_PASSWORD=root123 \
-v mysql-data:/var/lib/mysql \
mysql
```

Important path:

```text
/var/lib/mysql
```

This is where MySQL stores all database files.

Without a volume:

```text
Container Deleted = Database Deleted
```

With a volume:

```text
Container Deleted = Database Safe
```

---

# Bind Mounts vs Volumes

## Docker Volume

```bash
-v mysql-data:/var/lib/mysql
```

Docker manages the storage.

Best for:

* Databases
* Production environments
* Persistent application data

---

## Bind Mount

```bash
-v /home/ec2-user/data:/var/lib/mysql
```

Host directory is directly mounted inside the container.

Best for:

* Development
* Source code sharing
* Configuration files

---

# Volume Commands

Create Volume:

```bash
docker volume create myvolume
```

List Volumes:

```bash
docker volume ls
```

Inspect Volume:

```bash
docker volume inspect myvolume
```

Remove Volume:

```bash
docker volume rm myvolume
```

Remove Unused Volumes:

```bash
docker volume prune
```

---

# Real-World Usage

Most commonly used with:

* MySQL
* PostgreSQL
* MongoDB
* Redis
* Jenkins
* SonarQube
* Elasticsearch

Example:

```text
Application Container
        |
        |
Docker Volume
        |
        |
Host Storage
```

Even if the container crashes, gets recreated, or is upgraded, the data remains safe.

---

# Summary

| Storage Type         | Data Survives Container Deletion? |
| -------------------- | --------------------------------- |
| Container Filesystem | ❌ No                              |
| Bind Mount           | ✅ Yes                             |
| Docker Volume        | ✅ Yes                             |

Docker Volumes are mainly used to persist important data, especially database data, so that container failures or recreations do not result in data loss.
