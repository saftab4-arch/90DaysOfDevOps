# Day 29 - Docker Basics

## Objective

Today's goal was to understand Docker fundamentals, learn why containers exist, explore Docker architecture, and run containers from Docker Hub.

---

# Task 1 - What is Docker?

## What is a Container?

A container is a lightweight, portable package that contains:

* Application code
* Dependencies
* Libraries
* Runtime environment

Containers allow applications to run consistently across different environments.

Benefits:

* Fast startup
* Lightweight
* Portable
* Consistent deployments
* Efficient resource utilization

---

## Why Do We Need Containers?

Before containers:

* Applications worked on one machine but failed on another.
* Different operating systems created compatibility issues.
* Dependency conflicts were common.

Containers solve these problems by packaging everything required to run an application.

Example:

```text
Developer Machine
      ↓
Docker Container
      ↓
Testing Environment
      ↓
Production Environment
```

The application behaves the same everywhere.

---

# Containers vs Virtual Machines

## Virtual Machines

Architecture:

```text
Host Machine
      │
Hypervisor
      │
Virtual Machines
      │
Guest Operating Systems
```

Characteristics:

* Each VM contains a full operating system.
* Higher memory consumption.
* Slower startup times.
* Larger storage requirements.

Advantages:

* Strong isolation
* Separate operating systems

Disadvantages:

* Heavy resource usage
* Higher infrastructure costs

---

## Containers

Architecture:

```text
Host Machine
      │
Docker Engine
      │
Containers
```

Characteristics:

* Share the host operating system kernel.
* Lightweight.
* Fast startup.
* Better resource utilization.

Advantages:

* Portable
* Efficient
* Faster deployments

Disadvantages:

* Containers share the host kernel.

---

# Docker Architecture

Docker consists of several components:

## Docker Client

The Docker Client is where users run commands.

Examples:

```bash
docker run
docker ps
docker images
```

---

## Docker Daemon

The Docker Daemon (dockerd) runs in the background and manages:

* Containers
* Images
* Networks
* Volumes

---

## Docker Images

Images are templates used to create containers.

Example:

```text
nginx
ubuntu
mysql
```

---

## Docker Containers

Containers are running instances of Docker images.

Example:

```bash
docker run nginx
```

Creates a container from the nginx image.

---

## Docker Registry

A registry stores Docker images.

Example:

Docker Hub

Common command:

```bash
docker search nginx
```

---

# Docker Architecture Diagram

```text
User
 │
 ▼
Docker Client
 │
 ▼
Docker Daemon
 │
 ├── Images
 ├── Containers
 └── Networks
 │
 ▼
Docker Registry (Docker Hub)
```

---

# Task 2 - Install Docker

Docker Desktop was installed successfully on Windows.

Verification:

```bash
docker --version
```

Example output:

```text
Docker version 28.x.x
```

---

## Run Hello World Container

Command:

```bash
docker run hello-world
```

Output confirms:

* Docker client communicated with Docker daemon.
* Docker daemon pulled the image from Docker Hub.
* Container was created and executed successfully.

---

# Task 3 - Run Real Containers

## Run Nginx Container

Command:

```bash
docker run -d --name web-app -p 8080:80 nginx
```

Verification:

```bash
docker ps
```

Browser:

```text
http://localhost:8080
```

Result:

Nginx welcome page displayed successfully.

---

## Run Ubuntu Container

Command:

```bash
docker run -it --name myubuntu ubuntu bash
```

Commands executed inside container:

```bash
pwd
ls
whoami
cat /etc/os-release
```

This demonstrated how containers can be used as lightweight Linux environments.

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

## Stop a Container

```bash
docker stop web-app
```

---

## Remove a Container

```bash
docker rm web-app
```

---

# Task 4 - Explore Docker

## Detached Mode

Command:

```bash
docker run -d nginx
```

Explanation:

* Container runs in background.
* Terminal remains available.

---

## Custom Container Name

Command:

```bash
docker run -d --name nginx-lab nginx
```

---

## Port Mapping

Command:

```bash
docker run -d -p 8080:80 nginx
```

Meaning:

```text
Host Port      Container Port
8080      ->      80
```

---

## Check Logs

Command:

```bash
docker logs web-app
```

Used to view container output and troubleshoot issues.

---

## Execute Command Inside Running Container

Command:

```bash
docker exec -it web-app sh
```

Example:

```bash
pwd
ls
```

---

# Useful Commands

## Search Docker Hub

```bash
docker search nginx
```

---

## List Images

```bash
docker images
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

## Stop Container

```bash
docker stop <container-name>
```

---

## Remove Container

```bash
docker rm <container-name>
```

---

## View Logs

```bash
docker logs <container-name>
```

---

## Enter Container

```bash
docker exec -it <container-name> sh
```

---

# Why Docker Matters for DevOps

Docker is one of the most important technologies in modern DevOps.

It enables:

* Consistent deployments
* CI/CD pipelines
* Microservices architecture
* Cloud-native applications
* Kubernetes deployments

Many modern applications are packaged and deployed using containers.

Understanding Docker is a foundational skill for Cloud and DevOps Engineers.

---

# Key Learnings

During this lab I learned:

* What containers are and why they exist.
* Differences between containers and virtual machines.
* Core Docker architecture components.
* How to pull images from Docker Hub.
* How to create and run containers.
* How to access containers interactively.
* How to expose ports and access applications.
* How to inspect logs and troubleshoot containers.
* Basic Docker administration commands.

---

# Screenshots

Include screenshots of:

1. Docker installation verification
2. hello-world container execution
3. Running nginx container
4. Browser displaying nginx page
5. Ubuntu interactive container session
6. docker ps output
7. docker ps -a output

---

# Conclusion

This lab introduced the fundamentals of Docker and containerization. By the end of the exercise, I successfully installed Docker, ran multiple containers, explored Docker architecture, and practiced essential container management commands that form the foundation of modern DevOps workflows.
