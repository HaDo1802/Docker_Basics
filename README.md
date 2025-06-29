# Docker — From Basics to Advanced

Comprehensive guide to containerize, configure, and scale applications using Docker
![Alt Text](https://miro.medium.com/v2/resize:fit:2000/1*vQK4s0lOiK1ZkcXxFNIMDQ.png)
---
https://miro.medium.com/v2/resize:fit:2000/1*vQK4s0lOiK1ZkcXxFNIMDQ.png 
## Table of Contents
1. [Introduction](#introduction)  
2. [Why Docker?](#why-docker)  
3. [Core Concepts](#core-concepts)  
   - Images & Containers  
   - Docker Engine & CLI  
   - Container Registries  
4. [Basic Usage](#basic-usage)  
   - Installation  
   - Hello World  
   - Pulling & Running Images  
5. [Dockerfiles](#dockerfiles)  
   - Anatomy of a Dockerfile  
   - Example: Simple Web App  
6. [Container Management](#container-management)  
   - Inspecting  
   - Logs  
   - Exec  
   - Cleanup  
7. [Data Storage](#data-storage)  
   - Volumes  
   - Bind mounts  
8. [Networking](#networking)  
   - Network modes (bridge, host)  
   - Docker networking commands  
9. [Docker Compose](#docker-compose)  
   - `docker-compose.yml`  
   - Example: Multi-container stack  
10. [Image Optimization & Best Practices](#image-optimization-and-best-practices)  
11. [Advanced Topics](#advanced-topics)  
12. [CI/CD & Registries](#cicd--registries)  
13. [Troubleshooting](#troubleshooting)  
14. [Further Reading](#further-reading)  

---

## Introduction
Docker is a lightweight containerization platform that uses OS-level virtualization to package applications into portable containers.

---

## Why Docker?
- **Consistency:** "Works on my machine" becomes redundant—containers encapsulate all dependencies.
- **Efficiency:** Containers share the host kernel, making them lighter than VMs.
- **Portability:** Run reliably across environments—dev, CI, production—with the same container.

---

## Core Concepts

### Images & Containers
- **Image**: Read-only template that defines what the container will run.
- **Container**: Runtime instance of an image.

### Docker Engine & CLI
- The **daemon** `dockerd` manages containers.
- The **client** `docker` provides CLI to interact with the daemon.

### Container Registries
- **Docker Hub** is the default public registry.
- You can use private registries, including GitHub Container Registry.

### Docker Architecture

![Docker Architecture](https://miro.medium.com/v2/resize:fit:1400/0*SPCr5zXp8jw9Mfk8.png)

This diagram illustrates how developers create Dockerfiles to build Docker images, which are pushed to Docker Hub and later pulled by different environments (e.g., staging, testing) to run as containers.

---

## Basic Usage

### Installation
Install Docker Desktop (Windows/macOS) or Docker Engine (Linux).

### Hello World
```bash
docker run hello-world
```

### Pull & Run an Image
```bash
docker pull nginx
docker run -d --name web -p 8080:80 nginx
```

---

## Dockerfiles

### Anatomy
```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
CMD ["npm","start"]
EXPOSE 3000
```

### Example
```bash
docker build -t my-node-app .
docker run -p 3000:3000 my-node-app
```

---

## Container Management
- `docker ps`, `docker ps -a`
- `docker logs <container>`
- `docker exec -it <container> /bin/sh`
- `docker stop`, `docker rm`, `docker rmi`

---

## Data Storage

### Volumes
```bash
docker volume create data_vol
docker run -v data_vol:/data my-app
```

### Bind Mounts
```bash
docker run -v "$(pwd)":/app my-app
```

---

## Networking

- Bridge (default network)
- Host (shares host network)

---

## Docker Compose

### `docker-compose.yml`
```yaml
version: "3"
services:
  web:
    build: .
    ports:
     - "3000:3000"
  db:
    image: postgres
    volumes:
     - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

---

## Image Optimization & Best Practices
- Multi-stage builds
- Use `.dockerignore`
- Minimal base images
- Avoid unnecessary layers

---

## Advanced Topics
- Docker Swarm & Kubernetes
- Health checks, secrets, configs
- Rolling updates

![Docker Registry Lifecycle](/mnt/data/a733ebae-4746-4be3-a25d-fa857411f7dd.png)

---

## CI/CD & Registries
- Automate builds with GitHub Actions
- Push to Docker Hub or GHCR

---

## Troubleshooting
- `docker logs`
- `docker system prune`
- `docker inspect`

---

## Further Reading
- [Docker Docs](https://docs.docker.com/)
- [Play with Docker](https://labs.play-with-docker.com/)
- [Docker Curriculum](https://docker-curriculum.com/)

