# Day 34 – Docker Compose: Real-World Multi-Container Apps

## Overview

Day 34 focused on building a production-style multi-container application using Docker Compose. Instead of running isolated containers manually, we created a complete application stack consisting of:

- Flask Web Application
- PostgreSQL Database
- Redis Cache

We implemented:

- Custom Dockerfile builds
- Docker Compose orchestration
- Environment variables with `.env`
- Health checks
- Service dependencies
- Restart policies
- Named volumes
- Custom networks
- Service labels
- Troubleshooting container startup issues

---

# Architecture

```text
                Browser
                   │
                   ▼
          ┌─────────────────┐
          │ Flask Web App   │
          │ Port 5000       │
          └─────────────────┘
              │         │
              │         │
              ▼         ▼
     ┌────────────┐   ┌────────────┐
     │ PostgreSQL │   │ Redis Cache│
     │ Port 5432  │   │ Port 6379  │
     └────────────┘   └────────────┘

        Docker Compose Network
             app-network
```

---

# Project Structure

```text
day-34/
│
├── docker-compose.yml
├── .env
├── day-34-compose-advanced.md
│
└── app
     ├── app.py
     ├── requirements.txt
     └── Dockerfile
```

---

# Task 1 – Build a Three-Service Stack

## Services

### Web Application

- Python Flask
- Built using a Dockerfile
- Exposed on port 5000

### Database

- PostgreSQL 16
- Data persistence with named volume

### Cache

- Redis latest
- Used for fast in-memory caching

---

# Environment Variables

## .env

```bash
POSTGRES_USER=admin
POSTGRES_PASSWORD=password
POSTGRES_DB=appdb

POSTGRES_HOST=db
POSTGRES_PORT=5432

REDIS_HOST=redis
REDIS_PORT=6379
```

---

# Why Redis?

Redis acts as an in-memory cache.

Instead of querying the database every time:

```text
Browser
 ↓
Flask
 ↓
Postgres
```

We can cache frequently accessed data:

```text
Browser
 ↓
Flask
 ↓
Redis
 ↓
Postgres
```

Benefits:

- Faster response time
- Lower database load
- Better scalability

---

# app.py

The Flask application verifies connectivity to both Postgres and Redis.

Output:

```text
Day 34 Flask App

Postgres: Connected

Redis: Connected
```

---

# Dockerfile

```Dockerfile
FROM python:3.13-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python","app.py"]
```

---

# requirements.txt

```text
Flask
psycopg2-binary
redis
```

---

# docker-compose.yml

```yaml
services:

  db:
    image: postgres:16
    restart: always
    env_file:
      - .env

    volumes:
      - postgres-data:/var/lib/postgresql/data

    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d appdb"]
      interval: 10s
      timeout: 5s
      retries: 5

    networks:
      - app-network

    labels:
      project: "day34"
      service: "database"


  redis:
    image: redis:latest
    restart: always

    networks:
      - app-network

    labels:
      project: "day34"
      service: "cache"


  web:
    build: ./app
    restart: always

    ports:
      - "5000:5000"

    env_file:
      - .env

    depends_on:
      db:
        condition: service_healthy

    networks:
      - app-network

    labels:
      project: "day34"
      service: "frontend"


networks:
  app-network:

volumes:
  postgres-data:
```

---

# Task 2 – Health Checks and Dependencies

Database health check:

```yaml
healthcheck:
  test: ["CMD-SHELL","pg_isready -U admin -d appdb"]
  interval: 10s
  timeout: 5s
  retries: 5
```

### Purpose

The database container might start before PostgreSQL is fully ready.

Health checks ensure:

```text
Database process started
        ↓
Database accepting connections
        ↓
Healthy status
        ↓
Web container starts
```

---

# depends_on

```yaml
depends_on:
  db:
    condition: service_healthy
```

Without this:

```text
Web starts
 ↓
Database not ready
 ↓
Application crashes
```

With health checks:

```text
Database healthy
 ↓
Web starts
 ↓
Everything works
```

---

# Task 3 – Restart Policies

Used:

```yaml
restart: always
```

### Meaning

If containers crash:

```text
Container fails
      ↓
Docker automatically restarts it
```

---

# Restart Policy Types

### no

Default behavior.

Container remains stopped.

---

### on-failure

Restart only when exit code is non-zero.

Good for:

- Batch jobs
- Scripts

---

### always

Best for production services.

Used for:

- Web servers
- Databases
- Redis

---

### unless-stopped

Restart after reboot unless manually stopped.

Useful for home labs.

---

# Task 4 – Build from Dockerfile

Instead of pulling a prebuilt image:

```yaml
build: ./app
```

Docker Compose automatically:

1. Reads Dockerfile
2. Builds image
3. Creates container
4. Connects network
5. Starts services

---

# Rebuilding after code changes

```bash
docker compose up -d --build
```

---

# Task 5 – Named Networks

Created:

```yaml
networks:
  app-network:
```

All services join:

```text
web
db
redis
```

Docker provides internal DNS automatically.

Therefore:

```text
web
 ↓
db:5432

web
 ↓
redis:6379
```

No IP addresses are needed.

---

# Named Volumes

Volume:

```yaml
volumes:
  postgres-data:
```

Mounted to:

```yaml
/var/lib/postgresql/data
```

Purpose:

Preserves database files even if containers are removed.

---

# Labels

```yaml
labels:
  project: "day34"
  service: "database"
```

Labels help with:

- Monitoring
- Container organization
- Traefik
- Logging systems

---

# Testing

## Start

```bash
docker compose up -d
```

---

## Verify Containers

```bash
docker ps
```

Output:

```text
day-34-web-1
day-34-db-1
day-34-redis-1
```

---

## Verify Health

```bash
docker ps
```

Shows:

```text
day-34-db-1 (healthy)
```

---

## Open Browser

```text
http://localhost:5000
```

Result:

```text
Day 34 Flask App

Postgres: Connected

Redis: Connected
```

---

# Troubleshooting

## Problem 1

### Error

```text
ModuleNotFoundError: No module named flask
```

### Cause

requirements.txt missing Flask.

### Fix

Added:

```text
Flask
psycopg2-binary
redis
```

Rebuilt:

```bash
docker compose up -d --build
```

---

## Problem 2

### YAML Syntax Error

```text
did not find expected key
```

### Cause

Incorrect indentation.

### Fix

Corrected labels and service blocks.

---

## Problem 3

App container exited immediately.

### Cause

Database wasn't ready.

### Solution

Added:

```yaml
depends_on:
  db:
    condition: service_healthy
```

and

```yaml
healthcheck:
```

---

# Commands Used

Start:

```bash
docker compose up -d
```

Rebuild:

```bash
docker compose up -d --build
```

View containers:

```bash
docker ps
```

View logs:

```bash
docker compose logs
```

View logs of a single service:

```bash
docker compose logs web
```

Stop:

```bash
docker compose stop
```

Shutdown:

```bash
docker compose down
```

Remove everything:

```bash
docker compose down -v
```

---

# Skills Learned

- Docker Compose
- Custom Dockerfiles
- Flask Containers
- PostgreSQL Containers
- Redis Containers
- Environment Variables
- Health Checks
- depends_on
- Restart Policies
- Named Volumes
- Named Networks
- Labels
- Container DNS
- Build Context
- Multi-Container Applications
- Troubleshooting YAML
- Production-style Container Architecture

---

# Resume Project Description

Designed and deployed a production-style three-tier application stack using Docker Compose consisting of a custom Python Flask application, PostgreSQL database, and Redis cache. Implemented health checks, service dependencies, restart policies, custom networks, persistent storage with named volumes, environment variable management, and container labeling while troubleshooting real-world startup and dependency issues.
