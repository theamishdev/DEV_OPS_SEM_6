# Day 37: Shell Commands & Marketplace Actions 🐚🛍️

---

## 1. Executing Shell Commands (`run`)

The `run` keyword executes a command-line script on the runner's shell. By default, it uses bash on Linux/macOS and PowerShell on Windows.

### Single-Line Command:
```yaml
- name: Print Working Directory
  run: pwd
```

### Multi-line Commands:
Use the YAML literal block operator `|` to run multiple commands:
```yaml
- name: Run Multiple Diagnostics
  run: |
    echo "Running system diagnostics..."
    uname -a
    df -h
```

---

## 2. Using Marketplace Actions (`uses`)

Instead of writing complex scripts for standard tasks, you can use **Actions** created by GitHub or the community. Actions are identified by their coordinate repository path and a version tag (e.g., `creator/repo-name@version`).

### Reusable Action Examples:
- **`actions/checkout@v3`:** Checks out repository code so the runner can access it.
- **`actions/upload-artifact@v3`:** Saves build files generated in the workspace to GitHub so they can be downloaded later.
- **`actions/download-artifact@v3`:** Retrieves previously uploaded build outputs.

```yaml
- name: Archive Production JAR
  uses: actions/upload-artifact@v3
  with:
    name: packaged-jar-file
    path: target/*.jar # Uploads matching files
```

---

## 3. Language-Specific Setup Actions

Most programming language ecosystems have official actions to set up compilers, runtime environments, and manage tool dependency caches.

### A. Java Setup (`actions/setup-java`)
Installs JDKs, configures environment variables (`JAVA_HOME`), and sets up Maven credentials.
```yaml
- name: Setup Java JRE
  uses: actions/setup-java@v3
  with:
    java-version: '17'
    distribution: 'temurin' # Alternatives: zulu, corretto, microsoft
```

### B. Node.js Setup (`actions/setup-node`)
Configures Node runtime versions and caches NPM packages.
```yaml
- name: Setup Node runtime
  uses: actions/setup-node@v3
  with:
    node-version: '18.x'
    cache: 'npm' # Automates cache management
```

### C. Python Setup (`actions/setup-python`)
Installs Python, configures path variables, and sets up Pip caches.
```yaml
- name: Setup Python
  uses: actions/setup-python@v4
  with:
    python-version: '3.10'
```

---

## 4. Summary

- **`run`** executes custom shell commands directly on the runner VM.
- **`uses`** references pre-packaged community code modules (Actions).
- Use official setup actions (like `setup-java` or `setup-node`) to ensure the correct compiler toolchains are configured before building.
