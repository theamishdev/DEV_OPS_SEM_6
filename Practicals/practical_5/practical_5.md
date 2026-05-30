# Practical 5: Docker Image Build & Push Automation with GitHub Actions 🐳🔁

---

## 1. Problem Statement

Set up a Continuous Integration (CI) pipeline using GitHub Actions that automatically builds and pushes a Docker image to Docker Hub whenever code is pushed to the repository. 
Create a workflow file (`push-docker-hub.yml`) that:
1. Checks out the repository.
2. Logs in to Docker Hub using secure GitHub secrets.
3. Builds the Docker image using the project `Dockerfile`.
4. Tags the image as `username/app-ci:latest`.
5. Pushes the image to the Docker Hub registry.
6. Explains the roles of **environment variables** and **secrets** in this automation workflow.

---

## 2. Implementation Code

### A. The Application Dockerfile (`Dockerfile`)
The application uses the Flask web server configuration created in Practical 1:
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

### B. The GitHub Actions Workflow (`.github/workflows/push-docker-hub.yml`)
```yaml
name: Build and Push to Docker Hub

on:
  push:
    branches:
      - main

jobs:
  build-and-push:
    runs-on: ubuntu-latest

    steps:
      # Step 1: Checkout the repository
      - name: Checkout Code
        uses: actions/checkout@v4

      # Step 2: Set up Docker Buildx (extended builder engine)
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      # Step 3: Log in to Docker Hub using repository secrets
      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      # Step 4: Build and Push Docker image tagged as username/app-ci:latest
      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ secrets.DOCKER_USERNAME }}/app-ci:latest
```

---

## 3. The Role of Secrets & Environment Variables

### A. The Role of GitHub Secrets
**GitHub Secrets** are encrypted variables created at the repository level. 
* **Security:** They are stored in an encrypted database managed by GitHub and are never displayed in plain text in the workflow file, terminal outputs, or execution logs.
* **Accessing Secrets:** Referenced in YAML files using the `${{ secrets.NAME }}` syntax.
* **In this workflow:** 
  * `${{ secrets.DOCKER_USERNAME }}`: Holds the Docker Hub account username.
  * `${{ secrets.DOCKER_PASSWORD }}`: Holds the Docker Hub Personal Access Token (PAT).
  * If these values were hardcoded in the codebase, any public viewer could hijack your Docker Hub account and inject malicious images.

### B. The Role of Environment Variables
**Environment Variables** (`env`) are dynamic, plain-text key-value pairs used to pass non-sensitive configuration data to jobs or steps.
* **Scope:** Can be defined globally (top of workflow), at the job level, or at the step level.
* **Usage:** Referenced using standard environmental calls depending on the context (e.g., `${{ env.VAR_NAME }}` in YAML, or `$VAR_NAME` inside run shell commands).
* **In this workflow:** They can be used to set build configurations (like `IMAGE_NAME`, compiler versions, debug flags, or target paths) to keep the pipeline code modular and reusable.

---

## 4. GitHub Secrets Setup Guide

To configure the necessary credentials for this workflow:
1. Navigate to your repository page on GitHub.
2. Select **Settings** ➔ **Secrets and variables** ➔ **Actions**.
3. Click the **New repository secret** button.
4. Add the following credentials:
   * **Name:** `DOCKER_USERNAME` | **Value:** *Your Docker Hub Username*
   * **Name:** `DOCKER_PASSWORD` | **Value:** *Your Docker Hub Personal Access Token (PAT)*
5. Push changes to the `main` branch to trigger the pipeline automatically.

---

## 5. Key Viva / Interview Questions

### Q1: Why should we use a Personal Access Token (PAT) instead of our main password for `DOCKER_PASSWORD`?
Using your primary account password exposes your entire account. A Personal Access Token (PAT) can be scoped to specific permissions (e.g., read/write images, but no administrative access) and can be easily revoked or rotated individually without changing your main account password.

### Q2: What happens if a secret value is printed inside a `run: echo` command in GitHub Actions?
GitHub Actions logs automatically scan output streams for all declared secrets. If it detects a secret value being printed to the console, it intercepts the output and masks it, displaying `***` instead of the raw secret text.

### Q3: What does the `docker/setup-buildx-action` step do?
It installs and configures Docker Buildx, which leverages the Moby BuildKit engine. This enables advanced features like multi-platform builds (e.g., building ARM and x86 images simultaneously) and faster caching optimizations compared to standard `docker build` engines.
