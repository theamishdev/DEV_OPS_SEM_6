# Day 32: Maven & Docker Integration 🐳📦

---

## 1. Dockerizing a Maven-based Application

To containerize a Java Maven application, we write a `Dockerfile`. The most efficient practice is using a **Multi-Stage Build** to keep the production image size small.

### Multi-Stage `Dockerfile` Example:

```dockerfile
# --- Stage 1: Build Phase ---
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY pom.xml .
# Pre-download dependencies to cache Docker layer
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

# --- Stage 2: Runtime Phase ---
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
# Copy only the compiled JAR file from the builder stage
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

- **Why Multi-Stage?** The build stage contains Maven, JDK compilers, and local source files (total size ~500MB+). The runtime stage contains only the slim JRE and the output JAR (total size ~150MB).

---

## 2. Automating Builds with Maven Plugins

Instead of running separate `docker build` commands, you can configure Maven plugins to automatically build and push Docker images during the Maven build lifecycle.

### A. The Spotify `dockerfile-maven-plugin`
This plugin binds the Docker build process directly to the `package` phase, and the Docker push process to the `deploy` phase.

```xml
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>dockerfile-maven-plugin</artifactId>
    <version>1.4.13</version>
    <executions>
        <execution>
            <id>default</id>
            <phase>package</phase> <!-- Binds to package phase -->
            <goals>
                <goal>build</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <repository>myusername/${project.artifactId}</repository>
        <tag>${project.version}</tag>
        <buildArgs>
            <JAR_FILE>target/${project.build.finalName}.jar</JAR_FILE>
        </buildArgs>
    </configuration>
</plugin>
```

---

## 3. Pushing Artifacts to Registries

A Docker Registry (like Docker Hub, GitHub Container Registry, or AWS ECR) hosts and shares container images.

### Workflow:
1. **Build the image:**
   ```bash
   mvn package  # If using dockerfile-maven-plugin, this automatically builds the image
   ```
2. **Log into registry:**
   ```bash
   docker login --username myusername
   ```
3. **Push to registry:**
   ```bash
   mvn deploy  # Pushes the built image to Docker Hub
   ```

---

## 4. Summary

- **Multi-stage builds** isolate compile dependencies from the final lightweight production runtime image.
- **`dockerfile-maven-plugin`** couples Maven packaging and Docker compilation steps.
- Commit clean, cached Docker layers to speed up CI/CD pipeline operations.
