# Day 34: GitHub Actions Core Components ⚙️🧱

---

## 1. The GitHub Actions Component Model

GitHub Actions workflows are built on top of five primary concepts: **Workflows**, **Jobs**, **Steps**, **Actions**, and **Runners**.

```text
┌────────────────────────────────────────────────────────┐
│                      WORKFLOW                          │
│                                                        │
│  ┌───────────────────────┐   ┌───────────────────────┐  │
│  │         JOB 1         │   │         JOB 2         │  │
│  │   (Runs on Runner A)  │   │   (Runs on Runner B)  │  │
│  │                       │   │                       │  │
│  │  ┌─────────────────┐  │   │  ┌─────────────────┐  │  │
│  │  │  Step 1: Action │  │   │  │  Step 1: CMD    │  │  │
│  │  └─────────────────┘  │   │  └─────────────────┘  │  │
│  │  ┌─────────────────┐  │   │  ┌─────────────────┐  │  │
│  │  │  Step 2: Command│  │   │  │  Step 2: Action │  │  │
│  │  └─────────────────┘  │   │  └─────────────────┘  │  │
│  └───────────────────────┘   └───────────────────────┘  │
└────────────────────────────────────────────────────────┘
```

---

## 2. Deep Dive Into the 5 Components

### A. Workflow
- A configurable automated process that you add to your repository.
- Contains one or more jobs and is triggered by specific events (like pushing code).
- Configured as a `.yml` file in the `.github/workflows/` directory.

### B. Job
- A set of steps that execute on the **same runner instance**.
- By default, multiple jobs in a workflow run **in parallel** (concurrently).
- You can make jobs run sequentially by establishing dependencies using the `needs` keyword.
- Each job has a unique identifier (e.g., `build`, `test`, `deploy`).

### C. Step
- An individual task that runs a command or an action.
- Steps within a job run **sequentially** (one after another).
- Since steps run on the same runner, they share the local filesystem (e.g., compile files created in Step 1 are available to Step 2).

### D. Action
- A reusable component or package of code that performs a complex, common task (e.g., checking out your repository, setting up a JDK environment).
- You can write your own actions or use pre-built ones from the **GitHub Marketplace**.
- Referenced using the `uses` keyword (e.g., `uses: actions/checkout@v3`).

### E. Runner
- The server/virtual machine that runs your jobs when a workflow is triggered.
- Runners can be hosted by GitHub (e.g., `ubuntu-latest`, `windows-latest`) or hosted on your own infrastructure (Self-hosted runners).

---

## 3. Example Workflow Visualizing the Components

```yaml
name: Java CI Build

on: [push]

jobs:
  # Job definition
  build-and-test:
    runs-on: ubuntu-latest # Runner configuration

    steps: # Steps inside job
      # Step 1: Using a marketplace Action to pull repo code
      - name: Checkout Code
        uses: actions/checkout@v3

      # Step 2: Using an Action to set up Java JRE
      - name: Set up Java JDK
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'

      # Step 3: Running a shell command on the Runner
      - name: Build with Maven
        run: mvn clean package
```

---

## 4. Summary

- **Workflows** contain **Jobs**.
- **Jobs** run in parallel (by default) on separate **Runners**.
- **Steps** run sequentially inside a Job and share the local workspace files.
- **Actions** are reusable plugins used within steps.
