# Day 36: Jobs Coordination & Matrix Strategies 🔀🧮

---

## 1. Job Orchestration: Sequential vs. Parallel

By default, all jobs in a GitHub Actions workflow execute in parallel. To make a job depend on the completion of another (sequential flow), use the `needs` keyword.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building..."

  test:
    runs-on: ubuntu-latest
    needs: build # Will only run if the 'build' job succeeds
    steps:
      - run: echo "Testing..."

  deploy:
    runs-on: ubuntu-latest
    needs: [build, test] # Requires both compile and test to pass
    steps:
      - run: echo "Deploying..."
```

---

## 2. Matrix Strategies (Multi-Environment Testing)

A **Matrix strategy** allows you to test your application against multiple combinations of operating systems, language versions, tool versions, or dependency configurations in a single job definition.
GitHub Actions automatically generates multiple jobs based on the Cartesian product of the variables you declare.

### Example Matrix Workflow Configuration:

```yaml
name: Matrix Test Suite

on: [push]

jobs:
  run-tests:
    strategy:
      # If one job in the matrix fails, cancel all other running jobs (default: true)
      fail-fast: false
      
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        java-version: [11, 17, 21]

    runs-on: ${{ matrix.os }} # Interpolates OS parameter

    steps:
      - name: Checkout Code
        uses: actions/checkout@v3

      - name: Setup Java JRE
        uses: actions/setup-java@v3
        with:
          java-version: ${{ matrix.java-version }} # Interpolates Java version
          distribution: 'zulu'

      - name: Run test suite
        run: mvn test
```

- **Total Jobs Spawned:** 3 (OS versions) × 3 (Java versions) = **9 parallel jobs**!

---

## 3. Matrix Customization: Include and Exclude

You can add specific variables to a configuration, or filter out certain combinations that are not supported or are redundant.

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node-version: [16, 18, 20]
    
    # Exclude a specific invalid combination
    exclude:
      - os: windows-latest
        node-version: 16 # Don't test Node 16 on Windows

    # Include custom parameters for specific platforms
    include:
      - os: ubuntu-latest
        node-version: 20
        deploy-target: true # Adds custom property only to this configuration
```

---

## 4. Summary

- Use **`needs`** to chain jobs together and establish pipeline stages (Build ➔ Test ➔ Deploy).
- Use **`strategy.matrix`** to run test configurations across various environments concurrently.
- Set **`fail-fast: false`** if you want to inspect failures on all configurations, preventing a single failure from aborting the entire matrix.
