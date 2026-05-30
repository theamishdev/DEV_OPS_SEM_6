# Day 49: Jenkins & Docker Integration 🐳🔁

---

## 1. Prerequisites

To run Docker steps in Jenkins:
1. Docker must be installed on the executor agent server.
2. The user `jenkins` must be added to the `docker` group to execute commands without sudo:
   ```bash
   sudo usermod -aG docker jenkins
   # Restart Jenkins service
   sudo systemctl restart jenkins
   ```
3. Install the **Docker Pipeline Plugin** in Jenkins.

---

## 2. Building & Pushing Docker Images in Jenkinsfile

This pipeline logins to Docker Hub, builds an image, and pushes it:

```groovy
pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'myusername'
        IMAGE_NAME      = 'my-java-app'
        IMAGE_TAG       = "${env.BUILD_NUMBER}" // Uses build run number as tag
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/myusername/my-java-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    # Compiles image locally using Docker CLI
                    appImage = docker.build("${DOCKER_HUB_USER}/${IMAGE_NAME}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push to Registry') {
            steps {
                script {
                    # Reference Jenkins credentials ID holding Docker Hub password
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials-id') {
                        appImage.push()
                        appImage.push('latest') // Also pushes tag "latest"
                    }
                }
            }
        }
    }
}
```

---

## 3. Using Docker Containers as Pipeline Agents

Instead of running builds directly on the agent's host OS, you can run steps inside isolated Docker containers. This eliminates the need to install JDK or Maven on the host system.

```groovy
pipeline {
    agent {
        docker {
            image 'maven:3.9-eclipse-temurin-17'
            args  '-v /tmp:/tmp' // Passes volume arguments
        }
    }

    stages {
        stage('Build') {
            steps {
                # Runs compile commands inside the Maven docker container automatically
                sh 'mvn clean package'
            }
        }
    }
}
```

---

## 4. Summary

- **Jenkins-Docker Integration** requires adding the `jenkins` user to the host's `docker` unix group.
- Use **`docker.build`** and **`docker.withRegistry`** wrapper scripts to easily package and push images.
- Use **`agent { docker { image '...' } }`** to run compile cycles inside isolated, temporary containers, keeping build hosts clean.
