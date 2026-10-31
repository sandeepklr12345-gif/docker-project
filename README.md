# Docker Mastery Learning Project 🐳

A hands-on project for learning Docker by containerizing the same small FastAPI application with two different image-building strategies. The project currently covers beginner and intermediate Docker concepts. It does not require AWS or publish images to a registry.

## Learning goals

- Understand Docker images, containers, base images, and port mapping.
- Build an application using a single-stage Dockerfile.
- Compare a general Ubuntu base image with a purpose-built Python image.
- Understand Docker layer caching and dependency installation.
- Explore a multi-stage Docker build.
- Test API behavior with Pytest.
- Use GitHub Actions to run tests and build Docker images automatically.

## Repository structure

```text
docker-project/
├── .github/
│   └── workflows/
│       └── ci.yml
├── phase-1/
│   ├── app/main.py
│   ├── Dockerfile
│   ├── requirements.txt
│   └── .dockerignore
├── phase-2/
│   ├── app/main.py
│   ├── Dockerfile
│   ├── requirements.txt
│   ├── requirements-dev.txt
│   ├── test_main.py
│   └── .dockerignore
├── .gitignore
└── README.md
```

## Prerequisites

- Docker Engine or Docker Desktop running.
- Python 3.12 for running the Phase 2 test suite locally.
- Git for version control.

Check the tools:

```bash
docker --version
python3 --version
git --version
```

## Phase 1: Beginner, single-stage build

Phase 1 starts from `ubuntu:22.04`, installs Python and pip, installs the application dependencies, copies the FastAPI source, and starts Uvicorn on port 8000.

Build the image from the repository root:

```bash
docker build -t docker-mastery:phase1 ./phase-1
```

Run it on host port 8001:

```bash
docker run --rm -d --name docker-phase1 -p 8001:8000 docker-mastery:phase1
```

Try the endpoints:

```bash
curl http://localhost:8001/
curl http://localhost:8001/health
curl http://localhost:8001/info
```

Interactive API docs: <http://localhost:8001/docs>

Stop the container:

```bash
docker stop docker-phase1
```

## Phase 2: Intermediate, multi-stage build

Phase 2 uses `python:3.12-slim`. The builder stage installs the Python dependencies, while the final runtime stage starts from a fresh Python image and copies in the installed packages and application code.

Build the image:

```bash
docker build -t docker-mastery:phase2 ./phase-2
```

Run it on host port 8002:

```bash
docker run --rm -d --name docker-phase2 -p 8002:8000 docker-mastery:phase2
```

Try the endpoints:

```bash
curl http://localhost:8002/
curl http://localhost:8002/health
curl http://localhost:8002/info
```

Interactive API docs: <http://localhost:8002/docs>

Stop the container:

```bash
docker stop docker-phase2
```

## API endpoints

Both phases expose the same endpoints so their container builds can be compared while keeping application behavior consistent.

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/` | Welcome message, phase, and running status |
| `GET` | `/health` | Health status and UTC timestamp |
| `GET` | `/info` | Application name, version, and phase metadata |

## Run the Phase 2 tests locally

From the repository root, create a virtual environment and install the development dependencies:

```bash
cd phase-2
python3 -m venv .venv
source .venv/bin/activate
python -m pip install -r requirements-dev.txt
python -m pytest -v
```

When finished:

```bash
deactivate
cd ..
```

## CI with GitHub Actions

The workflow at `.github/workflows/ci.yml` runs on pushes and pull requests targeting `main`.

It performs two jobs:

1. Installs the Phase 2 development dependencies and runs the Pytest suite.
2. If the tests pass, builds the Phase 1 and Phase 2 Docker images.

This is a **continuous integration (CI)** workflow. It does not push images to Docker Hub or Amazon ECR and does not deploy to AWS. No AWS credentials are required. GitHub-hosted runner availability and usage limits depend on your GitHub account and current GitHub policies.

## Compare the images

After building locally, inspect the image sizes:

```bash
docker image ls docker-mastery
```

Record the sizes and compare them. A multi-stage build can exclude build-only files from the final image, but the measured result depends on the base images, dependencies, and build configuration.

## Current scope and next learning steps

- [x] Phase 1: single-stage Docker build.
- [x] Phase 2: multi-stage Docker build.
- [x] Phase 2 API tests.
- [x] GitHub Actions CI configuration for tests and image builds.
- [ ] Add equivalent API tests for Phase 1.
- [ ] Run the application as a non-root user.
- [ ] Add and verify a Docker `HEALTHCHECK`.
- [ ] Add image security scanning.
- [ ] Explore Docker Compose and multi-container networking.
- [ ] Consider registry publishing and cloud deployment later, if resources are available.

## References

- [Docker documentation](https://docs.docker.com/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [GitHub Actions documentation](https://docs.github.com/en/actions)
- [FastAPI documentation](https://fastapi.tiangolo.com/)
- [Pytest documentation](https://docs.pytest.org/)
