# Day 23: Practical Use Cases - Node.js + MongoDB & Spring Boot + PostgreSQL 🗄️☕ Node

---

## Use Case 1: Node.js Web App with MongoDB

This setup runs a Node.js express server connecting to a MongoDB database.

### `docker-compose.yml` for Node + MongoDB:

```yaml
version: '3.8'

services:
  # Express API server
  app:
    build: .
    ports:
      - "5000:5000"
    environment:
      - PORT=5000
      - MONGO_URI=mongodb://db:27017/my_database
    depends_on:
      - db
    networks:
      - node-mongo-net

  # MongoDB Instance
  db:
    image: mongo:6.0
    restart: always
    ports:
      - "27017:27017" # Exposed to local host for inspection (optional)
    volumes:
      - mongo_data:/data/db
    networks:
      - node-mongo-net

volumes:
  mongo_data:

networks:
  node-mongo-net:
```

### Accompanying Node.js Express `server.js` Snip:
```javascript
const express = require('express');
const mongoose = require('mongoose');

const app = express();
const MONGO_URI = process.env.MONGO_URI || 'mongodb://localhost:27017/test';

mongoose.connect(MONGO_URI)
  .then(() => console.log('Connected to MongoDB!'))
  .catch(err => console.error('Connection error:', err));

app.get('/', (req, res) => res.send('Hello from Node & MongoDB stack!'));

app.listen(process.env.PORT || 5000, () => console.log('Server is running'));
```

---

## Use Case 2: Java Spring Boot with PostgreSQL

This configures a Java Spring Boot microservice running on port 8080 and storing data in a PostgreSQL server.

### `docker-compose.yml` for Spring Boot + PostgreSQL:

```yaml
version: '3.8'

services:
  # Spring Boot Microservice
  app:
    image: spring-boot-app:latest
    ports:
      - "8080:8080"
    environment:
      - SPRING_DATASOURCE_URL=jdbc:postgresql://db:5432/appdb
      - SPRING_DATASOURCE_USERNAME=postgres
      - SPRING_DATASOURCE_PASSWORD=pgpassword
    depends_on:
      db:
        condition: service_healthy
    networks:
      - spring-pg-net

  # PostgreSQL DB Instance
  db:
    image: postgres:15-alpine
    restart: always
    environment:
      - POSTGRES_DB=appdb
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=pgpassword
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    networks:
      - spring-pg-net

volumes:
  postgres_data:

networks:
  spring-pg-net:
```

### Spring Boot configuration (`application.properties`):
```properties
spring.datasource.url=${SPRING_DATASOURCE_URL}
spring.datasource.username=${SPRING_DATASOURCE_USERNAME}
spring.datasource.password=${SPRING_DATASOURCE_PASSWORD}
spring.jpa.hibernate.ddl-auto=update
```

---

## Summary

- Use Docker Compose to inject environment variables representing backend connection strings (like `MONGO_URI` or `SPRING_DATASOURCE_URL`).
- Decoupling database configuration inside `properties` files via environment variable injection makes local development vs containerized deployment seamless.
