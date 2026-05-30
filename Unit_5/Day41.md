# Day 41: Docker & GitHub Actions CI/CD Integration 🐳🔁

---

## 1. Objective

Integrate Docker into a GitHub Actions CI pipeline. We will write a workflow that:
1. Triggers on a push to the `main` branch.
2. Builds a Docker image using a local `Dockerfile`.
3. Logs into a container registry.
4. Pushes the built image to Docker Hub and **GitHub Container Registry (GHCR)**.

---

## 2. Docker Registry Credentials

To push images, the runner needs authentication tokens.
- **Docker Hub:** Store `DOCKER_USERNAME` and `DOCKER_PASSWORD` (or Personal Access Token) as GitHub Encrypted Secrets.
- **GHCR:** GHCR is built into GitHub. We can authenticate using the automatic `${{ secrets.GITHUB_TOKEN }}` provided dynamically in every workflow run.

---

## 3. The GitHub Actions Workflow File

Create `.github/workflows/docker-publish.yml`:

```yaml
name: Build and Push Docker Images

on:
  push:
    branches: [main]

jobs:
  publish-image:
    runs-on: ubuntu-latest
    
    # Grant permissions needed to publish to GitHub Container Registry (GHCR)
    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      # 1. Set up Docker Buildx (advanced builder features)
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v2

      # 2. Login to Docker Hub
      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      # 3. Login to GitHub Container Registry (GHCR)
      - name: Login to GHCR
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }} # Automatically sets current username
          password: ${{ secrets.GITHUB_TOKEN }}

      # 4. Build and Push the Image
      - name: Build and Push Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: |
            docker.io/${{ secrets.DOCKER_USERNAME }}/my-app:latest
            ghcr.io/${{ github.repository }}/my-app:latest
```

---

## 4. Explaining the Workflow Steps

- **`docker/setup-buildx-action`:** Configures an build engine called Buildx, which enables build caching features and multi-platform compilation support.
- **`docker/login-action`:** Logs into registries. Using `ghcr.io` specifies the GitHub Container Registry endpoint.
- **`docker/build-push-action`:** Builds the container using build context, tags it, and pushes the final image to both repositories in parallel.

---

## 5. Summary

- Automating container compilation ensures that production releases are build-validated in clean, standardized environments.
- Use **GHCR** for package colocation inside the GitHub platform.
- Authenticate to GHCR securely using the automatic repository-scoped **`${{ secrets.GITHUB_TOKEN }}`** token.
