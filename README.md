# 🐳 Docker Mastery: A Hands-On Learning Project

A practical, phase-by-phase journey into Docker, containerization, and modern application deployment.

This project demonstrates how to package, build, and run a Python FastAPI application using different Docker strategies. It starts with a beginner-friendly single-stage build and progresses to a multi-stage build using a lightweight Python base image.

The goal is to understand not just **how to use Docker, but why different containerization strategies matter.**

---

## 📌 Project Overview

**Project Name:** Docker Mastery Demo API  
**Language:** Python  
**Framework:** FastAPI  
**Containerization:** Docker  
**API Server:** Uvicorn  

The project contains two learning phases. Both implement the same basic API functionality while using different Docker image-building strategies.

### Learning Objectives

- Understand Docker images and containers.
- Learn how Dockerfiles define application environments.
- Understand the role of base images and dependency installation.
- Explore Docker layer caching.
- Compare single-stage and multi-stage builds.
- Learn how to expose application ports.
- Test API endpoints and verify container behavior.
- Understand the importance of image size, security, and reproducibility.

---

## 📂 Project Structure

```text
docker-project/
│
├── phase-1/
│   ├── app/
│   │   └── main.py
│   ├── requirements.txt
│   ├── .dockerignore
│   └── Dockerfile
│
├── phase-2/
│   ├── app/
│   │   └── main.py
│   ├── requirements.txt
│   ├── requirements-dev.txt
│   ├── test_main.py
│   ├── .dockerignore
│   └── Dockerfile
│
├── .gitignore
└── README.md
```

**Note:** The Dockerfiles should be named `Dockerfile` with a capital `D` to support the standard Docker build commands shown below.

---

## 🟢 Phase 1: Beginner

### Objective

Understand the fundamentals of containerizing a Python application using a single-stage Docker build.

### Technologies

- Python
- FastAPI
- Uvicorn
- Ubuntu 22.04
- Docker

### How It Works

Phase 1 uses `ubuntu:22.04` as its base image.

The Docker build process:

1. Starts with an Ubuntu base image.
2. Installs Python 3 and pip.
3. Sets `/app` as the working directory.
4. Copies the dependency requirements file.
5. Installs the required Python packages.
6. Copies the application source code.
7. Exposes port `8000`.
8. Starts the FastAPI application using Uvicorn.

### Build the Docker Image

From the project root:

```bash
docker build -t docker-mastery:phase1 ./phase-1
```

### Run the Container

```bash
docker run --rm -d \
  --name docker-phase1 \
  -p 8001:8000 \
  docker-mastery:phase1
```

The application will be accessible at:

```text
http://localhost:8001
```

### View the Logs

```bash
docker logs docker-phase1
```

### Stop the Container

```bash
docker stop docker-phase1
```

### Concepts Learned

- Single-stage Docker builds
- Base images
- Installing system packages
- Installing Python dependencies
- Working directories
- Docker image layers
- Port mapping
- Running applications inside containers

### Key Takeaway

A single-stage build is straightforward to understand because the operating system, Python installation, dependencies, and application all reside in the same final image.

However, the final image may contain more components than the application needs at runtime.

---

## 🔵 Phase 2: Intermediate

### Objective

Understand how multi-stage Docker builds and purpose-built Python images can simplify the containerization process.

### Technologies

- Python 3.12
- FastAPI
- Uvicorn
- `python:3.12-slim`
- Docker multi-stage builds
- Pytest
- HTTPX

### How It Works

Phase 2 uses `python:3.12-slim` as its base image and separates the build into two stages.

#### Stage 1: Builder

The builder stage:

1. Uses the Python slim image.
2. Sets `/app` as the working directory.
3. Copies `requirements.txt`.
4. Installs Python dependencies into the user's local package directory.

#### Stage 2: Runtime

The runtime stage:

1. Starts from a fresh Python slim image.
2. Copies the installed Python packages from the builder stage.
3. Copies the application source code.
4. Configures the executable search path and Python output behavior.
5. Exposes port `8000`.
6. Starts the application using Uvicorn.

The final stage does not inherit the entire filesystem of the builder stage. Only the explicitly copied files are carried forward.

This can help reduce unnecessary build components in the final image.

### Build the Docker Image

From the project root:

```bash
docker build -t docker-mastery:phase2 ./phase-2
```

### Run the Container

```bash
docker run --rm -d \
  --name docker-phase2 \
  -p 8002:8000 \
  docker-mastery:phase2
```

The application will be accessible at:

```text
http://localhost:8002
```

### View the Logs

```bash
docker logs docker-phase2
```

### Stop the Container

```bash
docker stop docker-phase2
```

### Concepts Learned

- Multi-stage Docker builds
- Builder and runtime stages
- Purpose-built base images
- Dependency isolation
- Copying artifacts between stages
- Environment variables
- Image optimization
- Automated API testing

### Key Takeaway

Multi-stage builds allow the build environment to be separated from the runtime environment. They are especially useful when the build process requires tools that are unnecessary when running the finished application.

---

## 🔗 API Endpoints

Both phases expose the same three endpoints, making their behavior easy to compare.

| Endpoint | Method | Purpose |
|---|---|---|
| `/` | GET | Returns a welcome message, phase, and application status |
| `/health` | GET | Returns the health status and a UTC timestamp |
| `/info` | GET | Returns application metadata, version, and phase |

### Example Responses

#### 1. Root Endpoint

Request:

```http
GET /
```

Phase 1 response:

```json
{
  "message": "Welcome to the Docker Mastery Demo API",
  "phase": "1 - beginner",
  "status": "running"
}
```

Phase 2 returns the same welcome message and status, with the phase set to `2 - intermediate`.

#### 2. Health Endpoint

Request:

```http
GET /health
```

Example response:

```json
{
  "status": "healthy",
  "timestamp": "2026-01-01T10:00:00+00:00"
}
```

The timestamp is generated dynamically, so the actual value will differ.

#### 3. Information Endpoint

Request:

```http
GET /info
```

Phase 1 reports application version `1.0.0`.

Phase 2 reports application version `1.1.0`.

Both identify the application as `docker-mastery-demo`.

---

## 🧪 Testing the Application

### Access the API Documentation

FastAPI automatically provides interactive API documentation.

For Phase 1:

```text
http://localhost:8001/docs
```

For Phase 2:

```text
http://localhost:8002/docs
```

You can use the interface to send requests to the endpoints and inspect their responses.

### Run the Phase 2 Test Suite

Phase 2 includes a basic automated test suite using Pytest and FastAPI's `TestClient`.

Navigate to the Phase 2 directory:

```bash
cd phase-2
```

Create and activate a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Install the development dependencies:

```bash
python -m pip install -r requirements-dev.txt
```

Run the tests:

```bash
python -m pytest -v
```

The existing tests verify that:

- The root endpoint returns HTTP 200 and reports a running status.
- The health endpoint returns HTTP 200 and reports a healthy status.
- The information endpoint returns HTTP 200 and identifies the application.

Return to the project root when finished:

```bash
deactivate
cd ..
```

---

## 📊 Phase Comparison

| Feature | Phase 1 | Phase 2 |
|---|---|---|
| Difficulty | Beginner | Intermediate |
| Base image | `ubuntu:22.04` | `python:3.12-slim` |
| Python installation | Installed manually | Included in base image |
| Build strategy | Single-stage | Multi-stage |
| Dependency installation | `pip3 install` | `pip install --user` |
| Runtime image | Contains the single-stage environment | Built from a fresh Python image |
| Application framework | FastAPI | FastAPI |
| API server | Uvicorn | Uvicorn |
| Application port | 8000 | 8000 |
| Automated tests | Not included in the supplied Phase 1 files | Pytest tests included |

**Important:** The actual image sizes and build times should be measured rather than assumed. A multi-stage build does not automatically guarantee a smaller image in every situation.

---

## 🔍 Useful Docker Commands

### List Running Containers

```bash
docker ps
```

### List All Containers

```bash
docker ps -a
```

### List Project Images

```bash
docker image ls docker-mastery
```

### Inspect an Image

```bash
docker image inspect docker-mastery:phase1
```

For Phase 2:

```bash
docker image inspect docker-mastery:phase2
```

### View Container Logs

```bash
docker logs docker-phase1
```

### Inspect Container Details

```bash
docker inspect docker-phase1
```

### Remove an Image

Stop and remove any container using the image before attempting to remove it.

```bash
docker image rm docker-mastery:phase1
```

---

## 🛠️ Prerequisites

Before running the project, ensure you have:

- Python 3 installed for local development.
- Docker Engine or Docker Desktop installed and running.
- Git installed if cloning the repository.

Verify the installations:

```bash
python3 --version
docker --version
git --version
```

Check whether Docker is running:

```bash
docker info
```

---

## 🚀 Future Improvements

The following improvements are potential next steps for this learning project:

- [ ] Add a root README and phase-specific documentation.
- [ ] Compare actual image sizes and build times.
- [ ] Add automated tests for Phase 1.
- [ ] Add Docker health checks.
- [ ] Run the application as a non-root user.
- [ ] Improve image security and dependency management.
- [ ] Explore Docker volumes and persistent storage.
- [ ] Introduce Docker networking.
- [ ] Add Docker Compose for multi-service applications.
- [ ] Add GitHub Actions for automated testing.
- [ ] Document troubleshooting steps and common Docker errors.

These are planned learning opportunities, not claims that the features are already implemented.

---

## 🎯 Learning Philosophy

This project follows a simple principle:

**Build it. Run it. Test it. Understand it. Improve it.**

Each phase should introduce a concept, demonstrate it through working code, and explain the reasoning behind the implementation.

The ultimate goal is to move beyond memorizing Docker commands and develop the ability to build, debug, and maintain containerized applications independently.

---

## 📚 References

- [Docker Documentation](https://docs.docker.com/)
- [Docker Get Started](https://docs.docker.com/get-started/)
- [Dockerfile Reference](https://docs.docker.com/reference/dockerfile/)
- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Pytest Documentation](https://docs.pytest.org/)

---

**Status:** Active learning project  
**Current phases:** Phase 1 and Phase 2  
**Focus:** Docker fundamentals, containerization, image-building strategies, and application testing
