# Day 32 – Docker Volumes & Networking

## Overview

Today's goal was to solve two real-world Docker problems:

- Data persistence
- Container communication

Containers are ephemeral. If a container is deleted, everything inside it disappears unless the data is stored outside the container.

Containers also need a way to communicate with each other. Docker networking provides that capability.

---

# Skills Practiced

- PostgreSQL containers
- Named volumes
- Bind mounts
- Docker networks
- Custom bridge networks
- Container-to-container communication
- DNS-based service discovery

---

# Lab Environment

- Ubuntu WSL
- Docker Desktop
- PostgreSQL 16
- Nginx
- Ubuntu container
- Custom bridge networks

---

# Task 1 – Container Data Loss

## Create .env file

```bash
nano .env
```

Contents:

```env
POSTGRES_USER=admin
POSTGRES_PASSWORD=password123
POSTGRES_DB=appdb
```

---

## Start PostgreSQL without a volume

```bash
docker run -d --name no-volume --env-file .env postgres:16
```

Verify:

```bash
docker ps
```

---

## Connect to PostgreSQL

```bash
docker exec -it no-volume psql -U admin -d appdb
```

Create table:

```sql
CREATE TABLE students (
    id SERIAL PRIMARY KEY,
    name TEXT
);
```

Insert records:

```sql
INSERT INTO students (name)
VALUES ('Syed'), ('Aftab');
```

Verify:

```sql
SELECT * FROM students;
```

Output:

```
 id | name
----+-------
 1  | Syed
 2  | Aftab
```

---

## Delete container

```bash
docker stop no-volume

docker rm no-volume
```

---

## Start another container

```bash
docker run -d --name no-volume --env-file .env postgres:16
```

Reconnect:

```bash
docker exec -it no-volume psql -U admin -d appdb
```

Query:

```sql
SELECT * FROM students;
```

Result:

```
ERROR: relation "students" does not exist
```

---

# Observation

Data disappeared because containers are ephemeral.

---

# Task 2 – Named Volumes

## Create volume

```bash
docker volume create pd-data
```

Verify:

```bash
docker volume ls
```

---

## Start PostgreSQL with volume

```bash
docker run -d \
--name with-volume \
--env-file .env \
-v pd-data:/var/lib/postgresql/data \
postgres:16
```

---

## Create table again

```bash
docker exec -it with-volume psql -U admin -d appdb
```

```sql
CREATE TABLE students (
id SERIAL PRIMARY KEY,
name TEXT
);

INSERT INTO students (name)
VALUES ('Syed'),('Aftab');

SELECT * FROM students;
```

Output:

```
1 | Syed
2 | Aftab
```

---

## Delete container

```bash
docker stop with-volume

docker rm with-volume
```

Volume still exists:

```bash
docker volume ls
```

---

## Create new container using same volume

```bash
docker run -d \
--name with-volume \
--env-file .env \
-v pd-data:/var/lib/postgresql/data \
postgres:16
```

Reconnect:

```bash
docker exec -it with-volume psql -U admin -d appdb
```

Query:

```sql
SELECT * FROM students;
```

Output:

```
1 | Syed
2 | Aftab
```

---

# Observation

Containers are temporary.

Volumes persist.

---

# Task 3 – Bind Mounts

Create directory:

```bash
mkdir website

cd website
```

Create file:

```bash
nano index.html
```

Contents:

```html
<h1>Hello from Host Machine!</h1>
```

---

## Start nginx

```bash
docker run -d \
--name nginx-bind \
-p 8080:80 \
-v $(pwd):/usr/share/nginx/html \
nginx
```

Open:

```
http://localhost:8080
```

Page shows:

```
Hello from Host Machine!
```

---

Modify index.html

```html
<h1>Bind mount works!</h1>
```

Refresh browser.

Page updates immediately without restarting container.

---

# Observation

Bind mounts map host files directly into containers.

---

# Task 4 – Docker Networking

View networks:

```bash
docker network ls
```

Inspect default bridge:

```bash
docker network inspect bridge
```

---

## Run containers

```bash
docker run -dit --name alpine1 alpine

docker run -dit --name alpine2 alpine
```

Install ping:

```bash
docker exec -it alpine1 sh

apk add iputils
```

Same for alpine2.

---

Test connectivity.

IP communication works.

Container-name resolution on default bridge is limited.

---

# Task 5 – Custom Networks

Create network:

```bash
docker network create my-app-net
```

Run containers:

```bash
docker run -d --name network-test-1 --network my-app-net -p 80:80 nginx

docker run -d --name network-test-2 --network my-app-net -p 81:80 nginx
```

Enter network-test-2:

```bash
docker exec -it network-test-2 bash
```

Test:

```bash
curl network-test-1
```

Result:

Returned nginx default webpage.

---

# Observation

Custom bridge networks provide automatic DNS resolution.

Container names become hostnames.

---

# Task 6 – App Container to Database Container

Create network:

```bash
docker network create app-net
```

---

## Start database

```bash
docker run -d \
--name postgres-db \
--network app-net \
--env-file .env \
-v pd-data:/var/lib/postgresql/data \
postgres:16
```

---

## Start application container

```bash
docker run -it --name app-container --network app-net ubuntu
```

Inside:

```bash
apt update

apt install -y iputils-ping netcat-openbsd
```

---

Ping database:

```bash
ping postgres-db
```

Success.

---

Verify port:

```bash
nc -zv postgres-db 5432
```

Output:

```
Connection to postgres-db 5432 succeeded
```

---

# Architecture

```
                    app-net

        +-------------------------+
        |                         |
        |     app-container        |
        |      Ubuntu App          |
        +-----------+-------------+
                    |
                    |
             postgres-db:5432
                    |
                    |
        +-----------+-------------+
        |      PostgreSQL          |
        |        pd-data           |
        +-------------------------+

```

---

# Named Volume vs Bind Mount

| Feature | Named Volume | Bind Mount |
|-----------|------------|------------|
| Managed by Docker | Yes | No |
| Host path required | No | Yes |
| Good for databases | Yes | No |
| Good for source code | No | Yes |
| Portable | Yes | Depends |
| Production use | Yes | Development |

---

# Key Learnings

### Containers are ephemeral

Deleting a container removes its filesystem.

---

### Volumes provide persistence

Data survives container deletion.

---

### Bind mounts synchronize files

Changes on the host appear instantly inside containers.

---

### Custom networks provide DNS

Containers communicate by name.

---

### Applications should not use IP addresses

Use service names instead:

```python
postgres://postgres-db:5432
```

---

# Real-World Use Cases

### Volumes

- PostgreSQL
- MySQL
- Grafana
- Prometheus
- Redis

### Bind Mounts

- Source code
- Config files
- Development environments

### Custom Networks

- Web ↔ API
- API ↔ Database
- Microservices
- Docker Compose stacks

---

# Final Takeaway

Docker containers are disposable.

Data should live in volumes.

Applications should communicate through networks using service names instead of IP addresses.
