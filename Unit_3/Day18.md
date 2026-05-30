# Day 18: Docker Compose Volumes & Networks 🌐💾

---

## 1. Networking in Docker Compose

By default, Docker Compose sets up a single network for your application. Each service container joins this default network and is automatically discoverable by other containers on the same network using their **service name** as the hostname.

### Custom Networks in Compose
You can define custom networks to isolate service communication (e.g., frontend shouldn't talk directly to database).

```yaml
version: '3.8'

services:
  frontend:
    image: nginx:alpine
    networks:
      - web-net

  backend:
    image: node:18
    networks:
      - web-net
      - db-net

  database:
    image: postgres:alpine
    networks:
      - db-net

networks:
  web-net: # Public network
  db-net:  # Private network
```

- **Explanation:** The `frontend` can communicate with the `backend`. The `backend` can communicate with the `database`. But the `frontend` CANNOT access the `database` directly because they are on different networks.

---

## 2. Volumes in Docker Compose

Containers are ephemeral; their files are destroyed when the container is deleted. To persist data (like databases or uploads), we define volumes.

### A. Named Volumes
Managed by Docker. Declared at the top level and referenced under services.

```yaml
version: '3.8'

services:
  db:
    image: mysql:8.0
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data: # Declares a named volume managed by Docker
```

### B. Bind Mounts
Maps a specific host path to a container path. Useful for mounting local code folders during development.

```yaml
version: '3.8'

services:
  web:
    image: node:18
    volumes:
      - ./app:/usr/src/app # Maps local ./app directory to container path
```

---

## 3. Practical Example: Web Application with Volume and Custom Network

Let's look at a complete configuration that combines volumes and networks:

```yaml
version: '3.8'

services:
  web:
    image: ghost:alpine
    ports:
      - "8080:2368"
    networks:
      - ghost-net
    volumes:
      - ghost_content:/var/lib/ghost/content

  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD=ghostpwd
    networks:
      - ghost-net
    volumes:
      - db_data:/var/lib/mysql

volumes:
  ghost_content:
  db_data:

networks:
  ghost-net:
```

---

## 4. Key Docker Compose Networking/Volume Commands

| Command | Description |
| :--- | :--- |
| `docker volume ls` | Lists all volumes created on the host (including Compose volumes). |
| `docker network ls` | Lists all networks (Compose prefixes network names with project name). |
| `docker compose down -v` | Stops containers and deletes named volumes (useful for a clean reset). |

---

## 5. Summary

- **Service Names as Hostnames:** Containers on the same Compose network communicate using service names (e.g., `http://backend:8080`).
- **Network Isolation:** Custom networks prevent unauthorized containers from accessing backend database instances.
- **Data Persistence:** Named volumes store data safely on the host machine, independent of container lifecycles.
