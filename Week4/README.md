# CI/CD Pipeline with GitHub Actions

## Week 4 — Project 3: CI/CD Pipeline

This project implements an automated CI/CD pipeline for a React-based
StreamFlix application using GitHub Actions and Docker.

The pipeline automatically runs tests, builds the production application,
and builds a Docker image whenever changes are pushed to the `main` branch
or a pull request is opened against `main`.

---

## Project Objective

The objective of this project is to automate the software delivery process
so that code changes can be validated and packaged consistently.

The pipeline performs:

1. Automated testing
2. Production build
3. Docker image build
4. CI execution on GitHub-hosted runners

---

## Architecture

```text
Developer
    |
    v
GitHub Repository
    |
    | Push / Pull Request
    v
GitHub Actions
    |
    v
+------------------+
| Automated Tests  |
+------------------+
    |
    | Success
    v
+------------------+
| Production Build |
+------------------+
    |
    | Success
    v
+------------------+
| Docker Image     |
| Build            |
+------------------+
```

---

## Technology Stack

| Technology | Purpose |
|---|---|
| Git & GitHub | Source code management |
| GitHub Actions | CI/CD automation |
| Node.js 20 | Application build environment |
| npm | Dependency management |
| Vitest | Automated testing |
| React | Frontend application |
| Vite | Frontend build tool |
| Docker | Application containerization |
| Nginx | Production web server |

---

## Application

The application is **StreamFlix**, a React-based movie browsing application.

It uses:

- React
- Vite
- JavaScript
- CSS
- TMDB API
- Nginx
- Docker

The application includes automated tests for important application
behaviour, including API-key handling, movie rendering, and API error
handling.

---

## Repository Structure

```text
Week4/
├── .github/
│   └── workflows/
│       └── ci-cd.yml
├── app/
│   ├── src/
│   │   ├── api/
│   │   │   └── tmdb.js
│   │   ├── App.jsx
│   │   ├── App.css
│   │   ├── App.test.jsx
│   │   ├── config.js
│   │   ├── main.jsx
│   │   └── setupTests.js
│   ├── index.html
│   ├── package.json
│   ├── package-lock.json
│   ├── vite.config.js
│   ├── Dockerfile
│   ├── nginx.conf
│   ├── .dockerignore
│   └── .env.example
├── images/
│   ├── architecture.gif
│   └── pipeline-stages.gif
├── screenshots/
│   ├── aws-ec2.png
│   ├── docker-hub.png
│   ├── docker-ps.png
│   └── live-browsing.png
├── .gitignore
└── README.md
```

---

# CI/CD Pipeline

The GitHub Actions workflow is located at:

```text
.github/workflows/ci-cd.yml
```

The workflow is triggered by:

- Pushes to `main`
- Pull requests targeting `main`

---

## Pipeline Stages

```text
Push / Pull Request
        |
        v
   Checkout Code
        |
        v
    Test Stage
        |
        | Success
        v
   Build Stage
        |
        | Success
        v
 Docker Build Stage
```

### Stage 1 — Automated Testing

The pipeline:

1. Checks out the repository.
2. Sets up Node.js 20.
3. Uses the `app/package-lock.json` file for npm caching.
4. Installs dependencies using `npm ci`.
5. Runs the automated test suite.

Command:

```bash
npm test
```

The test stage must succeed before the build stage starts.

---

### Stage 2 — Production Build

The build stage runs after the test stage succeeds.

It:

1. Checks out the repository.
2. Sets up Node.js 20.
3. Installs dependencies.
4. Creates the production build.

Command:

```bash
npm run build
```

The generated production files are placed in:

```text
app/dist/
```

---

### Stage 3 — Docker Image Build

The Docker stage runs after the production build succeeds.

GitHub Actions uses Docker Buildx to build the application image.

The Docker build context is:

```text
./app
```

The image is tagged as:

```text
streamflix-clone:latest
```

The current CI workflow builds the image without pushing it to a registry.

---

# Docker Configuration

The application uses a multi-stage Dockerfile.

### Build Stage

```text
Node.js
   |
   v
Install dependencies
   |
   v
npm run build
   |
   v
Production files
```

### Runtime Stage

```text
Production files
      |
      v
Nginx Alpine
      |
      v
Port 80
```

This separates the application build environment from the lightweight
production runtime.

---

# Local Testing

## 1. Install Dependencies

```bash
cd app
npm ci --no-audit --no-fund
```

## 2. Run Automated Tests

```bash
npm test
```

## 3. Create Production Build

```bash
npm run build
```

## 4. Build Docker Image

From the `app` directory:

```bash
docker build \
  --build-arg VITE_TMDB_API_KEY="" \
  -t streamflix-clone:week4 .
```

## 5. Run the Container

```bash
docker run -d \
  --name streamflix-week4 \
  -p 8090:80 \
  streamflix-clone:week4
```

Open:

```text
http://localhost:8090
```

## 6. Check Container Health

```bash
docker inspect \
  --format='{{.State.Health.Status}}' \
  streamflix-week4
```

Expected result:

```text
healthy
```

## 7. Stop and Remove the Test Container

```bash
docker stop streamflix-week4
docker rm streamflix-week4
```

---

# GitHub Actions Workflow

The workflow contains three jobs:

```text
test
  |
  v
build
  |
  v
docker
```

The dependency relationship is:

```text
build needs test
docker needs build
```

Therefore, a failed test prevents the production build from running, and
a failed build prevents the Docker image from being built.

---

# Secrets and Environment Variables

The workflow references:

```text
VITE_TMDB_API_KEY
```

through a GitHub Actions secret.

The value is not stored directly in the workflow file.

Local environment configuration uses:

```text
app/.env
```

The `.env` file is excluded from Git using `.gitignore`.

A template is provided as:

```text
app/.env.example
```

Never commit real API keys or other credentials to the repository.

---

# Reliability Improvement

During local Docker testing, the initial Docker health check used:

```text
http://localhost:80/
```

The container was running Nginx correctly, but the health check failed.

The health check was changed to:

```text
http://127.0.0.1:80/
```

After rebuilding the image and restarting the container, the container
reported:

```text
healthy
```

This demonstrates troubleshooting and refinement of the container
health-check configuration.

---

# Testing Results

Local validation completed for the Week4 application:

| Test | Result |
|---|---|
| `npm ci` | Passed |
| `npm test` | Passed |
| `npm run build` | Passed |
| Docker image build | Passed |
| Docker container startup | Passed |
| Application HTTP response | Passed |
| Docker health check | Passed |

The automated test suite contains three tests covering:

- API key notice behaviour
- Movie rendering with an API key
- TMDB request error handling

---

# CI/CD Benefits

This pipeline provides:

- Automated testing for code changes
- Consistent production builds
- Automated Docker image creation
- Dependency-based job execution
- GitHub-hosted CI runners
- Reduced manual validation
- Early detection of application errors

---

# Future Improvements

The current pipeline focuses on the CI portion of the CI/CD workflow.

Future stages can extend it with:

```text
Test
  |
  v
Build
  |
  v
Docker Build
  |
  v
Docker Push
  |
  v
Deployment
  |
  v
Health Check
```

Possible future integrations include:

- Docker Hub image publishing
- AWS EC2 deployment
- Automated deployment triggers
- Post-deployment health checks
- Container image security scanning

---

# Project Status

**Project:** Project 3 — CI/CD Pipeline

**Platform:** GitHub Actions

**Application:** StreamFlix

**Status:** Week 4 development and CI implementation completed

**Next:** Week 5 reliability testing, refinement, documentation, and
completion of the CI/CD pipeline.
