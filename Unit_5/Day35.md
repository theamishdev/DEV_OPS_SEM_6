# Day 35: GitHub Actions Workflow Triggers 🚀⏱️

---

## 1. What are Workflow Triggers?

A workflow trigger is a specific event that tells GitHub Actions to start executing a workflow file. Triggers are declared under the `on` configuration key in the YAML file.

---

## 2. Common Types of Triggers

### A. Code Event Triggers (`push` & `pull_request`)
These triggers fire when developers push code changes or open/update Pull Requests (PRs). You can narrow triggers down to specific branches, paths, or tags.

```yaml
on:
  push:
    branches:
      - main
      - 'releases/**' # Matches releases/v1, releases/v2
    paths:
      - 'src/**'     # Run workflow only if files under src/ change
  pull_request:
    branches:
      - main         # Trigger only when PR is opened targeting main branch
```

### B. Scheduled Triggers (`schedule`)
Allows you to run workflows at specific UTC times using POSIX **cron syntax**.

```yaml
on:
  schedule:
    # Runs at 00:00 UTC every Sunday
    - cron: '0 0 * * 0'
```

#### Cron Syntax Breakdown:
```text
┌───────────── minute (0 - 59)
│ ┌─────────── hour (0 - 23)
│ │ ┌───────── day of the month (1 - 31)
│ │ │ ┌─────── month (1 - 12)
│ │ │ │ ┌───── day of the week (0 - 6, where 0 is Sunday)
│ │ │ │ │
* * * * *
```

### C. Manual Triggers (`workflow_dispatch`)
Allows developers to trigger the workflow manually from the GitHub web UI or via the GitHub API. You can also specify custom input parameters.

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: 'Environment to deploy'
        required: true
        default: 'staging'
        type: choice
        options:
          - staging
          - production
```

---

## 3. Multiple Triggers Combination

You can combine multiple triggers in a single workflow:

```yaml
name: Nightly Build and PR Verification

on:
  # Trigger on pushes to main
  push:
    branches: [main]
  # Trigger on any PR
  pull_request:
  # Run every night at 2:00 AM UTC
  schedule:
    - cron: '0 2 * * *'
  # Enable manual execution
  workflow_dispatch:
```

---

## 4. Summary

- **`push`** triggers CI pipelines on code commit integrations.
- **`pull_request`** triggers verification builds before code is merged.
- **`schedule`** uses standard cron patterns to trigger periodic jobs (e.g. nightly builds).
- **`workflow_dispatch`** enables manual trigger flows with customizable input fields.
