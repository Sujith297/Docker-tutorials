# Docker Networks - Complete Beginner Tutorial

## Why Do We Need Docker Networks?

In real-world applications, the frontend does not communicate directly with the database.

```text
Frontend --> Backend --> Database
```

The backend acts as a bridge between the frontend and the database.

To understand how containers communicate with each other and how we can isolate services, we need to understand Docker Networking.

---

# Default Docker Networks

Docker provides the following network drivers:

1. Bridge
2. Host
3. None
4. Overlay

> Overlay networking is mainly used in Docker Swarm and multi-host communication. We will cover it later.

---

# Install Docker

On Amazon Linux / RHEL:

```bash
yum install docker -y
systemctl start docker
```

Verify Docker is running:

```bash
docker ps
```

---

# List Available Networks

```bash
docker network ls
```

Example output:

```text
NETWORK ID     NAME      DRIVER
xxxxxxx        bridge    bridge
xxxxxxx        host      host
xxxxxxx        none      null
```

You won't see an overlay network unless Docker Swarm is initialized.

---

# Understanding Bridge Network

When you create a container without specifying any network, Docker automatically attaches it to the **bridge network**.

Let's create two containers:

```bash
docker run -itd --name cont1 -p 1111:80 nginx
docker run -itd --name cont2 -p 2222:80 httpd
```

Inspect container information:

```bash
docker inspect cont1
```

Look for:

```text
Network Mode
Gateway
IPAddress
```

You will notice the container is attached to the **bridge** network.

---

## Understanding Port Mapping

Suppose we have a Java application running on Tomcat.

Tomcat listens on:

```text
8080
```

The application inside the container is reachable only through the container's IP and port.

To access it externally, we map:

```text
Host Port --> Container Port
```

Example:

```bash
-p 8080:8080
```

Request Flow:

```text
User
  ↓
Server IP:8080
  ↓
Docker Host
  ↓
Container IP:8080
  ↓
Application
```

That's why we bind a host port to a container port.

---

## Check Container IP Address

```bash
docker inspect cont1 | grep -i "IPAddress"
```

Example:

```text
172.17.0.2
```

For the second container:

```text
172.17.0.3
```

---

## How Does Docker Assign IP Addresses?

Docker creates a virtual bridge called:

```text
docker0
```

By default:

```text
Gateway: 172.17.0.1
```

Containers connected to the bridge network get IPs such as:

```text
172.17.0.2
172.17.0.3
172.17.0.4
...
```

---

## Test Communication Between Containers

Enter container 1:

```bash
docker exec -it cont1 bash
```

Install ping utility:

```bash
apt update -y
apt install iputils-ping -y
```

Ping container 2:

```bash
ping 172.17.0.3
```

Similarly:

```bash
docker exec -it cont2 bash
```

```bash
apt update -y
apt install iputils-ping -y
```

```bash
ping 172.17.0.2
```

If the ping succeeds, both containers can communicate because they are connected to the same bridge network.

---

## Better Way: Communicate Using Container Names

Docker provides internal DNS.

Instead of:

```bash
ping 172.17.0.3
```

You can use:

```bash
ping cont2
```

This is how services usually communicate in real-world Docker environments.

---

# Understanding Host Network

When Docker uses host networking, the container shares the host machine's network stack.

Example:

```bash
docker run -itd --name cont1 --network host nginx
```

In this case:

```text
No separate container IP
No NAT
No port mapping required
```

Requests go directly to the server network.

Flow:

```text
User
 ↓
Server IP:80
 ↓
Nginx Container
```

---

## Difference Between Bridge and Host

### Bridge

```text
Server:1111 --> Container:80
```

Port mapping is required.

### Host

```text
Server:80 --> Container:80
```

Port mapping is not required.

---

## Port Conflict Example

Start nginx:

```bash
docker run -itd --name cont1 --network host nginx
```

Now start another web server:

```bash
docker run -itd --name cont2 --network host httpd
```

One of them may fail because both try to use the same host port.

Check:

```bash
docker ps -a
```

View logs:

```bash
docker logs cont2
```

Typical error:

```text
Address already in use
```

---

# Understanding None Network

Sometimes we do not want a container to communicate with any network.

Create a container:

```bash
docker run -itd --name cont1 --network none nginx
```

Check IP:

```bash
docker inspect cont1 | grep -i "IPAddress"
```

Output:

```text
"IPAddress": ""
```

No network interface is attached.

Use cases:

* Security testing
* Isolated workloads
* Internal processing containers
* Highly restricted environments

---

# Creating Custom Networks

In real projects, we may have:

```text
15 Services
15 Containers
```

We do not want every service communicating with every other service.

Instead, we create separate networks.

Create networks:

```bash
docker network create frontend
docker network create backend
docker network create database
```

Inspect:

```bash
docker network inspect frontend
docker network inspect backend
docker network inspect database
```

Example gateways:

```text
frontend : 172.18.0.1
backend  : 172.19.0.1
database : 172.20.0.1
```

---

## Create Containers in Different Networks

Frontend:

```bash
docker run -itd --name cont1 -p 1111:80 --network frontend nginx
```

Backend:

```bash
docker run -itd --name cont2 -p 2222:80 --network backend nginx
```

Database:

```bash
docker run -itd --name cont3 -p 3333:80 --network database nginx
```

---

## Test Communication

Install ping inside all containers.

Try:

```bash
ping <other-container-ip>
```

You will notice containers from different networks cannot communicate directly.

This is exactly what we want in production.

---

# Connecting Networks

Suppose backend needs to talk to frontend.

Connect cont2 to frontend:

```bash
docker network connect frontend cont2
```

Connect cont1 to backend:

```bash
docker network connect backend cont1
```

Connect cont2 to database:

```bash
docker network connect database cont2
```

Connect cont3 to backend:

```bash
docker network connect backend cont3
```

Now communication is possible through the shared network.

---

# Disconnecting Networks

To remove a container from a network:

```bash
docker network disconnect frontend cont2
```

---

# Real-World Architecture Example

```text
Frontend Network
 ├── React Container
 └── Nginx Container

Backend Network
 ├── Spring Boot Container
 ├── Node.js Container
 └── Python Container

Database Network
 ├── MySQL
 └── Redis
```

Communication:

```text
Frontend <--> Backend
Backend  <--> Database
Frontend X Database
```

The frontend should never communicate directly with the database.

This network isolation improves security and architecture design.

---

# Summary

| Network Type | Purpose                                                                |
| ------------ | ---------------------------------------------------------------------- |
| Bridge       | Default network, containers communicate within the same bridge network |
| Host         | Container uses host machine's network directly                         |
| None         | No networking                                                          |
| Overlay      | Multi-host networking in Docker Swarm                                  |

In real-world projects, custom bridge networks are used extensively to separate frontend, backend, and database services while allowing only the required communication between them.
