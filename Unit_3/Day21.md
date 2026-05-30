# Day 21: Multi-Container App Deployment (3-Tier Architecture) 🏗️

---

## 1. Concept: Three-Tier Web Application

A standard production application is split into three layers:
1. **Frontend (Presentation Tier):** The user interface (HTML/JS/React/Vue). Usually hosted in a web server like Nginx.
2. **Backend (Application Tier):** The business logic (Node.js, Spring Boot, Python API). Connects to database.
3. **Database (Data Tier):** Holds persistent storage (PostgreSQL, MongoDB, MySQL).

### Network Architecture:
- Frontend should be accessible to the public (host port mapping).
- Backend should be accessible by the frontend but not directly exposed to the public.
- Database must be isolated inside a private network, accessible only by the Backend.

---

## 2. Docker Compose File for 3-Tier App

Here is the complete `docker-compose.yml` for this deployment structure:

```yaml
version: '3.8'

services:
  # 1. Frontend Web Service
  frontend:
    image: nginx:alpine
    ports:
      - "80:80"
    volumes:
      - ./frontend-dist:/usr/share/nginx/html
    networks:
      - frontend-net
    depends_on:
      - backend

  # 2. Backend API Service
  backend:
    build:
      context: ./backend-app
      dockerfile: Dockerfile
    environment:
      - DATABASE_URL=mongodb://database:27017/appdb
      - PORT=5000
    networks:
      - frontend-net
      - backend-net
    depends_on:
      database:
        condition: service_healthy

  # 3. Database Service
  database:
    image: mongo:6.0
    volumes:
      - mongo-data:/data/db
    networks:
      - backend-net
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 5s
      timeout: 5s
      retries: 3

# Volumes mapping for data persistence
volumes:
  mongo-data:

# Network Isolation
networks:
  frontend-net:
  backend-net:
```

---

## 3. Explaining Network Flows

- **`frontend-net`:** Includes `frontend` and `backend`. Nginx can forward requests to the Node.js backend using `http://backend:5000`.
- **`backend-net`:** Includes `backend` and `database`. The Node.js app connects to the database using `mongodb://database:27017`.
- **Isolation:** The `database` is safe because it is not on `frontend-net` and does not expose its ports to the host machine.

---

## 4. Execution Workflow

1. Prepare directories and configuration files:
   ```text
   project-root/
   ├── docker-compose.yml
   ├── frontend-dist/
   │   └── index.html
   └── backend-app/
       ├── Dockerfile
       └── server.js
   ```
2. Start the stack:
   ```bash
   docker compose up -d --build
   ```
3. Verify running containers:
   ```bash
   docker compose ps
   ```
4. View real-time logs for the backend:
   ```bash
   docker compose logs -f backend
   ```

---

## 5. Summary

- Decoupling application tiers onto different networks is a standard security practice.
- The **Frontend** maps host ports to external clients, while the **Database** exposes no ports to the host, protecting it from remote exploits.
