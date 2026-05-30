# Practical 1: Flask Application Containerization & GitHub Actions CI/CD 🐳🔁

---

## 1. Problem Statement

1. **Application Setup:** Create a simple Python Flask web application that serves a home page message `"Hello from Docker CI/CD!"` on port `5000`.
2. **Containerization:** Write a `Dockerfile` to package this application into a lightweight Docker image using `python:3.9-slim`.
3. **Local Testing:** Build and run the Docker container locally and verify the application is accessible at `http://localhost:5000`.
4. **CI/CD Integration:** Set up a GitHub Actions workflow (`ci-cd.yml`) to automatically check out the code, build the Docker image, and list images on an Ubuntu runner every time code is pushed to the `main` branch.
5. **Git Configuration & Troubleshooting:** Initialize a local Git repository, configure correct user coordinates, set up remote GitHub URL, configure branch tracking, and address potential network proxy conflicts that could interrupt pushing code to GitHub.

---

## 2. Project File Structure

The project files are laid out as follows inside the `my-docker-app` workspace directory:

```text
my-docker-app/
├── app.py
├── requirements.txt
├── Dockerfile
└── .github/
    └── workflows/
        └── ci-cd.yml
```

---

## 3. Code Implementation

### A. Python Flask Web Application (`app.py`)
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def home():
    return "Hello from Docker CI/CD!"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000)
```

### B. Dependencies Configuration (`requirements.txt`)
```text
Flask
```

### C. Docker Engine Configuration (`Dockerfile`)
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

### D. GitHub Actions Workflow (`.github/workflows/ci-cd.yml`)
```yaml
name: CI-CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Build Docker Image
        run: docker build -t my-docker-app .

      - name: List Docker Images
        run: docker images
```

---

## 4. Step-by-Step Commands & Solutions

### Step 1: Initialize Git Repository & Configure User Settings
Run these commands in the root of the project to initialize Git and configure identity logs:

```bash
# 1. Initialize Git local repository
git init

# 2. Configure Git username
git config --global user.name "preetkaur18"

# 3. Configure Git email
git config --global user.email "wahlamanpreet18@gmail.com"
```

* **What it does:** `git init` creates a hidden `.git` folder in your project, starting version control. The `git config` commands attach your name and email to your commit logs.

---

### Step 2: Local Docker Verification & Run (Important for Viva)
Test the application locally inside Docker before committing changes to GitHub:

```bash
# 1. Build the Docker Image
docker build -t my-docker-app .

# 2. Run the Docker Container mapping port 5000
docker run -d -p 5000:5000 --name flask-container my-docker-app
```

* **What it does:**
  * `docker build -t my-docker-app .`: Compiles your local folder into a Docker image tagged `my-docker-app` using the `Dockerfile` in the current directory (`.`).
  * `docker run -d -p 5000:5000`: Launches the container in the background (`-d`), mapping port 5000 of the host machine to port 5000 of the container.
* **Verification:** Open your browser and navigate to `http://localhost:5000`. You should see the message: `Hello from Docker CI/CD!`.

---

### Step 3: Git Remote Setup & Proxy Troubleshooting
Configure your local repository to link with the remote GitHub server, resolving potential network proxy issues:

```bash
# 1. Add remote origin repository URL
git remote add origin https://github.com/preetkaur18/my-docker-app.git

# 2. Set/Override the origin URL (if already exists)
git remote set-url origin https://github.com/preetkaur18/my-docker-app.git

# 3. Reset Windows WinHTTP proxy (clears system network configurations)
netsh winhttp reset proxy

# 4. View active environment proxies (PowerShell command)
gci env: | findstr -i proxy

# 5. Clear environment variables for HTTP/HTTPS proxies
[Environment]::SetEnvironmentVariable("HTTP_PROXY", "", "User")
[Environment]::SetEnvironmentVariable("HTTPS_PROXY", "", "User")
```

* **What it does:**
  * `git remote add/set-url`: Associates your local repository with your GitHub repository.
  * Proxy commands: Resolves connection timeouts when trying to authenticate or push code to GitHub through proxy environments.

---

### Step 4: Staging, Committing, and Pushing Code

```bash
# 1. Change branch to default 'main' branch
git branch -M main

# 2. Stage all project files
git add .

# 3. Commit staged files
git commit -m "Initial commit and CI/CD workflow configuration"

# 4. Push code to remote repository (bypassing any Git proxy configurations)
git -c http.proxy= -c https.proxy= push -u origin main
```

* **What it does:**
  * `git branch -M main`: Renames the default branch to `main`.
  * `git add .`: Packages all untracked files into the Git stage area.
  * `git commit`: Saves a snapshot of changes with an explanation message.
  * `git -c http.proxy= ... push`: Bypasses any configured Git proxies and pushes code directly to GitHub.

---

## 5. Verification on GitHub

1. Open your repository at `https://github.com/preetkaur18/my-docker-app`.
2. Navigate to the **Actions** tab.
3. You should see a workflow run triggered by your commit message.
4. Click on the run, open the **build** job, and expand **Build Docker Image** and **List Docker Images** steps to verify the build completed successfully.
