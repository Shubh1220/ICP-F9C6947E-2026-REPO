## Week 2 — Dockerization and Image Optimization

### Objective

The objective of Week 2 was to develop and optimize the Dockerized 3-tier application using Dockerfiles, Docker Compose, health checks, persistent storage, and multi-stage builds.

### Dockerized Architecture

The application consists of four Docker services:

* **Frontend** — Static HTML, CSS, and JavaScript application served by Nginx.
* **Backend** — Flask REST API running with Gunicorn.
* **MySQL** — Database service with persistent Docker volume storage.
* **Nginx** — Reverse proxy that routes frontend and API traffic.

The services communicate through a Docker Compose network.

### Multi-Stage Docker Build

The backend Dockerfile was optimized using a two-stage Docker build:

1. **Builder stage**

   * Uses Python 3.11 Slim.
   * Installs required build dependencies.
   * Installs Python dependencies into a separate directory.

2. **Runtime stage**

   * Uses a clean Python 3.11 Slim image.
   * Copies only the installed dependencies from the builder stage.
   * Copies the application source code.
   * Runs the application as a non-root `appuser`.

This reduces unnecessary build dependencies from the final runtime image.

### Image Optimization Result

The backend image size was measured before and after applying the multi-stage Docker build.

| Version              | Image Size |
| -------------------- | ---------: |
| Before optimization  |     396 MB |
| After optimization   |     223 MB |
| Reduction            |     173 MB |
| Reduction percentage |     ~43.7% |

The optimized backend image is approximately **43.7% smaller** than the original image.

### Health Checks

Health checks were configured for the Docker services to verify that the containers are functioning correctly.

Final local verification:

```text
week2_backend_1    Up (healthy)
week2_frontend_1   Up (healthy)
week2_mysql_1      Up (healthy)
week2_nginx_1      Up (healthy)
```

### Application Verification

The application was tested through the Nginx reverse proxy using:

```bash
curl http://localhost:90/api/health
```

Result:

```json
{"database":"up","status":"ok"}
```

This confirms that:

* Nginx is reachable.
* The request reaches the backend API.
* The backend can successfully connect to MySQL.
* The database is operational.

### Persistent Database Storage

MySQL uses the named Docker volume:

```text
week2_db_data
```

The volume was preserved while recreating the MySQL container, ensuring that the database storage was not removed during the Docker optimization work.

### Issues Encountered and Resolved

During the Docker optimization process, the legacy Docker Compose version `1.29.2` produced a `ContainerConfig` error while recreating the MySQL container.

The issue was resolved by recreating only the MySQL container while preserving the existing `week2_db_data` volume.

After recreation, all services returned to a healthy state.

### Week 2 Completion Status

**Status: Completed ✅**

The Week 2 Dockerization and optimization work successfully produced a working multi-container application with:

* Dockerized frontend, backend, MySQL, and Nginx services.
* Multi-stage backend Docker build.
* Optimized backend image.
* Docker health checks.
* Persistent database storage.
* Working service-to-service communication.
* Successful API and database health verification.

