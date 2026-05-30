# Day 20: Build vs. Image & Service Dependency Ordering ⚙️🚦

---

## 1. `build` vs. `image` Fields in YAML

When defining services in a `docker-compose.yml` file, you must specify what image the service will run. You have two options:

### A. The `image` Field
Instructs Docker Compose to pull a pre-built image from a registry (like Docker Hub).

```yaml
services:
  web:
    image: nginx:alpine  # Pulls from Docker Hub
```

### B. The `build` Field
Instructs Docker Compose to build a new image from a local `Dockerfile` before running the container.

```yaml
services:
  api:
    build:
      context: ./api-service     # Folder containing Dockerfile
      dockerfile: Dockerfile     # Name of Dockerfile (default)
```

### C. Using Both Together
You can specify both `build` and `image`. In this case, Docker Compose builds the image from the specified `build` context and names/tags the resulting image using the `image` field.

```yaml
services:
  custom-api:
    build: ./api-service
    image: myusername/custom-api:1.0.0 # Built locally, tagged as this
```

---

## 2. Service Dependency Ordering (`depends_on`)

By default, Docker Compose starts all containers concurrently. However, applications often require database instances to start before the backend API runs.

We use the `depends_on` field to express startup and shutdown dependencies between services.

### Basic `depends_on` (Startup Order Only)
```yaml
services:
  web:
    image: my-web-app
    depends_on:
      - db

  db:
    image: postgres:alpine
```
- **Behavior:** Docker Compose will start `db` *before* starting `web`.
- **Limitation:** It only waits for the database container to *start*, not for the database software inside it to be *ready* to accept connections.

---

## 3. Advanced Dependency Ordering with Health Checks

To make sure a service only starts when its dependency is fully healthy and ready, we combine `depends_on` with `healthcheck`.

```yaml
version: '3.8'

services:
  web:
    image: my-backend-app
    depends_on:
      db:
        condition: service_healthy # Wait for db healthcheck to pass

  db:
    image: postgres:alpine
    environment:
      POSTGRES_PASSWORD: mysecretpassword
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5
```

### Healthcheck Parameters:
- `test`: Command to execute inside container to verify health.
- `interval`: How often to perform the check.
- `timeout`: Maximum time allowed for check command to run.
- `retries`: Number of consecutive failures before marking container as unhealthy.

---

## 4. Summary

- Use `image` for third-party tools (Databases, Nginx); use `build` for custom microservices you write code for.
- `depends_on` controls container startup order but does not guarantee the database engine is ready.
- Use `condition: service_healthy` alongside `healthcheck` to ensure services start only when dependencies are fully ready.
