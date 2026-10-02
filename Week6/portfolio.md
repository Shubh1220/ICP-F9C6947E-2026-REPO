# DevOps Internship Portfolio

## 1. Overview

This portfolio summarizes the practical DevOps work completed during the six-week **DevOps Engineering Self-Learning Internship**.

The internship focused on practical project development, automation, containerization, CI/CD, testing, reliability validation, troubleshooting, documentation, and portfolio preparation.

The completed portfolio contains two major DevOps projects:

1. **Dockerized 3-Tier Application Deployment**
2. **CI/CD Pipeline**

Both projects were developed, tested, documented, and validated as part of the internship project-based program.

---

# 2. Internship Information

| Field             | Details                                             |
| ----------------- | --------------------------------------------------- |
| Program           | DevOps Engineering Self-Learning Internship         |
| Internship Type   | Self-Learning Internship                            |
| Duration          | 6 Weeks                                             |
| Mode              | Project-Based Program                               |
| Portal ID         | ICP-F9C6947E-2026                                   |
| Repository ID     | REPO-E85A4BD3                                       |
| GitHub Repository | https://github.com/Shubh1220/ICP-F9C6947E-2026-REPO |

---

# 3. Project Portfolio

## Project 1 — Dockerized 3-Tier Application Deployment

### 3.1 Objective

The objective of this project was to containerize and deploy a three-tier application using Docker and supporting DevOps technologies.

The application separates the system into frontend, backend API, and database layers while using Nginx for HTTP handling and reverse proxy functionality.

### 3.2 Architecture

```text
                         User
                           |
                           v
                      Nginx Proxy
                           |
                           v
                       Frontend
                           |
                           v
                      Backend API
                           |
                           v
                       MySQL DB
```

The application uses Docker containers for the application components and Docker networking for communication between services.

### 3.3 Technologies Used

* Linux
* Git
* GitHub
* Docker
* Docker Compose
* Nginx
* Python
* Flask
* MySQL
* Shell Scripting
* AWS

### 3.4 Project Components

The project contains the following major components:

```text
Frontend
Backend API
MySQL Database
Nginx
Docker Network
Persistent Database Volume
```

Docker Compose is used to define and manage the multi-container application.

The services communicate through the Docker network, while environment variables are used for configuration.

### 3.5 Containerization

The frontend and backend components were containerized using Dockerfiles.

Docker Compose was used to manage:

* Application services
* Service networking
* Environment variables
* Health checks
* Persistent storage
* Port configuration
* Service dependencies

This provides a consistent environment for running the application.

### 3.6 Nginx Reverse Proxy

Nginx was configured as part of the application architecture.

The reverse proxy provides an HTTP entry point and forwards requests to the appropriate application service.

```text
Client
   |
   v
Nginx
   |
   +-----------------> Frontend
   |
   +-----------------> Backend API
```

### 3.7 Database Persistence

MySQL uses persistent Docker storage.

The persistent volume ensures that database data is retained when application containers are stopped or recreated.

The persistence configuration was validated by stopping and restarting the application stack and checking that the database data remained available.

### 3.8 Health Checks

Health checks were configured for the application services.

The backend health endpoint was tested successfully.

Example response:

```json
{
  "database": "up",
  "status": "ok"
}
```

Health checks provide an additional mechanism for verifying service availability.

### 3.9 Docker Image Optimization

The backend Docker image was optimized using a multi-stage Docker build.

The approximate image sizes were:

```text
Original image:   396 MB
Optimized image:  223 MB
Reduction:        43.7%
```

The optimization reduced unnecessary content in the final runtime image.

### 3.10 Testing and Validation

The application was validated through multiple testing stages:

* Docker Compose configuration validation
* Application availability testing
* Backend health testing
* Database connectivity testing
* Service restart testing
* Full-stack restart testing
* Persistent storage validation

The backend was also tested for database connectivity through the application health endpoint.

### 3.11 Project Outcome

The project demonstrated practical experience with:

* Containerization
* Multi-container application management
* Docker Compose
* Service networking
* Nginx reverse proxy
* Database persistence
* Health checks
* Docker image optimization
* Application troubleshooting
* Reliability validation

---

# 4. Project 2 — CI/CD Pipeline

## 4.1 Objective

The objective of the second project was to implement an automated CI/CD pipeline for a web application.

The pipeline automates application testing, production building, Docker image creation, and Docker Hub publishing using GitHub Actions.

### 4.2 Architecture

```text
                    Developer
                        |
                        v
                GitHub Repository
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
                 Docker Hub Push
                        |
                        v
                   Docker Hub
```

### 4.3 Technologies Used

* Git
* GitHub
* GitHub Actions
* Node.js
* npm
* Vite
* Docker
* Docker Buildx
* Docker Hub

### 4.4 CI/CD Pipeline Stages

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

The jobs use dependencies so that each stage runs only after the required previous stage succeeds.

### 4.5 Automated Testing

The first CI/CD stage installs the application dependencies and runs the automated test suite.

Dependencies are installed using:

```bash
npm ci --no-audit --no-fund
```

The test suite is executed using:

```bash
npm test
```

The validated local test result was:

```text
3 tests passed
```

The tests cover application behavior including:

* API key notice handling
* Movie data rendering
* API request failure handling

### 4.6 Application Build

The second stage creates the production application build.

The production build command is:

```bash
npm run build
```

The application uses Vite for the production build.

The generated production output is:

```text
dist/
```

The build was validated locally and through the CI/CD workflow.

### 4.7 Docker Image Build

The third stage creates the production Docker image.

The application Dockerfile is located at:

```text
Week4/app/Dockerfile
```

Docker Buildx is used in GitHub Actions.

The Docker build context is:

```text
./Week4/app
```

### 4.8 Docker Hub Publishing

After the Docker image is successfully built, it is published to Docker Hub.

Docker Hub repository:

```text
opsshbh/streamflix-clone
```

The pipeline publishes:

```text
latest
```

as well as a commit-specific image tag.

The commit-specific tag provides traceability between the source-code commit and the resulting Docker image.

### 4.9 Pipeline Triggers

The workflow supports push and pull-request triggers.

#### Push Trigger

```yaml
on:
  push:
    branches:
      - main
```

The pipeline runs when changes are pushed to the `main` branch.

#### Pull Request Trigger

```yaml
on:
  pull_request:
    branches:
      - main
```

The workflow also runs when a pull request targets the `main` branch.

### 4.10 CI/CD Job Dependencies

The workflow uses job dependencies.

The build job depends on the test job:

```yaml
needs: test
```

The Docker job depends on the build job:

```yaml
needs: build
```

Therefore, the workflow executes as:

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

If an earlier required job fails, the dependent job does not continue.

### 4.11 GitHub Actions Secrets

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

The application API key is supplied through the GitHub Actions secret during the Docker build.

### 4.12 Docker Health Check

A Docker health check was configured for the application.

The final health check is:

```dockerfile
HEALTHCHECK --interval=15s --timeout=5s --start-period=10s --retries=5 \
    CMD wget -q -O- http://127.0.0.1:80/ || exit 1
```

During testing, the initial health check using:

```text
http://localhost:80/
```

did not pass as expected.

The health check was changed to:

```text
http://127.0.0.1:80/
```

After rebuilding and restarting the container, the container reached:

```text
healthy
```

This issue and its resolution were documented as part of reliability testing.

### 4.13 Reliability Testing

The following areas were validated:

| Test                      | Result |
| ------------------------- | ------ |
| Dependency installation   | PASS   |
| Automated tests           | PASS   |
| Production build          | PASS   |
| Docker image build        | PASS   |
| Docker container startup  | PASS   |
| Container health check    | PASS   |
| Application HTTP response | PASS   |
| GitHub Actions test job   | PASS   |
| GitHub Actions build job  | PASS   |
| GitHub Actions Docker job | PASS   |
| Docker Hub publishing     | PASS   |

### 4.14 Project Outcome

The CI/CD project demonstrated practical experience with:

* GitHub Actions
* CI/CD workflow design
* Automated testing
* Production application builds
* Docker image creation
* Docker Buildx
* Docker Hub publishing
* GitHub Actions secrets
* Pipeline job dependencies
* Health checks
* Reliability testing
* Image traceability

---

# 5. Security Practices

Security considerations were included throughout the projects.

## 5.1 Secrets Management

Sensitive values are stored outside the source code.

GitHub Actions repository secrets are used for values required by the CI/CD workflow.

Examples include:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
VITE_TMDB_API_KEY
```

## 5.2 Docker Hub Authentication

The Docker Hub token is stored as a GitHub Actions secret rather than being written directly into the workflow.

```yaml
username: ${{ secrets.DOCKERHUB_USERNAME }}
password: ${{ secrets.DOCKERHUB_TOKEN }}
```

## 5.3 Environment Files

Local environment files are excluded from Git.

Important ignored files include:

```text
.env
.env.aws
```

## 5.4 Generated Files

Generated or dependency-related files are excluded from the repository where appropriate.

Examples include:

```text
node_modules/
dist/
```

---

# 6. Reliability and Troubleshooting

Reliability testing was performed on both projects.

## Project 1

The Dockerized 3-tier application was tested for:

* Service availability
* Backend health
* Database connectivity
* Service restart
* Full-stack restart
* Database persistence

## Project 2

The CI/CD pipeline was tested for:

* Dependency installation
* Automated tests
* Production build
* Docker image build
* Container startup
* Health checks
* HTTP response
* GitHub Actions jobs
* Docker Hub publishing

Troubleshooting activities included identifying and resolving the Docker container health-check issue.

The final health check successfully reported the container as:

```text
healthy
```

---

# 7. Skills Demonstrated

The internship projects provided practical experience with:

```text
Linux
Git
GitHub
Docker
Docker Compose
Nginx
Python
Flask
MySQL
Shell Scripting
Node.js
npm
Vite
GitHub Actions
Docker Buildx
Docker Hub
CI/CD
Automated Testing
Health Checks
Containerization
Service Networking
Database Persistence
Reliability Testing
Troubleshooting
Technical Documentation
```

---

# 8. DevOps Workflow Demonstrated

The projects demonstrate a progression from application containerization to automated CI/CD.

```text
                 DevOps Project Progression

        Application Development
                  |
                  v
             Containerization
                  |
                  v
          Docker Compose
                  |
                  v
        Service Configuration
                  |
                  v
           Health Checks
                  |
                  v
           Reliability Testing
                  |
                  v
            CI/CD Automation
                  |
                  v
          Automated Testing
                  |
                  v
           Application Build
                  |
                  v
             Docker Build
                  |
                  v
          Docker Hub Publishing
                  |
                  v
             Documentation
```

---

# 9. Repository Organization

The final internship repository follows the required weekly organization:

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

The weekly folders preserve the development history of the internship.

Week 6 consolidates the completed work into the final portfolio and operational documentation.

---

# 10. Project Documentation

Detailed project documentation is available throughout the weekly folders.

### Project 1

The Dockerized 3-tier application documentation covers:

* Containerization
* Docker Compose
* Service networking
* Nginx
* Database persistence
* Health checks
* Testing
* Restart and recovery
* Docker image optimization
* Troubleshooting

### Project 2

The CI/CD documentation covers:

* GitHub Actions
* Pipeline stages
* Automated testing
* Application build
* Docker build
* Docker Hub publishing
* GitHub Actions secrets
* Job dependencies
* Health checks
* Reliability testing
* Troubleshooting

---

# 11. Operational Runbooks

The Week 6 portfolio includes operational runbooks for the two completed projects.

### Dockerized 3-Tier Application

```text
Week6/runbook-docker-3tier.md
```

The runbook covers:

* Prerequisites
* Repository setup
* Environment configuration
* Docker Compose commands
* Application startup
* Application verification
* Health checks
* Service restart
* Full-stack restart
* Database persistence
* Troubleshooting
* Shutdown procedure

### CI/CD Pipeline

```text
Week6/runbook-cicd.md
```

The runbook covers:

* Repository structure
* GitHub Actions workflow
* Pipeline triggers
* Required GitHub Secrets
* Automated testing
* Application build
* Docker image build
* Docker Hub publishing
* Pipeline verification
* Image tagging
* Reliability checks
* Troubleshooting

---

# 12. GitHub Repository

## Main Internship Repository

```text
https://github.com/Shubh1220/ICP-F9C6947E-2026-REPO
```

The repository contains the internship work organized by week.

---

# 13. CI/CD Workflow

The GitHub Actions workflow is located at:

```text
.github/workflows/ci-cd.yml
```

The workflow contains the following stages:

```text
Test Application
       |
       v
Build Application
       |
       v
Build and Push Docker Image
```

The workflow automates the validation and packaging process for the application.

---

# 14. Docker Hub Repository

The CI/CD pipeline publishes the Docker image to:

```text
opsshubh/streamflix-clone
```

The published image includes:

```text
latest
```

and commit-specific image tags.

Commit-specific tags provide a relationship between source-code commits and Docker images.

---

# 15. LinkedIn Portfolio

The completed internship projects are intended to be presented through LinkedIn.

The project presentation covers:

Project objectives
Architecture
Technologies used
Implementation
Testing
Reliability improvements
Project outcomes
Learning experience

The LinkedIn post link can be added to the final internship submission after publishing the project post.

---

# 16. Internship Learning Outcomes

The internship provided practical experience in applying DevOps concepts to project-based development.

Key learning outcomes include:

### Containerization

Learned how to package applications and their dependencies using Docker.

### Multi-Container Application Management

Used Docker Compose to manage multiple services and their dependencies.

### Reverse Proxy

Configured Nginx as part of the application architecture.

### Database Management

Worked with MySQL and persistent container storage.

### Health and Reliability

Implemented health checks and performed restart and recovery testing.

### CI/CD Automation

Implemented an automated GitHub Actions workflow for testing, building, and Docker image publishing.

### Secrets Management

Used GitHub Actions secrets for sensitive values.

### Docker Image Management

Built and published Docker images to Docker Hub.

### Testing

Performed automated and reliability testing across the projects.

### Troubleshooting

Identified configuration and health-check issues and applied corrective changes.

### Documentation

Documented implementation details, testing, troubleshooting, and operational procedures.

---

# 17. Portfolio Summary

The completed internship portfolio contains two major DevOps projects.

## Project 1

```text
Dockerized 3-Tier Application Deployment

Frontend
    |
    v
Backend API
    |
    v
MySQL
```

The project demonstrates containerization, Docker Compose, Nginx, service networking, database persistence, health checks, image optimization, testing, and reliability validation.

## Project 2

```text
CI/CD Pipeline

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

The project demonstrates CI/CD automation, automated testing, production builds, Docker image creation, Docker Hub publishing, secrets management, health checks, and reliability testing.

---

# 18. Final Portfolio Checklist

Before final internship submission, verify:

```text
[✓] Minimum 2 completed projects
[✓] Week 1 documentation
[✓] Week 2 Dockerized 3-Tier Application implementation
[✓] Week 3 Dockerized 3-Tier Application completion
[✓] Week 4 CI/CD Pipeline implementation
[✓] Week 5 CI/CD Pipeline completion
[✓] Week 6 portfolio documentation
[✓] Project 1 runbook
[✓] Project 2 runbook
[✓] Public GitHub repository
[✓] LinkedIn project post
```

---

# 19. Final Outcome

The six-week internship resulted in a structured DevOps portfolio containing:

```text
                    DEVOPS PORTFOLIO
                           |
          +----------------+----------------+
          |                                 |
          v                                 v
 Dockerized 3-Tier                  CI/CD Pipeline
 Application                         |
          |                          |
          v                          v
 Containerization               GitHub Actions
 Docker Compose                 Automated Testing
 Nginx                          Application Build
 MySQL                          Docker Build
 Health Checks                  Docker Hub
 Persistence                    Health Checks
          |                          |
          +------------+-------------+
                       |
                       v
              Reliability Testing
                       |
                       v
               Documentation
                       |
                       v
              Operational Runbooks
```

The portfolio demonstrates practical experience in containerization, CI/CD automation, testing, reliability validation, Docker image publishing, configuration management, troubleshooting, and technical documentation.

---

# 20. Conclusion

The **DevOps Engineering Self-Learning Internship** provided a practical environment for developing and documenting DevOps projects over six weeks.

The completed portfolio contains the required minimum of two projects:

1. **Dockerized 3-Tier Application Deployment**
2. **CI/CD Pipeline**

The projects demonstrate the progression from containerizing and managing a multi-service application to implementing an automated CI/CD workflow.

The final Week 6 portfolio consolidates the project work, process documentation, testing results, reliability improvements, GitHub repository information, and operational runbooks into a structured submission.

**Portfolio Status: Completed**


