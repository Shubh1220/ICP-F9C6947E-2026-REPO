# Week 6 – Portfolio and Final Submission

## 1. Overview

Week 6 is the final phase of the DevOps Engineering Self-Learning Internship.

The objective of this week is to organize the completed projects into a professional portfolio, document the development and testing process, prepare operational runbooks, and prepare the final GitHub repository for submission.

The Week 6 deliverables are:

- Process documentation
- Portfolio presentation
- Minimum 2 completed projects
- GitHub links
- Runbooks

---

## 2. Internship Portfolio

The internship portfolio contains two completed DevOps projects:

```text
Project 1
Dockerized 3-Tier Application Deployment

Project 2
CI/CD Pipeline
```

Both projects include implementation, testing, documentation, and reliability validation.

---

# 3. Project 1 – Dockerized 3-Tier Application Deployment

## 3.1 Project Overview

The first project is a Dockerized 3-tier application consisting of a frontend, backend API, and MySQL database.

The application architecture is:

```text
                    User
                      |
                      v
                  Frontend
                      |
                      v
                 Backend API
                      |
                      v
                 MySQL Database
```

Nginx is used as part of the application architecture for HTTP handling and reverse proxy functionality.

---

## 3.2 Technologies Used

- Linux
- Git
- GitHub
- Docker
- Docker Compose
- Nginx
- Python
- Flask
- MySQL
- Shell Scripting
- AWS

---

## 3.3 Project Components

The application contains:

```text
Frontend
Backend API
MySQL Database
Nginx
Docker Network
Persistent Database Volume
```

Docker Compose is used to manage the application services.

The services communicate through a dedicated Docker network.

---

## 3.4 Containerization

The frontend and backend components were containerized using Dockerfiles.

Docker Compose was used to define and manage the multi-container application.

The configuration includes:

- Container services
- Service networking
- Environment variables
- Health checks
- Persistent storage
- Port configuration
- Service dependencies

---

## 3.5 Nginx Reverse Proxy

Nginx was configured as part of the application architecture.

The reverse proxy provides an entry point for the application and forwards requests to the appropriate application service.

The architecture is:

```text
Client
  |
  v
Nginx
  |
  +---------> Frontend
  |
  +---------> Backend API
```

---

## 3.6 Database Persistence

MySQL uses persistent Docker storage.

The database volume ensures that database data is not lost when the application containers are recreated.

The project was tested by stopping and restarting the application stack.

---

## 3.7 Health Checks

Health checks were configured for the application services.

The backend health endpoint was tested successfully.

Example response:

```json
{
  "database": "up",
  "status": "ok"
}
```

The health checks provide an additional mechanism for verifying service availability.

---

## 3.8 Docker Image Optimization

The backend Docker image was optimized using a multi-stage Docker build.

The original image size was approximately:

```text
396 MB
```

The optimized image size was approximately:

```text
223 MB
```

Approximate reduction:

```text
43.7%
```

The optimization reduced unnecessary content in the final runtime image.

---

## 3.9 Project Testing

The application was tested through multiple stages.

### Configuration Validation

The Docker Compose configuration was validated before deployment.

### Application Availability

The frontend was accessed through the configured HTTP port.

### Backend Health

The backend health endpoint was tested.

### Database Connectivity

The backend was verified to communicate with the MySQL database.

### Service Restart

The backend service was restarted to verify recovery.

### Full Stack Recovery

The complete application stack was stopped and started again.

### Persistent Storage

The database volume was verified to confirm persistent storage.

---

# 4. Project 2 – CI/CD Pipeline

## 4.1 Project Overview

The second project implements a multi-stage CI/CD pipeline using GitHub Actions.

The pipeline automates application testing, application building, Docker image creation, and Docker Hub publishing.

The overall workflow is:

```text
GitHub Repository
        |
        v
GitHub Actions
        |
        v
Automated Tests
        |
        v
Application Build
        |
        v
Docker Build
        |
        v
Docker Hub Push
```

---

## 4.2 Technologies Used

- Git
- GitHub
- GitHub Actions
- Node.js
- npm
- Vite
- Docker
- Docker Buildx
- Docker Hub

---

## 4.3 CI/CD Pipeline Stages

The pipeline contains three main jobs:

```text
Test Application
       |
       v
Build Application
       |
       v
Build and Push Docker Image
```

Each dependent stage runs after the previous stage succeeds.

---

# 5. Automated Testing

The first CI/CD stage runs automated application tests.

The application dependencies are installed using:

```bash
npm ci --no-audit --no-fund
```

The test suite is executed using:

```bash
npm test
```

The local test result was:

```text
3 tests passed
```

The tests cover application behavior including:

- API key notice handling
- Movie data rendering
- API request failure handling

---

# 6. Application Build

The second stage builds the production application.

The production build command is:

```bash
npm run build
```

The application uses Vite for the production build.

The production output is generated in:

```text
dist/
```

The build completed successfully during local validation and through the CI/CD workflow.

---

# 7. Docker Image Build

The third stage creates the production Docker image.

The application Dockerfile is located at:

```text
Week4/app/Dockerfile
```

Docker Buildx is used in GitHub Actions for building the image.

The Docker build context is:

```text
./Week4/app
```

---

# 8. Docker Hub Publishing

After the Docker image is successfully built, it is published to Docker Hub.

Docker Hub repository:

```text
opsshubh/streamflix-clone
```

The pipeline publishes:

```text
latest
```

and a commit-specific image tag.

The commit-specific tag provides traceability between the source-code commit and the resulting Docker image.

---

# 9. Pipeline Triggers

The GitHub Actions workflow supports:

### Push Trigger

```yaml
on:
  push:
    branches:
      - main
```

The pipeline runs when changes are pushed to the `main` branch.

### Pull Request Trigger

```yaml
on:
  pull_request:
    branches:
      - main
```

The workflow also runs when a pull request targets the `main` branch.

---

# 10. CI/CD Job Dependencies

The workflow uses job dependencies.

The build job depends on the test job:

```yaml
needs: test
```

The Docker job depends on the build job:

```yaml
needs: build
```

Therefore:

```text
Test
 |
 | PASS
 v
Build
 |
 | PASS
 v
Docker Build & Push
```

If an earlier job fails, the dependent job does not continue.

---

# 11. GitHub Actions Secrets

Sensitive values are stored using GitHub Actions repository secrets.

The workflow uses:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
VITE_TMDB_API_KEY
```

The secret values are not stored directly in the repository.

Docker Hub authentication uses:

```yaml
with:
  username: ${{ secrets.DOCKERHUB_USERNAME }}
  password: ${{ secrets.DOCKERHUB_TOKEN }}
```

The TMDB API key is supplied through the GitHub Actions secret during the Docker build.

---

# 12. Docker Health Check

A Docker health check was configured for the application.

The final health check uses:

```dockerfile
HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=5 \
    CMD wget -q -O- http://127.0.0.1:80/ || exit 1
```

During testing, the initial health check using:

```text
http://localhost:80/
```

did not pass as expected.

The check was changed to:

```text
http://127.0.0.1:80/
```

After rebuilding and restarting the container, the container reached:

```text
healthy
```

This issue and resolution were documented as part of reliability testing.

---

# 13. CI/CD Reliability Testing

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
| Docker Hub publishing | PASS |

---

# 14. Complete CI/CD Architecture

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

# 15. Security Practices

The projects follow basic DevOps security practices.

## Secrets

Sensitive values are stored outside the source code.

## Docker Hub Authentication

The Docker Hub token is stored as a GitHub Actions secret.

## Application API Key

The TMDB API key is stored as a GitHub Actions secret.

## Environment Files

Local environment files are excluded from Git.

Important ignored files include:

```text
.env
.env.aws
```

## Generated Files

Generated files such as:

```text
node_modules/
dist/
```

are excluded from the repository.

---

# 16. Process Documentation

The internship work was documented throughout the project lifecycle.

The documentation covers:

1. Project selection
2. Environment setup
3. Application development
4. Docker configuration
5. CI/CD implementation
6. Automated testing
7. Reliability testing
8. Troubleshooting
9. Docker image optimization
10. GitHub Actions configuration
11. Docker Hub publishing
12. Final portfolio preparation

The weekly folders provide the development history of the internship.

---

# 17. Repository Organization

The final repository is organized by internship week:

```text
ICP-F9C6947E-2026-REPO/
│
├── README.md
│
├── Week1/
│
├── Week2/
│
├── Week3/
│
├── Week4/
│
├── Week5/
│
└── Week6/
```

Week 6 contains the final portfolio and operational documentation.

---

# 18. Week 6 Documentation

The Week 6 directory contains:

```text
Week6/
├── README.md
├── portfolio.md
├── runbook-docker-3tier.md
└── runbook-cicd.md
```

### README.md

Contains the final process documentation and portfolio overview.

### portfolio.md

Contains the presentation of the completed DevOps projects.

### runbook-docker-3tier.md

Contains operational instructions for the Dockerized 3-tier application.

### runbook-cicd.md

Contains operational instructions for the CI/CD pipeline.

---

# 19. Project 1 Runbook

The Dockerized 3-tier application runbook documents:

- Prerequisites
- Repository setup
- Environment configuration
- Docker Compose commands
- Application startup
- Application verification
- Health checks
- Service restart
- Full stack restart
- Database persistence
- Troubleshooting
- Shutdown procedure

The detailed runbook is available at:

```text
Week6/runbook-docker-3tier.md
```

---

# 20. Project 2 Runbook

The CI/CD runbook documents:

- Repository structure
- GitHub Actions workflow
- Pipeline triggers
- Required GitHub Secrets
- Automated testing
- Application build
- Docker image build
- Docker Hub publishing
- Pipeline verification
- Image tagging
- Reliability checks
- Troubleshooting

The detailed runbook is available at:

```text
Week6/runbook-cicd.md
```

---

# 21. Portfolio Presentation

The completed projects demonstrate practical experience with:

```text
Linux
Git & GitHub
Docker
Docker Compose
Nginx
Python / Flask
MySQL
Shell Scripting
GitHub Actions
Node.js
npm
Vite
Docker Buildx
Docker Hub
CI/CD
Automated Testing
Health Checks
Reliability Testing
Documentation
```

The portfolio demonstrates the progression from containerized application deployment to automated CI/CD workflows.

---

# 22. GitHub Repository

## Main Internship Repository

```text
https://github.com/Shubh1220/ICP-F9C6947E-2026-REPO
```

The repository contains all internship work organized by week.

---

# 23. CI/CD Workflow Location

The GitHub Actions workflow is located at:

```text
.github/workflows/ci-cd.yml
```

The workflow contains:

```text
Test Application
       |
       v
Build Application
       |
       v
Build and Push Docker Image
```

---

# 24. Docker Hub Repository

The CI/CD pipeline publishes the application image to:

```text
opsshubh/streamflix-clone
```

The image contains:

```text
latest
```

and commit-specific tags.

---

# 25. LinkedIn Portfolio

The completed internship projects are presented through LinkedIn posts.

The LinkedIn posts document:

- Project objectives
- Architecture
- Technologies
- Implementation
- Testing
- Reliability improvements
- Project outcomes
- Learning experience

The LinkedIn project post link will be included in the final internship submission.

---

# 26. Final Repository Checklist

Before final submission, the following items should be verified:

```text
✓ Minimum 2 completed projects
✓ Week 1 documentation
✓ Week 2 documentation
✓ Week 3 documentation
✓ Week 4 CI/CD implementation
✓ Week 5 CI/CD completion
✓ Week 6 portfolio documentation
✓ Project 1 runbook
✓ Project 2 runbook
✓ GitHub repository link
✓ LinkedIn project post
✓ No secrets committed
✓ No unnecessary generated files
✓ Git working tree clean
```

---

# 27. Minimum Two Projects

The final portfolio contains the required minimum of two completed projects.

## Project 1

```text
Dockerized 3-Tier Application Deployment
```

Architecture:

```text
Frontend
   |
   v
Backend API
   |
   v
MySQL
```

## Project 2

```text
CI/CD Pipeline
```

Architecture:

```text
GitHub
   |
   v
GitHub Actions
   |
   v
Test
   |
   v
Build
   |
   v
Docker
   |
   v
Docker Hub
```

---

# 28. Final Portfolio Structure

```text
ICP-F9C6947E-2026-REPO/
│
├── README.md
│
├── Week1/
│
├── Week2/
│
├── Week3/
│
├── Week4/
│
├── Week5/
│
└── Week6/
    ├── README.md
    ├── portfolio.md
    ├── runbook-docker-3tier.md
    └── runbook-cicd.md
```

---

# 29. Final Submission Preparation

The final repository is prepared for submission after all internship tasks are completed.

The final submission information includes:

```text
Public GitHub Repository
LinkedIn Post Link
Project Title
Project Description
```

The GitHub repository contains the completed work organized into the required weekly structure.

---

# 30. Final Outcome

The internship portfolio contains:

```text
Project 1
Dockerized 3-Tier Application Deployment
        +
Project 2
CI/CD Pipeline
        +
Process Documentation
        +
Runbooks
        +
Portfolio Presentation
```

The completed projects demonstrate practical DevOps experience in containerization, CI/CD automation, testing, reliability validation, image publishing, configuration management, and technical documentation.

---

# 31. Conclusion

**Status: Week 6 consolidates the completed internship work into a structured DevOps portfolio**.

The final portfolio contains the required minimum of two completed projects, process documentation, GitHub repository references, and operational runbooks.

The repository provides a structured record of the development, testing, reliability improvements, and documentation completed throughout the internship.

The completed portfolio is prepared for the final internship submission.
