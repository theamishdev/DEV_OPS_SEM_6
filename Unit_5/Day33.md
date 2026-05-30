# Day 33: Introduction to CI & GitHub Actions Directory 🔁📁

---

## 1. What is Continuous Integration (CI)?

Continuous Integration (CI) is a software development practice where developers merge their code changes into a central repository frequently (usually multiple times a day). 

Each merge triggers an automated build and test pipeline to verify code quality and detect integration bugs as early as possible.

```text
Developer pushes code ➔ Trigger CI Runner ➔ Checkout Code ➔ Compile ➔ Run Tests ➔ Build Artifact
```

---

## 2. What is GitHub Actions?

GitHub Actions is a SaaS CI/CD platform built directly into GitHub. It allows you to automate software workflows (build, test, deploy) directly from your repository in response to events (e.g., code push, new issue, release creation).

---

## 3. GitHub Actions Directory Structure

To define workflows in GitHub, you must place your YAML configuration files in a specific folder structure at the root of your project:

```text
my-project/
├── .github/              # Hidden directory at project root
│   └── workflows/        # Workflows configuration folder
│       ├── build.yml     # Workflow file 1
│       └── release.yml   # Workflow file 2
├── src/
└── pom.xml
```

- **Folder Path:** `.github/workflows/` (must be spelled exactly like this, in lowercase, with a dot before `github`).
- **File Extension:** `.yml` or `.yaml`.
- You can have multiple independent workflow files inside this directory.

---

## 4. Anatomy of a Minimal Workflow File

Here is a basic workflow file (`.github/workflows/hello-world.yml`):

```yaml
# Name of the workflow
name: Hello World CI

# Defines when the workflow runs
on: [push]

# List of jobs to execute
jobs:
  say-hello:
    # Operating system of the runner
    runs-on: ubuntu-latest

    # Sequence of tasks (steps) to execute
    steps:
      - name: Output message
        run: echo "Hello, GitHub Actions!"
```

---

## 5. Summary

- **Continuous Integration** automates compilation, verification, and testing of new changes.
- GitHub Actions reads workflow configurations from the **`.github/workflows/`** directory.
- Workflows are defined in **YAML** files.
