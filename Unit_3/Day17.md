# Day 17: Docker Compose Basics & YAML Structure 🐳

---

## 1. What is Docker Compose?

Docker Compose is a tool for defining and running multi-container Docker applications. 
- Instead of executing multiple long `docker run` commands manually, you define your entire application stack in a single configuration file named `docker-compose.yml`.
- You can start, stop, and rebuild all services with a single command: `docker-compose up` or `docker compose up`.

---

## 2. The YAML (YAML Ain't Markup Language) Structure

Docker Compose files use YAML syntax. Key rules of YAML:
1. **Case Sensitivity:** YAML is case-sensitive.
2. **Indentation:** Uses spaces for indentation (tabs are NOT allowed). Consistent spacing (usually 2 spaces) defines the nested parent-child relationships.
3. **Key-Value Pairs:** Represented as `key: value`.
4. **Lists/Arrays:** Denoted by a leading hyphen `-`.

---

## 3. Basic Anatomy of a `docker-compose.yml` File

A standard Compose file is divided into four top-level configuration blocks:

```yaml
version: '3.8'      # 1. Schema version (Deprecated in newer specs, but widely used)

services:           # 2. Contains the container configurations
  web:
    image: nginx:alpine
    ports:
      - "8080:80"

volumes:            # 3. Named volumes for persistent storage
  db_data:

networks:           # 4. Custom networks for container communication
  app_net:
```

---

## 4. Explaining `version` and `services`

### A. The `version` Field
- Specifies the compatibility of the Compose file format (e.g., `'3'`, `'3.8'`).
- Newer versions of Docker Compose (Compose V2) do not strictly require this field and default to the latest specification, but it is standard practice to include it for backward compatibility.

### B. The `services` Block
- Defines the containers that make up your application.
- Each child of `services` represents a container instance.
- Common parameters under a service:
  - `image`: Specifies the Docker image to pull and run.
  - `ports`: Maps host ports to container ports (`"host_port:container_port"`).
  - `container_name`: Assigns a custom name (optional).

---

## 5. First Hands-On: A Simple Web Server Compose File

Let's create a file named `docker-compose.yml` with the following contents:

```yaml
version: '3.8'

services:
  my-web-server:
    image: nginx:latest
    container_name: compose-web
    ports:
      - "80:80"
```

### How to Run it:
1. Navigate to the directory containing the file.
2. Start the container in detached mode:
   ```bash
   docker compose up -d
   ```
3. Check running services:
   ```bash
   docker compose ps
   ```
4. Stop and remove containers:
   ```bash
   docker compose down
   ```

---

## 6. Summary

- **Docker Compose** simplifies managing multi-container stacks.
- **YAML** relies strictly on spaces for indentation.
- The `services` block is the core part where container configurations are declared.
