# Docker — From Basics to Advanced

Comprehensive guide to containerize, configure, and scale applications using Docker
![Alt Text](https://miro.medium.com/v2/resize:fit:2000/1*vQK4s0lOiK1ZkcXxFNIMDQ.png)
---

## Table of Contents
1. [Introduction](#introduction)  
2. [Why Docker?](#why-docker)  
3. [Core Concepts](#core-concepts)  
   - Images & Containers  
   - Docker Engine & CLI  
   - Container Registries
4.[Docker Images](#docker-images)
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
- **Consistency:** "Works on my machine" becomes redundant—containers encapsulate all dependencies. I think of it like everyone use different operating systems, packaged-environment,..., so even having the same code source does not ensure it run similarly on everyone machines.
- **Efficiency:** Containers share the host kernel, making them lighter than VMs.
- **Portability:** Run reliably across environments—dev, CI, production—with the same container.

---

## Core Concepts

### Images & Containers
- **Image**: Read-only template that defines what the container will run. Blueprint for containers
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
## Docker Images

To view all Docker images on your local system:

```bash
docker images
```
Use to list all of these image created
### Example Output:

```
REPOSITORY      TAG       IMAGE ID       CREATED          SIZE
hello-world     latest    acbd5h5ea8j51   3 months ago      13.3kB
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
- my-app-image: Name of the image we about to build
### Running a Container from an Image

```bash
docker run -d -p 8080:80 --name my-app my-app-image
```

- -d: This runs the image in detached mode, meaning the containers run on the background and we still can edit on the terminal
- 8080:8080: maps port 80 inside the container to port 8080 on the host
-  my-app: names the container `my-app`
-  my-app-image: Name of the image we built above

### Sample Output

```bash
$ docker ps
CONTAINER ID   IMAGE          COMMAND                  STATUS         PORTS                  NAMES
2b1f4d3fa8a3   my-app-image   "npm start"              Up 10 seconds  0.0.0.0:8080->80/tcp   my-app
```
- **IMAGE** – Name of the image
- **CONTAINER ID** – Unique identifier for the container
- **PORTS** – When the image was built
- **NAMES** – Name of the containers
### Docker Compose Example (Explained)
We used "docker compose" when we have many features within an app, each feature is run by a container, and we want to connect those containers, ensuring they can talk to each other well.

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

- **version**: Specifies the version of the Docker Compose file format
- **services**: Lists all the containers/services in the app
  - **web**: The main application service
    - **build**: Specifies how to build the Docker image (from Dockerfile)
    - **ports**: Maps port 3000 on the host to port 3000 in the container
    - **depends\_on**: Ensures `db` is started before `web`
  - **db**: Uses a prebuilt PostgreSQL image from Docker Hub
    - **environment**: Sets environment variables (username, password, db)
    - **volumes**: Stores persistent data
- **volumes**: Defines named volumes shared with containers

To start this stack:

```bash
docker-compose up --build
```

To stop and remove:

```bash
docker-compose down
```
When we run "docker compose", it would also created the images listed in the docker_yaml file with running containers.


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
- `docker ps`, `docker ps -a`: list of running containers
- `docker logs <container>`
- `docker exec -it <container> /bin/sh`: run into each container where we can dive in each: checking package isntalled, versions of package, etc
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

