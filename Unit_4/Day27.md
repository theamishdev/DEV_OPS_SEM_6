# Day 27: Maven Dependency Scopes 🎯📦

---

## 1. What is Dependency Scope?

When you declare a dependency in `pom.xml`, you can specify its **scope**. The scope determines:
- Which classpaths the dependency is added to (Compile, Test, Runtime).
- Whether the dependency is packaged into the final artifact (JAR/WAR).

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter-api</artifactId>
    <version>5.9.2</version>
    <scope>test</scope>  <!-- Declaring scope here -->
</dependency>
```

---

## 2. The 6 Maven Dependency Scopes

Maven defines 6 scopes to control dependency inclusion:

### A. `compile` (Default Scope)
- If no scope is specified, `compile` is used.
- Available on **all classpaths** (Compilation, Testing, and Execution).
- Packaged with the application.
- *Example:* Lombok, Apache Commons.

### B. `provided`
- Used when you expect the JDK or the target container/application server (like Tomcat or WildFly) to provide the dependency at runtime.
- Available on compile and test classpaths, but **excluded from packaging**.
- *Example:* Servlet API (`javax.servlet-api`).

### C. `runtime`
- Not needed for compilation, but required for application execution.
- Available on runtime and test classpaths, but NOT on the compilation classpath.
- *Example:* JDBC Database Drivers (e.g., MySQL driver `mysql-connector-java`).

### D. `test`
- Only required to compile and run tests.
- Not available during normal application runtime, and **NOT packaged** into the final artifact.
- *Example:* JUnit, Mockito.

### E. `system`
- Similar to `provided`, but you must specify a local path to the library JAR file on the system using `<systemPath>`.
- *Caution:* Deprecated and should be avoided as it breaks build portability.

### F. `import`
- Only used within `<dependencyManagement>` inside a POM. It imports dependency configurations from a remote BOM (Bill of Materials) POM.

---

## 3. Scope Comparison Matrix

| Scope | Compile Classpath | Test Classpath | Runtime Classpath | Packaged in Artifact? |
| :--- | :---: | :---: | :---: | :---: |
| **`compile`** | Yes | Yes | Yes | **Yes** |
| **`provided`**| Yes | Yes | No | **No** |
| **`runtime`** | No | Yes | Yes | **Yes** |
| **`test`**    | No | Yes | No | **No** |

---

## 4. Summary

- Default scope is **`compile`**.
- Use **`test`** for mock and assertion frameworks.
- Use **`provided`** for servlet APIs or container-level libraries.
- Use **`runtime`** for database drivers to enforce clean architecture (coding against interfaces rather than direct implementation details).
