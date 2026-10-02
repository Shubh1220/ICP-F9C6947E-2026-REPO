# Runbook — Dockerized 3-Tier Application

## 1. Purpose

This runbook provides operational instructions for running, verifying, restarting, troubleshooting, and shutting down the Dockerized 3-Tier Application.

The application consists of:

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

The project uses Docker and Docker Compose to manage the application services.

---

# 2. Project Components

The application contains the following major components:

```text
Frontend
Backend API
MySQL Database
Nginx
Docker Network
Persistent Database Volume
```

### Technologies

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

---

# 3. Prerequisites

Before running the application, make sure the following are available:

* Linux environment
* Git
* Docker
* Docker Compose
* Access to the project repository
* Required environment configuration

Verify Docker:

```bash
docker --version
```

Verify Docker Compose:

```bash
docker compose version
```

Verify Git:

```bash
git --version
```

---

# 4. Repository Setup

Clone the project repository:

```bash
git clone https://github.com/Shubh1220/ICP-F9C6947E-2026-REPO
```

Move into the project directory:

```bash
cd ICP-F9C6947E-2026-REPO
```

Verify the repository contents:

```bash
ls -la
```

The project contains the application components and deployment configuration used by Docker Compose.

---

# 5. Environment Configuration

The application uses environment variables for configuration.

The project supports environment configuration for database connectivity and application ports.

Important configuration values include:

```text
DB_HOST
DB_PORT
DB_NAME
DB_USER
PUBLIC_PORT
```

For local execution, the database host is configured for the local MySQL service.

For production/AWS deployment, the database configuration can point to the configured AWS RDS database.

Do not commit sensitive environment files containing credentials or secrets to Git.

---

# 6. Build the Application

From the project root directory, build the Docker images:

```bash
docker compose build
```

To rebuild images without using the existing Docker build cache:

```bash
docker compose build --no-cache
```

Use the no-cache option when changes to the Dockerfile or dependencies require a completely fresh image build.

---

# 7. Start the Application

Start the complete application stack:

```bash
docker compose up -d
```

The `-d` option starts the services in detached mode.

Verify the running containers:

```bash
docker compose ps
```

View all containers:

```bash
docker ps
```

---

# 8. View Application Logs

To view logs for all services:

```bash
docker compose logs
```

To follow logs continuously:

```bash
docker compose logs -f
```

To view logs for a specific service:

```bash
docker compose logs -f backend
```

Replace `backend` with the appropriate service name when checking another component.

---

# 9. Verify Container Health

Check the Compose service status:

```bash
docker compose ps
```

The configured services should reach their expected healthy/running state.

Docker health status can also be inspected with:

```bash
docker ps
```

For a specific container:

```bash
docker inspect <container_name>
```

Health information can be located in the container inspection output.

---

# 10. Verify Frontend

The frontend should be accessible through the configured HTTP port.

For local testing, use:

```bash
curl http://localhost:<PORT>/
```

Replace `<PORT>` with the configured public application port.

A successful response should return the application HTML.

The frontend can also be opened in a browser using:

```text
http://localhost:<PORT>/
```

---

# 11. Verify Backend Health

The backend provides a health endpoint used to verify application and database availability.

Test the endpoint using:

```bash
curl http://localhost:<BACKEND_PORT>/health
```

The expected response is:

```json
{
  "database": "up",
  "status": "ok"
}
```

A response showing:

```text
"status": "ok"
```

and:

```text
"database": "up"
```

indicates that the backend is running and database connectivity is available.

---

# 12. Verify Database Connectivity

Database connectivity is validated through the backend health endpoint.

Run:

```bash
curl http://localhost:<BACKEND_PORT>/health
```

Check that the response contains:

```text
"database": "up"
```

If the database is unavailable, inspect the MySQL container:

```bash
docker compose logs mysql
```

Also check the service status:

```bash
docker compose ps
```

---

# 13. Verify Service Networking

The application services communicate through the Docker network created by Docker Compose.

Inspect the networks:

```bash
docker network ls
```

Inspect the application network:

```bash
docker network inspect <network_name>
```

The expected application services should be connected to the project network.

---

# 14. Service Restart

If a specific service needs to be restarted, use:

```bash
docker compose restart <service>
```

For example:

```bash
docker compose restart backend
```

After restarting the service, check:

```bash
docker compose ps
```

Then verify the backend health endpoint again:

```bash
curl http://localhost:<BACKEND_PORT>/health
```

---

# 15. Full Application Restart

To stop the application stack:

```bash
docker compose down
```

Start it again:

```bash
docker compose up -d
```

Verify:

```bash
docker compose ps
```

Then test the frontend and backend health endpoint again.

---

# 16. Database Persistence Verification

The MySQL database uses persistent Docker storage.

The purpose of the persistent volume is to prevent database data from being lost when containers are recreated.

Check Docker volumes:

```bash
docker volume ls
```

Inspect the project volume if required:

```bash
docker volume inspect <volume_name>
```

To test persistence:

### Step 1 — Start the application

```bash
docker compose up -d
```

### Step 2 — Verify the application

```bash
docker compose ps
```

### Step 3 — Stop the application

```bash
docker compose down
```

### Step 4 — Start the application again

```bash
docker compose up -d
```

### Step 5 — Verify database connectivity

```bash
curl http://localhost:<BACKEND_PORT>/health
```

The database should become available again after the application stack starts.

---

# 17. Health Check Troubleshooting

If a service is reported as unhealthy, first check:

```bash
docker compose ps
```

Then inspect the service logs:

```bash
docker compose logs <service>
```

For more detailed container information:

```bash
docker inspect <container_name>
```

Check:

* Container status
* Health status
* Port configuration
* Service dependencies
* Environment variables
* Network connectivity

---

# 18. Common Troubleshooting

## 18.1 Containers Are Not Running

Check:

```bash
docker compose ps
```

Then view logs:

```bash
docker compose logs
```

If required, restart:

```bash
docker compose down
docker compose up -d
```

---

## 18.2 Frontend Is Not Accessible

Check the container status:

```bash
docker compose ps
```

Check frontend/Nginx logs:

```bash
docker compose logs frontend
```

Verify the configured port:

```bash
docker compose config
```

Then test:

```bash
curl http://localhost:<PORT>/
```

---

## 18.3 Backend Health Check Fails

Check backend logs:

```bash
docker compose logs backend
```

Check the backend status:

```bash
docker compose ps
```

Then test the health endpoint:

```bash
curl http://localhost:<BACKEND_PORT>/health
```

If the response does not show the database as `up`, check the MySQL service.

---

## 18.4 Database Is Unavailable

Check MySQL status:

```bash
docker compose ps
```

View MySQL logs:

```bash
docker compose logs mysql
```

Verify the database environment configuration.

Check that the backend is using the correct database host, port, database name, username, and password.

---

## 18.5 Service Cannot Communicate With Another Service

Inspect the Docker network:

```bash
docker network ls
```

Then:

```bash
docker network inspect <network_name>
```

Confirm that the required services are connected to the same Docker network.

---

# 19. Configuration Validation

Before starting the application, validate the Compose configuration:

```bash
docker compose config
```

This helps identify configuration and YAML errors before the services are started.

If the configuration is valid, proceed with:

```bash
docker compose up -d
```

---

# 20. Image Optimization

The backend Docker image was optimized using a multi-stage Docker build.

The approximate image sizes were:

```text
Original image:   396 MB
Optimized image:  223 MB
Reduction:        43.7%
```

The optimized image reduces unnecessary content in the final runtime image.

To inspect local images:

```bash
docker images
```

---

# 21. Rebuild After Application Changes

After modifying application code or Docker configuration, rebuild the required image:

```bash
docker compose build
```

Then restart the application:

```bash
docker compose up -d
```

Verify:

```bash
docker compose ps
```

Test the application again after the rebuild.

---

# 22. Safe Shutdown

To stop the application stack:

```bash
docker compose down
```

This stops and removes the containers created by the Compose project.

If the application needs to be started again:

```bash
docker compose up -d
```

---

# 23. Cleanup

To stop and remove the containers:

```bash
docker compose down
```

Do not remove persistent database volumes unless database data is intentionally being deleted.

Avoid using destructive volume-removal commands during normal troubleshooting.

---

# 24. Operational Verification Checklist

After deployment or restart, verify:

```text
[ ] Docker is running
[ ] Docker Compose configuration is valid
[ ] Required environment configuration is available
[ ] Application images are available
[ ] Containers are running
[ ] Services reach their expected health state
[ ] Frontend is accessible
[ ] Backend health endpoint responds
[ ] Database reports "up"
[ ] Docker network is available
[ ] Database persistence is maintained
```

---

# 25. Standard Recovery Procedure

If the application is not responding:

### Step 1 — Check service status

```bash
docker compose ps
```

### Step 2 — Check logs

```bash
docker compose logs
```

### Step 3 — Restart the affected service

```bash
docker compose restart <service>
```

### Step 4 — Verify health

```bash
docker compose ps
```

### Step 5 — Test the backend

```bash
curl http://localhost:<BACKEND_PORT>/health
```

### Step 6 — Test the frontend

```bash
curl http://localhost:<PORT>/
```

### Step 7 — Perform a complete restart if required

```bash
docker compose down
docker compose up -d
```

### Step 8 — Perform final verification

```bash
docker compose ps
```

Then verify the frontend, backend, and database connectivity.

---

# 26. Operational Notes

* Keep sensitive configuration outside Git.
* Do not commit `.env` or `.env.aws` files containing secrets.
* Check service health after every deployment or restart.
* Use logs when diagnosing service failures.
* Verify database connectivity through the backend health endpoint.
* Preserve the database volume during normal application restarts.
* Avoid destructive Docker volume commands unless data removal is intentional.
* Rebuild images when Dockerfile or dependency changes require it.
* Validate Docker Compose configuration before starting the stack.

---

# 27. Related Project Documentation

Additional project documentation is available in the internship repository.

Internship repository:

```text
https://github.com/Shubh1220/ICP-F9C6947E-2026-REPO
```

The internship repository contains the project development history organized by week.

---

# 28. Runbook Completion

This runbook provides the operational procedures for the Dockerized 3-Tier Application, including:

* Environment preparation
* Docker image building
* Application startup
* Service verification
* Health checks
* Database connectivity
* Service restart
* Full-stack restart
* Database persistence
* Troubleshooting
* Image optimization
* Safe shutdown
* Recovery procedures

**Runbook Status: Completed**
