# Runbook — StreamFlix CI/CD Pipeline

## 1. Purpose

This runbook provides operational instructions for the StreamFlix CI/CD pipeline.

The pipeline automates:

```text
Developer
    |
    v
GitHub Repository
    |
    v
GitHub Actions
    |
    +----------------+
    |                |
    v                v
  Tests           Build
    |                |
    +-------+--------+
            |
            v
       Docker Build
            |
            v
       Docker Hub
```

The pipeline is designed to automatically validate application changes, create the production build, build the Docker image, and publish the image to Docker Hub.

---

# 2. Project Overview

### Project

**StreamFlix CI/CD Pipeline**

### Main Technologies

* Git
* GitHub
* GitHub Actions
* Node.js
* npm
* Vite
* Docker
* Docker Buildx
* Docker Hub

### Main Pipeline Stages

```text
1. Test
2. Build
3. Docker Build
4. Docker Hub Push
```

The build stage depends on successful testing, and the Docker publishing stage depends on the successful build.

---

# 3. Repository Structure

The CI/CD project is maintained in the internship repository.

The application Dockerfile is located at:

```text
Week4/app/Dockerfile
```

The Docker build context is:

```text
./Week4/app
```

The GitHub Actions workflow is stored inside the repository's GitHub Actions workflow directory.

---

# 4. Pipeline Trigger

The workflow is configured to run when changes are pushed to the `main` branch.

It also runs for pull requests targeting `main`.

The general workflow is:

```text
Push / Pull Request
        |
        v
    GitHub Actions
        |
        v
      Tests
        |
   Test successful?
      /     \
    No       Yes
    |         |
   Stop       v
             Build
               |
               v
          Docker Build
               |
               v
          Docker Hub
```

---

# 5. Required GitHub Secrets

The pipeline uses GitHub Actions secrets for credentials and configuration.

The documented secrets are:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
VITE_TMDB_API_KEY
```

### DOCKERHUB_USERNAME

Contains the Docker Hub username used by the workflow.

### DOCKERHUB_TOKEN

Contains the Docker Hub access token.

### VITE_TMDB_API_KEY

Contains the TMDB API key required by the StreamFlix application.

---

# 6. Security Requirements

Secrets must be stored in GitHub repository secrets.

Do not place credentials directly inside:

* Source code
* Dockerfiles
* GitHub Actions YAML
* README files
* Public documentation
* Git commits

Do not commit `.env` files containing real credentials or API keys.

If a secret is accidentally exposed, revoke or rotate it immediately.

---

# 7. Local Prerequisites

Before testing the application locally, verify the required tools.

Check Node.js:

```bash
node --version
```

Check npm:

```bash
npm --version
```

Check Docker:

```bash
docker --version
```

Check Git:

```bash
git --version
```

---

# 8. Install Dependencies

Move into the application directory:

```bash
cd Week4/app
```

Install dependencies:

```bash
npm ci --no-audit --no-fund
```

`npm ci` installs dependencies based on the project's lock file and is suitable for reproducible CI environments.

---

# 9. Run Automated Tests

Run the application tests:

```bash
npm test
```

The documented local test result is:

```text
3 tests passed
```

A successful test run should complete without test failures.

If tests fail, do not proceed with the release/publishing process until the failure has been investigated.

---

# 10. Production Build

Create the production build:

```bash
npm run build
```

The production output is generated in:

```text
dist/
```

Verify the build output:

```bash
ls -la dist/
```

A successful build should generate the required production files inside `dist/`.

---

# 11. Docker Image Build

The application Dockerfile is located at:

```text
Week4/app/Dockerfile
```

The Docker build context is:

```text
./Week4/app
```

Build the Docker image from the appropriate project directory using the repository's Docker configuration.

Example:

```bash
docker build -t streamflix-clone .
```

Verify the image:

```bash
docker images
```

---

# 12. Docker Container Startup

Start the built application image:

```bash
docker run -d -p <HOST_PORT>:80 --name streamflix-clone <IMAGE_NAME>
```

Replace:

```text
<HOST_PORT>
<IMAGE_NAME>
```

with the configured values.

Check the container:

```bash
docker ps
```

---

# 13. Container Health Check

The application Dockerfile contains a health check based on the local HTTP endpoint:

```text
http://127.0.0.1:80/
```

The configured health check uses:

```text
interval: 15 seconds
timeout: 5 seconds
start period: 10 seconds
retries: 5
```

The health check command verifies that the application responds successfully over HTTP.

Check container health:

```bash
docker ps
```

For more detailed health information:

```bash
docker inspect <container_name>
```

---

# 14. Verify HTTP Response

After starting the container, test the application:

```bash
curl http://localhost:<HOST_PORT>/
```

A successful response indicates that the application is serving HTTP traffic.

The application can also be opened in a browser:

```text
http://localhost:<HOST_PORT>/
```

---

# 15. GitHub Actions Workflow

The GitHub Actions pipeline performs the automated CI/CD process.

The workflow follows the dependency order:

```text
Test
  |
  v
Build
  |
  v
Docker Build / Publish
```

The later jobs use job dependencies so that subsequent stages do not proceed when the required previous stage fails.

---

# 16. Test Stage

The test stage installs the application dependencies:

```bash
npm ci --no-audit --no-fund
```

Then executes:

```bash
npm test
```

The purpose of this stage is to identify application/test failures before building and publishing the Docker image.

Expected result:

```text
Tests: PASS
```

Documented project result:

```text
3 tests passed
```

---

# 17. Build Stage

After successful testing, the build stage executes:

```bash
npm run build
```

The expected output directory is:

```text
dist/
```

The build stage verifies that the application can be converted into a production-ready frontend bundle.

---

# 18. Docker Build Stage

After successful testing and build completion, the pipeline builds the Docker image using the project's Dockerfile.

The Dockerfile location is:

```text
Week4/app/Dockerfile
```

The build context is:

```text
./Week4/app
```

Docker Buildx is used as part of the container image build process.

---

# 19. Docker Hub Publishing

After a successful Docker build, the pipeline publishes the image to Docker Hub.

The documented Docker Hub repository is:

```text
dockerhubusername/streamflix-clone
```

The workflow publishes:

```text
latest
```

and a commit-specific image tag.

This allows the latest application version and a specific build version to be identified separately.

---

# 20. Image Tags

The pipeline uses two types of image tags:

### Latest Tag

```text
latest
```

This represents the latest successfully published application image.

### Commit-Specific Tag

The pipeline also creates a tag associated with the Git commit.

This allows a particular application version to be traced back to the source commit that produced it.

---

# 21. Triggering a Pipeline

To trigger the workflow through a normal development change:

### Step 1 — Check repository status

```bash
git status
```

### Step 2 — Make the required application change.

### Step 3 — Stage the changes

```bash
git add .
```

### Step 4 — Commit

```bash
git commit -m "Update StreamFlix application"
```

### Step 5 — Push to `main`

```bash
git push origin main
```

GitHub Actions should detect the push and start the configured workflow.

---

# 22. Verify GitHub Actions

Open the repository on GitHub and navigate to:

```text
Actions
```

Select the relevant workflow run.

Verify the jobs complete successfully in the configured order:

```text
Test
  ↓
Build
  ↓
Docker Build / Publish
```

A successful pipeline should show completed jobs without errors.

---

# 23. Verify Docker Hub Publishing

After a successful pipeline run, verify that the image has been published to the configured Docker Hub repository:

```text
opsshubh/streamflix-clone
```

Verify that the expected tags are present:

```text
latest
<commit-specific-tag>
```

The commit-specific tag should correspond to the Git commit used by the pipeline.

---

# 24. Pull the Published Image

To test the published image independently:

```bash
docker pull dockerhubusername/streamflix-clone:latest
```

Verify the downloaded image:

```bash
docker images
```

Run the image:

```bash
docker run -d -p <HOST_PORT>:80 --name streamflix-test dockerhubuername/streamflix-clone:latest
```

Check:

```bash
docker ps
```

---

# 25. Verify Published Container

Test the running published image:

```bash
curl http://localhost:<HOST_PORT>/
```

Check the container health:

```bash
docker ps
```

For detailed health information:

```bash
docker inspect streamflix-test
```

A healthy container should successfully respond to the configured HTTP health check.

---

# 26. Pipeline Failure Handling

If the Test stage fails:

```text
Test
 ↓
FAIL
```

Investigate the test failure before continuing.

Run locally:

```bash
npm ci --no-audit --no-fund
npm test
```

---

If the Build stage fails:

```text
Test
 ↓
PASS
 ↓
Build
 ↓
FAIL
```

Run locally:

```bash
npm run build
```

Check the build output and application dependencies.

---

If the Docker build fails:

Check the Dockerfile:

```text
Week4/app/Dockerfile
```

Then reproduce the Docker build locally.

Check:

* Dockerfile instructions
* Build context
* Application dependencies
* Required files
* Build output
* Docker configuration

---

If Docker Hub publishing fails:

Check:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Verify that:

* The secrets exist.
* The token is valid.
* The Docker Hub repository is accessible.
* The configured image name is correct.

---

# 27. GitHub Actions Troubleshooting

If the workflow does not start:

1. Verify the changes were pushed to the correct branch.
2. Verify the workflow trigger configuration.
3. Check the GitHub Actions page.
4. Check whether the workflow file is present in the repository.

If a job fails:

1. Open the failed job.
2. Read the failed step's log.
3. Reproduce the command locally when possible.
4. Fix the underlying problem.
5. Commit the fix.
6. Push the changes again.

---

# 28. Docker Health Check Troubleshooting

The Docker health check uses:

```text
127.0.0.1:80
```

If the container becomes unhealthy:

Check:

```bash
docker ps
```

Then:

```bash
docker inspect <container_name>
```

View application logs:

```bash
docker logs <container_name>
```

Test the HTTP endpoint from inside the container if required:

```bash
docker exec -it <container_name> sh
```

Then verify that the application is listening on the expected port.

---

# 29. Container Restart

Restart the application container:

```bash
docker restart <container_name>
```

Verify:

```bash
docker ps
```

Then test:

```bash
curl http://localhost:<HOST_PORT>/
```

---

# 30. Stop the Container

Stop the running container:

```bash
docker stop <container_name>
```

Verify:

```bash
docker ps
```

The stopped container will no longer appear in the default running-container list.

---

# 31. Remove the Container

After stopping the container:

```bash
docker rm <container_name>
```

Verify:

```bash
docker ps -a
```

---

# 32. Reliability Verification

The completed project was tested across the following areas:

```text
[PASS] Dependency installation
[PASS] Automated tests
[PASS] Production build
[PASS] Docker image build
[PASS] Docker container startup
[PASS] Container health
[PASS] HTTP response
[PASS] GitHub Actions pipeline
[PASS] Docker Hub publishing
```

The local automated test result was:

```text
3 tests passed
```

---

# 33. Deployment Verification Checklist

After a successful CI/CD run, verify:

```text
[ ] Code pushed to the correct branch
[ ] GitHub Actions workflow started
[ ] Test stage passed
[ ] Build stage passed
[ ] Docker image built successfully
[ ] Docker Hub authentication succeeded
[ ] Image published successfully
[ ] latest tag exists
[ ] Commit-specific tag exists
[ ] Published image can be pulled
[ ] Container starts successfully
[ ] Container health check passes
[ ] Application responds over HTTP
```

---

# 34. Security Checklist

Before finalizing a pipeline change:

```text
[ ] No API keys committed
[ ] No Docker Hub token committed
[ ] No passwords committed
[ ] GitHub secrets used for sensitive values
[ ] Public repository contains no credentials
[ ] Docker Hub credentials are not hard-coded
```

---

# 35. Standard Recovery Procedure

If a deployment fails:

### Step 1 — Check GitHub Actions

Open the failed workflow run and identify the failed job.

### Step 2 — Identify the failed stage

```text
Test
Build
Docker Build
Docker Hub Publish
```

### Step 3 — Reproduce locally

Run the corresponding command locally.

### Step 4 — Fix the issue

Correct the application, configuration, Dockerfile, workflow, or secret configuration as appropriate.

### Step 5 — Commit the fix

```bash
git add .
git commit -m "Fix CI/CD pipeline"
```

### Step 6 — Push

```bash
git push origin main
```

### Step 7 — Verify the new workflow run

Confirm that all required stages complete successfully.

---

# 36. Operational Notes

* Keep Docker Hub credentials in GitHub Secrets.
* Keep API keys out of source control.
* Do not bypass the testing stage when publishing application changes.
* Check GitHub Actions logs when a pipeline fails.
* Verify the Docker image after publishing.
* Use commit-specific tags when tracing a particular build.
* Verify the container health check after starting a published image.
* Keep the Dockerfile and workflow configuration version-controlled.

---

# 37. Related Project Documentation

Internship repository:

```text
https://github.com/Shubh1220/ICP-F9C6947E-2026-REPO
```

StreamFlix CI/CD project documentation is maintained under the Week 4 and Week 6 project documentation.

---

# 38. Runbook Completion

This runbook documents the operational process for the StreamFlix CI/CD pipeline, including:

* Repository structure
* GitHub Actions workflow
* Pipeline triggers
* Required secrets
* Automated testing
* Production build
* Docker image creation
* Docker Hub publishing
* Image tagging
* Container health checks
* Pipeline verification
* Reliability checks
* Troubleshooting
* Recovery procedures
* Security practices

**Runbook Status: Completed**
