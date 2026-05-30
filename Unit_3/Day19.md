# Day 19: Docker Compose Environment Variables, Secrets, & Configs 🔑🛡️

---

## 1. Environment Variables in Docker Compose

Environment variables let you configure services dynamically without hardcoding configurations inside images.

### Methods to define Environment Variables:

#### Method A: Direct Definition in `docker-compose.yml`
```yaml
services:
  db:
    image: postgres:alpine
    environment:
      - POSTGRES_DB=mydb
      - POSTGRES_PASSWORD=securepassword
```

#### Method B: Using an External `.env` File
Docker Compose automatically looks for a file named `.env` in the same directory and loads variables from it.

**`.env` File:**
```properties
DB_NAME=production_db
DB_PASS=SuperSecurePass123
```

**`docker-compose.yml` File:**
```yaml
services:
  db:
    image: postgres:alpine
    environment:
      - POSTGRES_DB=${DB_NAME}
      - POSTGRES_PASSWORD=${DB_PASS}
```

---

## 2. Secrets in Docker Compose

Passing sensitive data (like API keys, SSL certificates, passwords) via environment variables can expose them (e.g., through `docker inspect` or process logs). **Docker Secrets** provide a secure alternative.

### Using Local File-based Secrets:

```yaml
version: '3.8'

services:
  db:
    image: mysql:8.0
    secrets:
      - db_password
    environment:
      MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_password

secrets:
  db_password:
    file: ./db_password.txt  # File containing the secret content
```

- **How it works:** Docker mounts the secret file at `/run/secrets/db_password` inside the container in read-only memory. The database reads the password from that file path.

---

## 3. Configs in Docker Compose

Similar to secrets, **Docker Configs** allow you to inject non-sensitive configuration files (like Nginx `nginx.conf` or Prometheus `prometheus.yml`) into containers without rebuilding the image.

```yaml
version: '3.8'

services:
  web:
    image: nginx:alpine
    ports:
      - "80:80"
    configs:
      - source: nginx_config
        target: /etc/nginx/nginx.conf

configs:
  nginx_config:
    file: ./custom_nginx.conf
```

---

## 4. Environment Variables vs. Secrets vs. Configs

| Feature | Environment Variables | Secrets | Configs |
| :--- | :--- | :--- | :--- |
| **Data Type** | Short text settings | Sensitive data (passwords, certificates) | Plain-text application configurations |
| **Visibility** | Publicly visible in `docker inspect` | Decrypted inside container filesystem | Decrypted inside container filesystem |
| **Access Path**| Environment variables | Mounted at `/run/secrets/<name>` | Mounted at target location |
| **Security** | Low | High (safe for production credentials) | Medium |

---

## 5. Summary

- Use **`.env` files** to parameterize Compose files and keep them dry.
- Avoid passing passwords in plain-text environment variables in production.
- Use **Secrets** for passwords/keys and **Configs** for config files to decouple code from configuration.
