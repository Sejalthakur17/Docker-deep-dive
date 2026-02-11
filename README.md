# Docker-deep-dive

## The Problem Docker Solves
Docker solves the problem of environment inconsistency in software development and deployment.

Before Docker:
Applications worked on a developer’s machine but failed in production.
Different OS versions and dependency versions caused conflicts.
Virtual Machines were used for isolation, but they were heavy, slow, and resource-intensive.
Deployment required complex manual setup on every server.

What Docker Does:
Packages the application with all its dependencies into a container.
Ensures the app runs the same in development, testing, and production.
Provides lightweight isolation without needing a full operating system.
Makes applications portable — build once, run anywhere.

## Virtual Machines vs Docker :
Virtual Machines (VMs) and Docker Containers both provide isolation, but they work differently.

Virtual Machines :
Use hardware-level virtualization via a hypervisor.
Each VM runs a full operating system.
Heavy (GBs in size) and slower to start.
Consume more CPU and memory.
Provide strong isolation.

Docker Containers :
Use OS-level virtualization.
Share the host OS kernel.
Lightweight (MBs in size) and start in seconds.
Use fewer resources.
Ideal for microservices and cloud-native apps.

## Docker Architecture :
What Gets Installed When Docker Is Installed?
When you install Docker, several components are installed that work together to build and run containers.

Core Components Installed :
1. Docker CLI (Client) :
   The docker command you use in terminal.
   Sends instructions to Docker Daemon.
2. Docker Daemon (dockerd) :
   Background service that manages containers, images, networks, and volumes.
3. Docker Engine :
   Combination of CLI + Daemon + REST API.
   Core platform that runs Docker.
4. Containerd :
   Manages container lifecycle (start, stop, pull images).
5. runc :
   Low-level runtime that actually creates and runs containers.
6. Networking & Storage Drivers
   Default bridge network
   Image layering system (e.g., overlay2)
7. (Mac/Windows Only)
   Lightweight Linux VM (since containers require Linux kernel)

Execution Flow :
Docker CLI → Docker Daemon → containerd → runc → Container

## Dockerfile Deep Dive :
1. Base Image : Uses Node.js v18 with a lightweight Alpine Linux base image.
FROM node:18-alpine

2. Set Working Directory : Sets /app as the working directory inside the container.
WORKDIR /app

3. Copy dependency files : Copies dependency files to install packages efficiently.
COPY package*.json ./

4. Install dependencies : Installs all required Node.js dependencies during build time.
RUN npm install

5. Copy application code : Copies the rest of your application code into the container.
COPY . .

6. Expose application port : Declares that the app runs on port 3000.
EXPOSE 3000

7. Default startup command : Starts the application when the container runs.
CMD ["npm", "start"]

## Key Docker Commands :
docker build → Build image
docker run → Run container (docker run -p 3000:3000 myapp)
docker ps → List containers
docker stop → Stop container
docker logs → View logs
docker exec → Enter container
docker images → List images

## Docker Networking :
1. Bridge (Default) :
   Default network for containers on a single host.
   Containers get private IP addresses.
   Containers communicate using container names.
   Most commonly used network type.
2. Host :
   Container shares the host’s network stack.
   No network isolation.
   Faster performance (no NAT).
3. None :
   Disables networking completely.
   Container cannot access internet or other containers.
4. Overlay :
   Connects containers across multiple hosts.
   Used in Docker Swarm for distributed systems.

## Volumes & Persistence :
By default, Docker containers are ephemeral, meaning if a container is deleted, its data is lost.

Why Volumes Are Needed :
To store database data
To keep uploaded files
To preserve logs
To retain application data

Types of Docker Storage :
1. Named Volumes :
   Managed by Docker
   Best for production and databases
   Data persists even after container deletion
2. Bind Mounts :
   Map a host directory to a container
   Commonly used in development
3. Anonymous Volumes :
   Automatically created
   Harder to manage

## Docker compose :
What is Docker Compose?
Docker Compose is a tool used to define and manage multi-container Docker applications using a single YAML file.

Instead of running multiple docker run commands, you define everything in one file:
docker-compose.yml

With Compose:
docker compose up

## Docker Compose

Basic docker-compose.yml:

```yaml
version: "3.9"

services:
  app:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db

  db:
    image: mysql:8
    environment:
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
```


## Key Sections Explained

- **version** : Specifies the Docker Compose file format version.

- **services** : Defines the containers that make up your application.

- **build** : Builds the image from a Dockerfile.

- **image** : Uses an image from Docker Hub.

- **ports** : Maps host port → container port.

- **environment** : Sets environment variables inside the container.

- **depends_on** : Ensures one service starts before another.

- **volumes** : Defines persistent storage.
