# Docker — From Basics to Advanced



Here is how I learn about Docker from scratch!
![Alt Text](https://miro.medium.com/v2/resize:fit:2000/1*vQK4s0lOiK1ZkcXxFNIMDQ.png)
---

## Table of Contents

1. [Introduction](#introduction)
2. [Why Docker?](#why-docker)
3. [Core Concepts](#core-concepts)
   - Images & Containers
   - Docker Engine & CLI
   - Container Registries
4. [Docker Images](#docker-images)
5. [Basic Usage](#basic-usage)
   - Installation
   - Hello World
   - Pulling & Running Images
6. [Dockerfiles](#dockerfiles)
   - Anatomy of a Dockerfile
   - Example: Simple Web App
7. [Container Management](#container-management)
   - Inspecting
   - Logs
   - Exec
   - Cleanup
8. [Data Storage](#data-storage)
   - Volumes
   - Bind mounts
9. [Networking](#networking)
   - Network modes (bridge, host)
   - Docker networking commands
10. [Docker Compose](#docker-compose)
    - `docker-compose.yml`
    - Example: Multi-container stack
11. [Image Optimization & Best Practices](#image-optimization-and-best-practices)
12. [Advanced Topics](#advanced-topics)
13. [CI/CD & Registries](#cicd--registries)
14. [Troubleshooting](#troubleshooting)
15. [Further Reading](#further-reading)

---

## Introduction

Docker is a lightweight containerization platform that uses OS-level virtualization to package applications into portable containers.

---

## Why Docker?

- **Consistency:** "Works on my machine" becomes redundant—containers encapsulate all dependencies. This is useful when developers use different operating systems or system setups. Even with the same source code, results may differ unless the environment is packaged.
- **Efficiency:** Containers share the host kernel, making them lighter than VMs.
- **Portability:** Run reliably across environments—dev, CI, production—with the same container.

---

## Core Concepts

### Images & Containers

- **Image**: Read-only template that defines what the container will run. Think of it as a blueprint.
- **Container**: A runtime instance of an image—an executable package of software.
Think of dockerfile like a recipe, image is the pre-pared food (frozen one) made following the dockerfile recipe, and container is a ready-served food just by baking or cooking the image.
### Docker Engine & CLI

- The **daemon** `dockerd` manages containers.
- The **client** `docker` provides a command-line interface (CLI) to interact with Docker.

### Container Registries

- **Docker Hub** is the default public registry.
- You can also use private registries like GitHub Container Registry (GHCR).

### Docker Architecture

This diagram illustrates how developers create Dockerfiles to build Docker images, which are then pushed to Docker Hub. Later, different environments (e.g., staging, testing) pull these images and run them as containers.

![Docker Architecture](https://miro.medium.com/v2/resize:fit:1400/0*SPCr5zXp8jw9Mfk8.png)
---

## Docker Images

To view all Docker images on your local system:

```bash
docker images
```

### Example Output:

```
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
hello-world     latest    acbd5h5ea8j51   3 months ago     13.3kB
```

### Column Breakdown

- **REPOSITORY** – Name of the image (e.g., `hello-world`)
- **TAG** – Version label (default is `latest`)
- **IMAGE ID** – Unique identifier for the image
- **CREATED** – When the image was built
- **SIZE** – Disk space used by the image

### Building from a Dockerfile

If you have a Dockerfile, you can build a custom image:

```bash
docker build -t my-app-image .
```

- `my-app-image`: Name/tag of the image to build

### Running a Container from an Image

```bash
docker run -d -p 8080:80 --name my-app my-app-image
```

- `-d`: Run in detached mode (in background)
- `-p 8080:80`: Map host port 8080 to container port 80
- `--name my-app`: Assigns a name to the container
- `my-app-image`: The image to use for creating the container

### Sample Output

```bash
$ docker ps
CONTAINER ID   IMAGE          COMMAND       STATUS        PORTS                  NAMES
2b1f4d3fa8a3   my-app-image   "npm start"   Up 10 secs    0.0.0.0:8080->80/tcp   my-app
```

### Docker Compose Example (Explained)

When your application involves multiple services (e.g., web server + database), Docker Compose helps manage them together, ensuring they can talk clear to each other.

```yaml
version: '3.9'
services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    depends_on:
      - db

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

### Explanation:

- **version**: Docker Compose file format version
- **services**:
  - `web`: Custom-built Node app
    - `build`: Builds the image using Dockerfile in current context
    - `ports`: Maps container 3000 to host 3000
    - `depends_on`: Starts `db` before `web`
  - `db`: PostgreSQL database
    - `environment`: DB credentials and name
    - `volumes`: Mount persistent volume
- **volumes**: Defines named volume for data persistence

### To Start:

```bash
docker-compose up --build
```

### To Stop:

```bash
docker-compose down
```

> Running `docker-compose` will build the images and spin up multiple containers as described.


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


