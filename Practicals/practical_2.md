# Practical 2: Multi-Stage Production CI/CD Pipeline 🚀⚙️

---

## 1. Problem Statement

1. **Pipeline Design:** Design an automated pipeline that implements continuous integration, continuous delivery, and continuous deployment for the Flask app.
2. **Quality Gate (CI):** Implement a testing stage that automatically executes `pytest` tests on a clean runner environment. If testing fails, the pipeline must halt and prevent image building.
3. **Container Publication (CD - Delivery):** Implement a stage that builds the Docker image and pushes it to Docker Hub using secure credentials. Use two tags: `latest` for production deployment and `github.sha` for unique versioning.
4. **Server Deployment (CD - Deployment):** Implement a deployment stage that connects to a remote Virtual Machine via SSH, pulls the latest built image from Docker Hub, and safely restarts the application container on port 80.
5. **Security Management:** Set up GitHub Actions repository secrets for sensitive Docker Hub and SSH server credentials.

---

## 2. CI/CD Pipeline Architecture

```text
                     [ Developer Pushes to main ]
                                 │
                                 ▼
┌─────────────────────────────────────────────────────────────────┐
│                     GITHUB ACTIONS PIPELINE                     │
│                                                                 │
│ ┌────────────────┐        ┌────────────────┐        ┌─────────┐ │
│ │  Job 1: Test   │───────>│ Job 2: Build & │───────>│ Job 3:  │ │
│ │  - Setup Py    │(Pass)  │        Push    │(Pass)  │ Deploy  │ │
│ │  - Run Pytest  │        │ - Login Hub    │        │ - SSH VM│ │
│ └────────────────┘        │ - Build & Push │        │ - Pull  │ │
│                           └────────────────┘        │ - Run   │ │
│                                                     └─────────┘ │
└─────────────────────────────────────────────────────────────────┘
                                                           │
                                                           ▼
                                                   [ Target VM Host ]
                                                   (App running on :80)
```

---

## 3. Configuration & Test Code

Ensure the following files are present inside the `my-docker-app` directory:

### A. Python Unit Test (`test_app.py`)
```python
import pytest
from app import app

@pytest.fixture
def client():
    with app.test_client() as client:
        yield client

def test_home_page(client):
    response = client.get('/')
    assert response.status_code == 200
    assert response.data == b"Hello from Docker CI/CD!"
```

### B. Modified Dependencies (`requirements.txt`)
```text
Flask
pytest
```

---

## 4. The Complete GitHub Actions Workflow (`.github/workflows/ci-cd.yml`)

```yaml
name: CI-CD Production Pipeline

on:
  push:
    branches:
      - main

jobs:
  # Job 1: Run unit tests (Continuous Integration)
  test:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
          cache: 'pip'

      - name: Install Dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run Unit Tests
        run: pytest

  # Job 2: Build & Push Image (Continuous Delivery)
  build-and-push:
    runs-on: ubuntu-latest
    needs: test # Executes only if the test job passes
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      - name: Log in to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: |
            ${{ secrets.DOCKER_USERNAME }}/my-docker-app:latest
            ${{ secrets.DOCKER_USERNAME }}/my-docker-app:${{ github.sha }}

  # Job 3: Deploy App to Remote Server (Continuous Deployment)
  deploy:
    runs-on: ubuntu-latest
    needs: build-and-push # Executes only if publish job succeeds
    steps:
      - name: Deploy to Server via SSH
        uses: appleboy/ssh-action@v0.1.10
        with:
          host: ${{ secrets.SERVER_IP }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: |
            # Pull the latest image
            docker pull ${{ secrets.DOCKER_USERNAME }}/my-docker-app:latest
            
            # Stop and remove existing container if it exists
            docker stop flask-app || true
            docker rm flask-app || true
            
            # Run the new container
            docker run -d \
              -p 80:5000 \
              --name flask-app \
              --restart always \
              ${{ secrets.DOCKER_USERNAME }}/my-docker-app:latest
```

---

## 5. Setting Up GitHub Action Repository Secrets

To allow the workflow to securely communicate with Docker Hub and your host VM server:

1. Open your repository on GitHub.
2. Go to **Settings ➔ Secrets and variables ➔ Actions**.
3. Click **New repository secret** and add the following keys:

| Secret Key | Example Value | Description |
| :--- | :--- | :--- |
| **`DOCKER_USERNAME`** | `myusername` | Your Docker Hub account ID. |
| **`DOCKER_PASSWORD`** | `dckr_pat_xxxxxx` | Docker Hub Access Token (PAT). |
| **`SERVER_IP`** | `203.0.113.15` | The public IP of the remote deployment server. |
| **`SERVER_USER`** | `ubuntu` | SSH login username of the VM. |
| **`SSH_PRIVATE_KEY`** | `-----BEGIN OPENSSH...` | The SSH private key corresponding to the public key authorized on the VM. |

---

## 6. How the Pipeline Execution Works

1. **Trigger:** A developer pushes code changes to the `main` branch.
2. **Testing (CI):** The `test` job initializes an Ubuntu VM runner. It checkouts the code, sets up Python, installs dependencies from `requirements.txt`, and executes `pytest`. If any test fails, the runner stops, sending a failure alert.
3. **Docker Publish (CD):** Once tests pass, the `build-and-push` job logs into Docker Hub using secrets. It compiles the Docker image and pushes the image tagged as `latest` and `github.sha` (git commit hash) to Docker Hub.
4. **Deploy (CD):** The `deploy` job uses the `appleboy/ssh-action` to open an SSH tunnel to the deployment server. It logs in using the private key, pulls the fresh image from Docker Hub, stops the old container, and starts a new container mapped to production port `80`.

---

## 7. Key Viva / Interview Questions

### Q1: Why do we use `github.sha` in tags alongside `latest`?
Using only the `latest` tag overrides the image history. Using the git commit hash (`github.sha`) ensures every built image is uniquely tagged. This makes rollback scenarios simple (e.g., you can deploy a specific commit hash image if a release fails).

### Q2: What does `needs: test` do in the workflow file?
By default, GitHub Actions jobs run in parallel. The `needs` keyword establishes sequential execution dependencies. It ensures compilation and deployment stages only trigger if the testing stage passes.

### Q3: Why is `appleboy/ssh-action` used instead of raw SSH commands?
Using raw SSH commands requires writing complex bash logic inside the workflow to configure the SSH agent, handle keys, and handle Host Key Verification. The `appleboy` Action packages this securely and efficiently, managing the connection parameters natively.
