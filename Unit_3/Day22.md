# Day 22: Practical Use Case - WordPress & MySQL Deployment 📝🐳

---

## 1. Objective

Deploy a complete **WordPress** website backed by a **MySQL** database. Both services run in independent containers, communicate via a secure network, and persist data using named volumes.

---

## 2. The `docker-compose.yml` File

Create a file named `docker-compose.yml` in a clean directory:

```yaml
version: '3.8'

services:
  # MySQL database container
  db:
    image: mysql:8.0
    container_name: wordpress-db
    restart: always
    environment:
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppassword
      MYSQL_ROOT_PASSWORD: rootpassword
    volumes:
      - mysql_data:/var/lib/mysql
    networks:
      - wp-net

  # WordPress web container
  wordpress:
    image: wordpress:latest
    container_name: wordpress-app
    restart: always
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: db:3306  # Uses MySQL service name as host
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppassword
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress_data:/var/www/html
    networks:
      - wp-net
    depends_on:
      - db

# Persistence Storage
volumes:
  mysql_data:
  wordpress_data:

# Internal Private Network
networks:
  wp-net:
```

---

## 3. Configuration Breakdown

### A. Environment Variables:
- `WORDPRESS_DB_HOST: db:3306` tells WordPress to look for MySQL on the hostname `db` (which is resolved automatically by Docker's internal DNS using the service name).
- Common database credentials (`MYSQL_USER`, `MYSQL_PASSWORD`, `MYSQL_DATABASE`) are shared between both containers to establish connection.

### B. Persistent Volumes:
- `mysql_data` mounts at `/var/lib/mysql` to ensure blog posts, comments, configuration, and users are saved if the container restarts.
- `wordpress_data` mounts at `/var/www/html` to store themes, plugins, and uploaded images.

---

## 4. Run & Test Instructions

### Step 1: Spin up the containers
```bash
docker compose up -d
```

### Step 2: Confirm containers are running
```bash
docker compose ps
```

### Step 3: Complete WordPress Setup
- Open your browser and navigate to `http://localhost:8080`.
- Choose language, enter site title, administrator credentials, and finish the configuration.

### Step 4: Tear down stack (preserving data)
```bash
docker compose down
```
If you start the containers again with `docker compose up -d`, your website configurations and posts will remain intact.

---

## 5. Summary

- **Restart Policy (`restart: always`):** Ensures containers restart automatically if the Docker host restarts or if a container crashes.
- **Data Persistence:** Using named volumes ensures database state and site uploads survive container updates.
- **Port Mapping:** The database is not exposed to the host machine, making it accessible only to WordPress internally.
