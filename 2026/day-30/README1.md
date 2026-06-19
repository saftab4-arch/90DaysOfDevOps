# Day 30 – Docker Images & Container Lifecycle

## Objective

Understand Docker images, image layers, and the complete container lifecycle.

---

# Image vs Container

```text
Image
 ↓
Blueprint / Template
 ↓
Container
 ↓
Running Instance
```

One image can create many containers.

---

# Images Used

* nginx
* ubuntu
* alpine

Check local images:

```bash
docker images
```

---

# Pull Images

```bash
docker pull nginx
docker pull ubuntu
docker pull alpine
```

---

# Why Is Alpine Smaller?

| Image  | Size  |
| ------ | ----- |
| Alpine | ~4MB  |
| Ubuntu | ~45MB |
| Nginx  | ~66MB |

Alpine uses a minimal base system and is optimized for containers.

---

# Inspect Image

```bash
docker image inspect nginx
```

Information observed:

* Image ID
* Architecture
* OS
* Entrypoint
* Exposed Ports
* RootFS Layers

---

# Image Layers

```bash
docker image history nginx
```

Layers allow:

* Faster builds
* Shared storage
* Efficient caching
* Smaller downloads

Example:

```text
Base OS
 ↓
Dependencies
 ↓
Packages
 ↓
Nginx
 ↓
Configuration
```

---

# Container Lifecycle

```text
Create
 ↓
Start
 ↓
Pause
 ↓
Unpause
 ↓
Stop
 ↓
Restart
 ↓
Kill
 ↓
Remove
```

---

# Create Container Without Starting

```bash
docker create --name lifecycle nginx
```

State:

```text
Created
```

---

# Start Container

```bash
docker start lifecycle
```

State:

```text
Running
```

---

# Pause Container

```bash
docker pause lifecycle
```

State:

```text
Paused
```

---

# Unpause Container

```bash
docker unpause lifecycle
```

State:

```text
Running
```

---

# Stop Container

```bash
docker stop lifecycle
```

State:

```text
Exited
```

---

# Restart Container

```bash
docker restart lifecycle
```

---

# Kill Container

```bash
docker kill lifecycle
```

---

# Remove Container

```bash
docker rm lifecycle
```

---

# Logs

```bash
docker logs webserver

docker logs -f webserver
```

---

# Execute Commands Inside Container

Interactive shell:

```bash
docker exec -it webserver bash
```

Single commands:

```bash
docker exec webserver hostname

docker exec webserver ls /

docker exec webserver cat /etc/os-release
```

---

# Inspect Container

```bash
docker inspect webserver
```

Observed:

* Container IP Address
* Port mappings
* Mounts
* Network settings

Container IP only:

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' webserver
```

---

# Cleanup Commands

Stop all running containers:

```bash
docker stop $(docker ps -q)
```

Remove all containers:

```bash
docker rm $(docker ps -aq)
```

Remove unused images:

```bash
docker image prune
```

Remove everything unused:

```bash
docker system prune -a
```

---

# Disk Usage

```bash
docker system df
```

Displays:

* Images
* Containers
* Volumes
* Build Cache

---

# Useful Commands

```bash
docker images
docker image inspect
docker image history
docker create
docker start
docker pause
docker unpause
docker stop
docker restart
docker kill
docker rm
docker logs
docker exec
docker inspect
docker system df
docker system prune
```

---

# Technologies Used

* Docker Engine
* Docker Hub
* Nginx
* Ubuntu
* Alpine Linux

---

# Skills Gained

* Understanding image layers
* Managing container lifecycle
* Inspecting images and containers
* Working with logs
* Executing commands inside containers
* Docker cleanup and storage management

#90DaysOfDevOps #Docker #DevOps
