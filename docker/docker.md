# Docker Guide

## Table of Contents
1. [What is Docker?](#what-is-docker)
2. [Dockerfile](#dockerfile)
3. [Docker Compose](#docker-compose)
4. [Why Need to Use Docker?](#why-need-to-use-docker)
5. [When to Use Docker](#when-to-use-docker)
6. [How to Write Docker Files](#how-to-write-docker-files)
7. [Setup Examples](#setup-examples)

---

## What is Docker?

Docker is an open-source platform designed to simplify the process of creating, deploying, and running applications by using containerization. Containers are lightweight, standalone, executable packages that include everything needed to run a piece of software: code, runtime, libraries, system tools, and system settings.

### Key Concepts:
- **Container**: A lightweight, standalone executable package that includes everything needed to run a piece of software
- **Image**: A read-only template that contains the application and its dependencies
- **Registry**: A service that stores and distributes Docker images
- **Docker Engine**: The client-server application that includes the daemon, REST API, and CLI

---

## Dockerfile

A Dockerfile is a text document that contains all the commands to assemble an image. It's the foundation of Docker image creation.

### Basic Structure:
```dockerfile
# Syntax: INSTRUCTION arguments

# Base image
FROM ubuntu:20.04

# Set working directory
WORKDIR /app

# Copy files
COPY . .

# Install dependencies
RUN apt-get update && apt-get install -y python3

# Environment variables
ENV PYTHONUNBUFFERED=1

# Expose port
EXPOSE 8000

# Run command
CMD ["python3", "app.py"]
```

### Common Instructions:
- `FROM`: Set the base image
- `WORKDIR`: Set the working directory
- `COPY`: Copy files from host to container
- `ADD`: Similar to COPY but with URL support and extraction
- `RUN`: Execute commands in the container
- `CMD`: Set default command to run when container starts
- `ENTRYPOINT`: Set executable for container
- `EXPOSE`: Inform Docker that container listens on specified network ports
- `ENV`: Set environment variables
- `VOLUME`: Create a mount point for named volumes
- `USER`: Set user name or UID
- `ARG`: Define build-time variables
- `ONBUILD`: Configuration that is executed when the image is used as a base

---

## Docker Compose

Docker Compose is a tool for defining and running multi-container Docker applications using a `docker-compose.yml` file.

### Key Features:
- Define multi-container applications as a single service
- Manage start, stop, and build order
- Scale services
- Volume management
- Networking configuration

---

## Why Need to Use Docker?

### 1. **Consistency Across Environments**
- Eliminates "it works on my machine" problem
- Same environment from development to production

### 2. **Isolation**
- Applications run in isolated containers
- Prevents conflicts between dependencies
- Separate environments for different services

### 3. **Portability**
- Run anywhere Docker is installed
- Cloud, on-premise, local development
- No platform-specific configuration

### 4. **Scalability**
- Easy to scale applications horizontally
- Quick deployment and scaling
- Efficient resource utilization

### 5. **Version Control**
- Images can be versioned
- Rollback to previous versions easily
- Consistent deployment history

### 6. **Resource Efficiency**
- Lightweight compared to virtual machines
- Faster startup times
- Lower resource consumption

---

## When to Use Docker

### Use Docker When:
- **Microservices architecture**: Each service in its own container
- **Web applications**: Multiple services (app, database, cache, etc.)
- **Development environments**: Ensure consistent development setup
- **CI/CD pipelines**: Standardize build and deployment processes
- **Legacy applications**: Modernize without rewriting code
- **Multi-cloud deployment**: Deploy across different cloud providers

### Don't Use Docker When:
- **Simple scripts**: Single-purpose, small scripts
- **Heavy GUI applications**: May require display integration
- **Kernel-level development**: Need direct kernel access
- **Very simple applications**: Overhead may not be justified
- **Performance-critical applications**: May have overhead

---

## How to Write Docker Files

### Best Practices:

1. **Use specific base images** (e.g., `python:3.9` instead of `python:latest`)
2. **Minimize layers** by combining commands
3. **Leverage build cache** by ordering commands properly
4. **Use .dockerignore** to exclude unnecessary files
5. **Run as non-root user** when possible
6. **Use multi-stage builds** for optimized final images

### Dockerignore Example:
```
.git
node_modules
npm-debug.log
Dockerfile
.dockerignore
```

---

## Setup Examples

### Example 1: Python Web Application

#### `Dockerfile`:
```dockerfile
 # Use Python 3.9 slim image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy requirements first for better caching
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy application code
COPY . .

# Expose port
EXPOSE 8000

# Run as non-root user
RUN useradd -m -u 1000 appuser && chown -R appuser:appuser /app
USER appuser

# Run the application
CMD ["python", "-m", "uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

#### `docker-compose.yml`:
```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8000:8000"
    volumes:
      - .:/app
      - /app/__pycache__
    environment:
      - PYTHONUNBUFFERED=1
    depends_on:
      - postgres

  postgres:
    image: postgres:13
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```

#### `requirements.txt`:
```
fastapi
uvicorn[standard]
psycopg2-binary
```

### Example 2: Node.js Application

#### `Dockerfile`:
```dockerfile
# Use Node.js 18 alpine image
FROM node:18-alpine

# Create app directory
WORKDIR /usr/src/app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application code
COPY . .

# Create non-root user
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001

# Change ownership
USER nextjs

# Expose port
EXPOSE 3000

# Start the application
CMD ["npm", "start"]
```

#### `docker-compose.yml`:
```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    volumes:
      - .:/usr/src/app
      - /usr/src/app/node_modules
    environment:
      - NODE_ENV=development
    depends_on:
      - redis

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

volumes:
  redis_data:
```

### Example 3: Multi-stage Build for Production

#### `Dockerfile`:
```dockerfile
# Build stage
FROM node:18-alpine as builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

### Common Docker Commands:

#### Build an image:
```bash
docker build -t myapp:1.0 .
```

#### Run a container:
```bash
docker run -d -p 8000:8000 myapp:1.0
```

#### Using Docker Compose:
```bash
# Build and start all services
docker-compose up -d

# View logs
docker-compose logs -f

# Stop all services
docker-compose down

# Rebuild and start
docker-compose up -d --build
```

#### Container management:
```bash
# List running containers
docker ps

# List all containers
docker ps -a

# Stop container
docker stop container_id

# Remove container
docker rm container_id

# Remove image
docker rmi image_name
```

This Docker guide covers the essential concepts and provides practical examples to help you get started with containerization. Docker is a powerful tool for modern development and deployment workflows.