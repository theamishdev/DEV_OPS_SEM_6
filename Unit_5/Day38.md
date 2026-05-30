# Day 38: Workflow Optimization & Dependency Caching ⚡💾

---

## 1. The Cost of Fresh Builds

In a clean CI runner instance, every build downloads all dependencies from public repositories (e.g., Maven Central or NPM registry) over the network.
- For a medium-sized project, this downloading phase can take several minutes, consuming network bandwidth and slowing down the developer feedback cycle.
- **Solution:** Store these packages in a persistent cache location provided by GitHub Actions and reuse them in subsequent runs if the dependency files (like `pom.xml` or `package-lock.json`) have not changed.

---

## 2. Using the Generic `actions/cache` Action

The `actions/cache` plugin allows you to cache custom folders based on a unique cache key.

```yaml
- name: Cache Maven Local Repository
  uses: actions/cache@v3
  with:
    # Path of directory to cache
    path: ~/.m2/repository
    
    # Key representing the cache state (hashes the pom.xml file)
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
    
    # Fallback keys to use if there is no exact cache match
    restore-keys: |
      ${{ runner.os }}-maven-
```

### How Cache Keys Work:
- **`hashFiles('**/pom.xml')`:** Creates a cryptographic hash of your dependency declaration files.
- If dependencies change, the hash changes, generating a new cache key.
- If dependency files remain unchanged, the hash matches, and Maven restores the cache folder instantly.

---

## 3. Simplified Ecosystem Setup Caching

Modern setup actions (like `setup-java` and `setup-node`) now offer built-in caching configuration, making it unnecessary to write long `actions/cache` blocks.

### A. Caching Maven with `setup-java`:
```yaml
- name: Setup Java with caching
  uses: actions/setup-java@v3
  with:
    java-version: '17'
    distribution: 'temurin'
    cache: 'maven' # Automatically caches ~/.m2/repository
```

### B. Caching NPM with `setup-node`:
```yaml
- name: Setup Node with caching
  uses: actions/setup-node@v3
  with:
    node-version: '18'
    cache: 'npm' # Automatically caches ~/.npm directory
```

---

## 4. Cache Expiry & Limits

- **Cache Limit:** GitHub allows up to 10 GB of cache space per repository.
- **Cache Eviction:** If the repository exceeds this size limit, GitHub will delete the oldest caches to free up space.
- **Cache Age:** Caches that have not been accessed for over 7 days are automatically purged.

---

## 5. Summary

- **Caching** dramatically decreases CI pipeline execution times.
- Tie the cache key directly to the cryptographic hash of dependency files (e.g., `pom.xml` or `package-lock.json`).
- Prefer the **built-in `cache` option** in setup actions for simpler configuration.
