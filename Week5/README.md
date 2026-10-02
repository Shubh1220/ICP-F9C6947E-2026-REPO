# Week 5 – CI/CD Pipeline Completion, Reliability Testing and Documentation

## 1. Overview

Week 5 focuses on completing, testing, refining, and documenting **Project 3: CI/CD Pipeline** from the DevOps Engineering Self-Learning Internship.

The objective is to ensure that the CI/CD pipeline is reliable, reproducible, and properly documented.

The pipeline automates:

1. Automated testing
2. Application build
3. Docker image build
4. Docker image publishing to Docker Hub

The implementation uses **GitHub Actions** as the CI/CD platform.

---

## 2. Project 3 Requirements

The internship task defines Project 3 as:

> **CI/CD Pipeline**

### Real-World Problem

Code changes need automated testing and deployment.

### Difficulty Level

Intermediate

### Key Skills and Tools

- GitHub Actions / GitLab CI
- Test Automation
- Build Stages
- Deployment Triggers

### Expected Outcome

A complete CI/CD pipeline with multiple stages.

This Week 5 work focuses on completing and validating these requirements.

---

## 3. Project Architecture

```text
                    Developer
                        |
                        v
                GitHub Repository
                        |
                        v
              GitHub Actions Trigger
                 /              \
                /                \
       Push to main          Pull Request
                \                /
                 \              /
                  v            v
              Automated Tests
                     |
                     | PASS
                     v
             Application Build
                     |
                     | PASS
                     v
               Docker Build
                     |
                     | PASS
                     v
             Docker Hub Push
                     |
                     v
             Published Image
```

---

## 4. Repository Structure

```text
ICP-F9C6947E-2026-REPO/
│
├── .github/
│   └── workflows/
│       └── ci-cd.yml
│
├── Week1/
│
├── Week2/
│
├── Week3/
│
├── Week4/
│   ├── app/
│   │   ├── Dockerfile
│   │   ├── package.json
│   │   ├── package-lock.json
│   │   ├── src/
│   │   ├── index.html
│   │   ├── nginx.conf
│   │   └── ...
│   │
│   ├── images/
│   ├── screenshots/
│   └── README.md
│
├── Week5/
│   └── README.md
│
└── README.md
```

---

## 5. CI/CD Workflow

The GitHub Actions workflow is located at:

```text
.github/workflows/ci-cd.yml
```

The pipeline contains three jobs:

```text
Test Application
       |
       v
Build Application
       |
       v
Build and Push Docker Image
```

Each stage depends on the successful completion of the previous stage.

---

## 6. Stage 1 – Automated Testing

The first pipeline stage is:

```text
Test Application
```

The workflow:

1. Checks out the repository.
2. Installs Node.js.
3. Restores the npm dependency cache.
4. Installs dependencies using `npm ci`.
5. Runs the automated test suite.

The application directory is:

```text
Week4/app
```

The test command is:

```bash
npm test
```

The project contains automated tests for:

- API key notice handling
- Movie data rendering
- API request failure handling

### Local Test Result

The test suite was executed locally.

Result:

```text
1 test file
3 tests passed
```

Therefore, the application test stage completed successfully.

---

## 7. Stage 2 – Application Build

The second stage is:

```text
Build Application
```

This job runs only after the testing job succeeds.

The production build command is:

```bash
npm run build
```

The application uses Vite for the production build.

The generated production directory is:

```text
dist/
```

The build process was successfully verified locally.

Example build result:

```text
vite build
✓ 33 modules transformed
✓ built successfully
```

The generated `dist/` directory is excluded from Git using `.gitignore`.

---

## 8. Stage 3 – Docker Image Build

The third stage creates the production Docker image.

The Dockerfile is located at:

```text
Week4/app/Dockerfile
```

The Docker image is built using Docker Buildx in GitHub Actions.

The pipeline uses:

```yaml
uses: docker/setup-buildx-action@v3
```

and:

```yaml
uses: docker/build-push-action@v6
```

The Docker build context is:

```text
./Week4/app
```

The application API key is supplied through a GitHub Actions secret during the Docker build.

---

## 9. Stage 4 – Docker Hub Publishing

After the Docker image is successfully built, the image is pushed to Docker Hub.

Docker Hub repository:

```text
opsshubh/streamflix-clone
```

The pipeline publishes two image tags.

### Latest Tag

```text
opsshubh/streamflix-clone:latest
```

### Commit SHA Tag

```text
opsshubh/streamflix-clone:<github-sha>
```

The commit SHA tag provides traceability between a Docker image and the Git commit that produced it.

---

## 10. GitHub Actions Secrets

Sensitive values are stored using GitHub Actions repository secrets.

The workflow uses:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
VITE_TMDB_API_KEY
```

These values are not stored directly inside the workflow file.

### Docker Hub Authentication

The workflow authenticates to Docker Hub using:

```yaml
with:
  username: ${{ secrets.DOCKERHUB_USERNAME }}
  password: ${{ secrets.DOCKERHUB_TOKEN }}
```

This prevents the Docker Hub credentials from being hard-coded into the repository.

---

## 11. API Key Handling

The application uses the TMDB API.

The API key is handled through an environment variable / GitHub Actions secret.

The pipeline passes the value as a Docker build argument:

```yaml
build-args: |
  VITE_TMDB_API_KEY=${{ secrets.VITE_TMDB_API_KEY }}
```

The actual secret value is not committed to GitHub.

The local `.env` file is excluded using `.gitignore`.

---

## 12. Pipeline Triggers

The workflow is triggered by two events.

### Push to Main

```yaml
on:
  push:
    branches:
      - main
```

This runs the pipeline whenever changes are pushed to the `main` branch.

### Pull Request

```yaml
on:
  pull_request:
    branches:
      - main
```

This runs the testing and build process when a pull request targets the `main` branch.

These triggers provide automated validation whenever code changes are introduced.

---

## 13. Job Dependencies

The workflow uses GitHub Actions job dependencies.

The build job contains:

```yaml
needs: test
```

Therefore:

```text
Test
  |
  | PASS
  v
Build
```

The Docker job contains:

```yaml
needs: build
```

Therefore:

```text
Build
  |
  | PASS
  v
Docker Build & Push
```

The complete dependency chain is:

```text
test
  |
  v
build
  |
  v
docker
```

If an earlier job fails, the dependent job does not continue.

---

## 14. Reliability Testing

The CI/CD pipeline was tested locally and through GitHub Actions.

The following areas were validated:

| Test | Result |
|---|---|
| Dependency installation | PASS |
| Automated tests | PASS |
| Production build | PASS |
| Docker image build | PASS |
| Docker container startup | PASS |
| Container health check | PASS |
| Application HTTP response | PASS |
| GitHub Actions test job | PASS |
| GitHub Actions build job | PASS |
| GitHub Actions Docker job | PASS |
| Docker Hub image publishing | PASS |

---

## 15. Local Application Testing

The application was tested using:

```bash
npm ci --no-audit --no-fund
```

followed by:

```bash
npm test
```

The result was:

```text
3 tests passed
```

The production build was tested using:

```bash
npm run build
```

The build completed successfully.

---

## 16. Docker Container Testing

The Docker image was built locally using:

```bash
docker build \
  --build-arg VITE_TMDB_API_KEY="" \
  -t streamflix-clone:week4 \
  Week4/app
```

The container was then started using a local port:

```bash
docker run -d \
  --name streamflix-week4 \
  -p 8090:80 \
  streamflix-clone:week4
```

The application was verified using:

```bash
curl http://localhost:8090/
```

The application returned the Streamflix HTML page successfully.

---

## 17. Docker Health Check

A Docker health check was configured in the application Dockerfile.

The health check uses:

```dockerfile
HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=5 \
    CMD wget -q -O- http://127.0.0.1:80/ || exit 1
```

The container successfully reached:

```text
healthy
```

This confirms that the web server inside the container was responding correctly.

---

## 18. Reliability Issue Identified and Fixed

During local Docker testing, the initial health check used:

```text
http://localhost:80/
```

The container application was reachable, but the Docker health check did not pass.

The health check was changed to:

```text
http://127.0.0.1:80/
```

After rebuilding the Docker image and restarting the container, the health status became:

```text
healthy
```

This demonstrated the importance of testing container health checks rather than only checking whether the container process is running.

---

## 19. Reliability Improvement

The health check was improved by using the loopback IP address:

```text
127.0.0.1
```

instead of:

```text
localhost
```

The final health check verifies that Nginx is actually serving the application from inside the container.

This provides an additional reliability check for the containerized application.

---

## 20. GitHub Actions Verification

The GitHub Actions workflow was successfully executed.

The pipeline contained the following jobs:

```text
✓ Test Application

✓ Build Application

✓ Build and Push Docker Image
```

All jobs completed successfully.

The successful execution confirms that the workflow can:

1. Checkout the repository.
2. Install dependencies.
3. Run automated tests.
4. Build the application.
5. Build the Docker image.
6. Authenticate with Docker Hub.
7. Push the Docker image.

---

## 21. Docker Hub Verification

The CI/CD pipeline successfully published the Docker image to:

```text
dockerhubusername/streamflix-clone
```

Published tags include:

```text
latest
```

and:

```text
<commit-sha>
```

The Docker Hub repository was verified after the successful GitHub Actions workflow.

The published image size was approximately:

```text
20.1 MB
```

---

## 22. Image Traceability

The pipeline publishes a commit-specific Docker image tag:

```text
${{ github.sha }}
```

For example:

```text
dockerhubusername/streamflix-clone:<commit-sha>
```

This provides a relationship between:

```text
Git Commit
     |
     v
GitHub Actions Run
     |
     v
Docker Image
     |
     v
Docker Hub Tag
```

This makes it possible to identify which source-code revision produced a particular image.

---

## 23. Reproducibility

The pipeline uses:

```bash
npm ci
```

instead of:

```bash
npm install
```

`npm ci` installs dependencies from the existing lock file.

The workflow also specifies:

```yaml
node-version: 20
```

This provides a consistent Node.js environment for GitHub Actions.

The Docker build uses a Dockerfile stored in the repository, allowing the image to be rebuilt from the same source configuration.

---

## 24. Security Practices

The following practices were used:

### Secrets

Sensitive credentials are stored in GitHub Actions Secrets.

### Docker Hub Token

The Docker Hub access token is not stored in the repository.

### API Key

The TMDB API key is supplied through a GitHub Actions secret.

### Environment Files

Local environment files are excluded from Git:

```gitignore
app/.env
.env
```

### Generated Files

Generated files such as:

```text
node_modules/
dist/
```

are excluded from the repository.

---

## 25. Git Ignore Configuration

The project uses `.gitignore` rules for generated files and secrets.

Important entries include:

```gitignore
node_modules/
dist/
app/dist/
*.log

app/.env
.env

dependency-check-report.*
trivy-*.txt
test-results.xml

.DS_Store
```

This helps prevent local secrets and generated files from being committed.

---

## 26. Complete CI/CD Flow

```text
                    Developer
                        |
                        v
                 GitHub Repository
                        |
              +---------+---------+
              |                   |
          Push to main       Pull Request
              |                   |
              +---------+---------+
                        |
                        v
               GitHub Actions
                        |
                        v
               Install Dependencies
                        |
                        v
                Automated Tests
                        |
                    PASS
                        |
                        v
               Application Build
                        |
                    PASS
                        |
                        v
                 Docker Build
                        |
                    PASS
                        |
                        v
              Docker Hub Login
                        |
                        v
               Docker Image Push
                        |
                        v
                  Docker Hub
                        |
              +---------+---------+
              |                   |
            latest             commit SHA
```

---

## 27. Final Pipeline Stages

| Stage | Technology | Purpose |
|---|---|---|
| Source | GitHub | Store application source code |
| Trigger | GitHub Actions | Start CI/CD workflow |
| Test | npm / automated tests | Validate application |
| Build | Vite | Create production build |
| Containerize | Docker | Package application |
| Registry | Docker Hub | Store Docker image |
| Traceability | Git SHA | Identify image source |

---

## 28. Week 5 Reliability Improvements

The following improvements were completed:

- Automated testing verified.
- Production build verified.
- Docker image build verified.
- Docker container startup verified.
- Docker health check tested.
- Health check issue identified and fixed.
- GitHub Actions workflow verified.
- Docker Hub publishing verified.
- Commit-based Docker image tagging verified.
- Repository secrets used for sensitive values.
- Generated files excluded from Git.
- CI/CD workflow documented.

---

## 29. Project 3 Requirement Mapping

The implementation maps to the internship Project 3 requirements as follows:

| Internship Requirement | Implementation |
|---|---|
| GitHub Actions / GitLab CI | GitHub Actions |
| Test Automation | npm automated tests |
| Build Stages | Test → Build → Docker |
| Deployment Triggers | Push and Pull Request triggers |
| Multiple Pipeline Stages | Separate GitHub Actions jobs |
| Complete CI/CD Pipeline | Automated test, build, Docker build and Docker Hub publishing |

---

## 30. Final Status

```text
Project 3: CI/CD Pipeline
Status: COMPLETED
```

### Completed

```text
✓ GitHub Actions workflow
✓ Automated testing
✓ Application build
✓ Docker image build
✓ Docker Hub authentication
✓ Docker image publishing
✓ Push trigger
✓ Pull request trigger
✓ Job dependencies
✓ Reliability testing
✓ Docker health check
✓ Health check issue resolution
✓ Commit-based image tagging
✓ Secret management
✓ Documentation
```

---

## 31. Week 5 Outcome

The CI/CD pipeline now provides an automated workflow for validating application changes, building the production application, creating a Docker image, and publishing the image to Docker Hub.

The pipeline was tested locally and through GitHub Actions.

The final workflow provides:

```text
Code Change
    ↓
Automated Test
    ↓
Application Build
    ↓
Docker Build
    ↓
Docker Hub Publish
```

This completes the implementation, reliability testing, refinement, and documentation work for **Project 3: CI/CD Pipeline**.

---

## 32. Conclusion

**Status: Week 5 completed the CI/CD project by validating the complete automated workflow and documenting the implementation**.

The final pipeline uses GitHub Actions to automate testing, application building, Docker image creation, and Docker Hub publishing.

The workflow also includes automated triggers, sequential job dependencies, secret management, health-check validation, and commit-based image traceability.

The completed CI/CD implementation satisfies the core requirements of **Project 3: CI/CD Pipeline**.
