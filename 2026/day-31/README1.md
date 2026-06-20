# Day 31 – Dockerfile: Build Your Own Images

## Objective

Learn how to create custom Docker images and understand Dockerfile instructions, image layers, build cache, CMD, and ENTRYPOINT.

---

# Dockerfile Workflow

```text
Dockerfile
     ↓
docker build
     ↓
Image
     ↓
docker run
     ↓
Container
```

---

# Task 1 – My First Dockerfile

Dockerfile:

```dockerfile
FROM ubuntu

RUN apt update && apt install curl -y

CMD ["echo","Hello from my custom image!"]
```

Build:

```bash
docker build -t my-ubuntu:v1 .
```

Run:

```bash
docker run my-ubuntu:v1
```

Output:

```text
Hello from my custom image!
```

### Concepts Learned

* FROM creates the base layer.
* RUN executes during build time.
* CMD runs when the container starts.
* Container exits after the command finishes.

---

# Task 2 – Dockerfile Instructions

Dockerfile:

```dockerfile
FROM ubuntu

RUN apt update && apt install -y curl

WORKDIR /app

COPY app.txt .

EXPOSE 8080

CMD ["cat","app.txt"]
```

Created file:

```text
Hello from app.txt
```

Build:

```bash
docker build -t docker-demo:v1 .
```

Run:

```bash
docker run docker-demo:v1
```

Output:

```text
Hello from app.txt
```

Verified inside container:

```bash
docker run -it docker-demo:v1 bash
```

Commands:

```bash
pwd
ls
cat app.txt
```

---

# Dockerfile Instructions

| Instruction | Purpose                       |
| ----------- | ----------------------------- |
| FROM        | Base image                    |
| RUN         | Execute commands during build |
| WORKDIR     | Set working directory         |
| COPY        | Copy files into image         |
| EXPOSE      | Document application port     |
| CMD         | Default command               |

---

# Task 3 – CMD vs ENTRYPOINT

## CMD Example

```dockerfile
FROM ubuntu

CMD ["echo","hello"]
```

Run:

```bash
docker run cmd-demo:v1
```

Output:

```text
hello
```

Override:

```bash
docker run cmd-demo:v1 ls
```

CMD gets replaced.

---

## ENTRYPOINT Example

```dockerfile
FROM ubuntu

ENTRYPOINT ["echo"]
```

Run:

```bash
docker run entry-demo:v1 Docker Rocks
```

Output:

```text
Docker Rocks
```

ENTRYPOINT remains fixed and appends arguments.

---

# CMD vs ENTRYPOINT

| CMD                        | ENTRYPOINT               |
| -------------------------- | ------------------------ |
| Easy to override           | Fixed executable         |
| Default behavior           | Main application         |
| Replaced by custom command | Receives extra arguments |

---

# Task 4 – Static Website

Dockerfile:

```dockerfile
FROM nginx:alpine

COPY index.html /usr/share/nginx/html/

EXPOSE 80
```

Build:

```bash
docker build -t my-website:v1 .
```

Run:

```bash
docker run -d --name website -p 8080:80 my-website:v1
```

Open:

```text
http://localhost:8080
```

Screenshot:

![Website](screenshots/website-running.png)

---

# Task 5 – .dockerignore

Contents:

```text
node_modules
.git
*.md
.env
```

Purpose:

* Reduce build context
* Improve build speed
* Exclude unnecessary files

---

# Task 6 – Docker Build Cache

Docker uses layers:

```text
FROM
 ↓
RUN
 ↓
COPY
 ↓
CMD
```

When nothing changes:

```text
CACHED
```

Changing a file invalidates that layer and everything after it.

### Why Layer Order Matters

Bad:

```dockerfile
COPY . .

RUN npm install
```

Every code change reruns npm install.

Good:

```dockerfile
COPY package*.json .

RUN npm install

COPY . .
```

Expensive layers stay cached.

---

# Commands Practiced

```bash
docker build
docker run
docker images
docker exec
docker ps
docker logs
```

---

# Skills Gained

* Writing Dockerfiles
* Building custom images
* Understanding build context
* Using COPY and WORKDIR
* Understanding CMD vs ENTRYPOINT
* Creating static websites with Nginx
* Using .dockerignore
* Understanding image layers and cache

#90DaysOfDevOps
#Docker
#DevOps
