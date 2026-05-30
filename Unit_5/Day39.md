# Day 39: Multi-Job Pipelines, Artifacts, & Secrets 🏗️🔑

---

## 1. Sharing Files Between Jobs (Artifacts)

Because every job runs on a fresh, isolated runner VM, files compiled in one job (e.g., a `build` job) are not accessible to subsequent jobs (e.g., a `deploy` job).
To bridge this gap, we use **GitHub Artifacts** to upload files to a shared storage pool and download them.

```text
Job 1: Build ➔ Output: app.jar ➔ Upload to GitHub Artifacts
                                            │
Job 2: Deploy ➔ Download from GitHub Artifacts ➔ Deploy to Host
```

### Configuration Snippet:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: mvn clean package
      
      # Upload the compiled JAR
      - name: Upload Build Artifact
        uses: actions/upload-artifact@v3
        with:
          name: my-app-jar
          path: target/my-app.jar

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      # Retrieve the uploaded JAR
      - name: Download Build Artifact
        uses: actions/download-artifact@v3
        with:
          name: my-app-jar
          path: deploy-folder/
```

---

## 2. Managing Environment Secrets

Never hardcode API keys, passwords, or deployment credentials in your workflow YAML files. Instead, store them in the **Encrypted Secrets** vault in your GitHub repository configuration page.

### Referencing Secrets in Workflows:
Access secrets in your YAML file using the `secrets` context:

```yaml
steps:
  - name: Deploy to Cloud VM
    run: |
      echo "${{ secrets.SSH_PRIVATE_KEY }}" > private_key.pem
      ssh -i private_key.pem ${{ secrets.VM_USER }}@${{ secrets.VM_HOST }} "docker run -d my-app"
```

---

## 3. GitHub Environments

Environments are used to set deployment rules and manage deployment-specific secrets (e.g., Development, Staging, Production).

```yaml
jobs:
  deploy-to-prod:
    runs-on: ubuntu-latest
    environment:
      name: Production
      url: https://my-production-app.com # Link printed on GitHub Run UI
    steps:
      - name: Deploy
        run: ./deploy.sh
```

### Environment Safeguards:
- **Required Reviewers:** Stops the deployment until an administrator approves the release manually.
- **Wait Timer:** Pauses execution for a specified duration.

---

## 4. Summary

- Use **`actions/upload-artifact`** and **`actions/download-artifact`** to pass binaries between runner VMs.
- Store production credentials in **GitHub Secrets** and read them using `${{ secrets.MY_SECRET_NAME }}`.
- Bind jobs to specific **Environments** to enforce approvals and isolation policies before deployments.
