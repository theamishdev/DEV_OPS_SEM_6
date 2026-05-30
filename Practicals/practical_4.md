# Practical 4: Automated Java Build Pipeline with Maven & GitHub Actions ☕⚙️

---

## 1. Problem Statement

Create a GitHub Actions workflow configuration file (`.github/workflows/ci.yml`) that performs the following automated tasks upon pushes or pull requests targeting the `main` branch:
1. Checks out the source code from the repository.
2. Sets up the appropriate Java Development Kit (JDK) version required for the project (JDK 17).
3. Installs all necessary project dependencies using Maven (with local caching enabled).
4. Compiles the Java application and executes unit tests (JUnit 5) using Maven lifecycle commands.
5. Archives the generated build artifacts (the final executable `.jar` file) for future release use.

---

## 2. Project Directory Layout

The Java Maven application files are structured as follows inside the `my-maven-app` workspace directory:

```text
my-maven-app/
├── pom.xml
├── src/
│   ├── main/
│   │   └── java/
│   │       └── com/
│   │           └── example/
│   │               └── App.java
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   └── AppTest.java
└── .github/
    └── workflows/
        └── ci.yml
```

---

## 3. Implementation Code

### A. Maven Configurations (`pom.xml`)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>my-maven-app</artifactId>
    <version>1.0.0</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- JUnit 5 dependencies for unit testing -->
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.9.2</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-engine</artifactId>
            <version>5.9.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.11.0</version>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.0.0-M9</version>
            </plugin>
        </plugins>
    </build>
</project>
```

### B. Java Main Application (`App.java`)
```java
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello from Maven automated build!");
    }

    public int add(int a, int b) {
        return a + b;
    }
}
```

### C. JUnit 5 Test Class (`AppTest.java`)
```java
package com.example;

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class AppTest {
    @Test
    public void testAdd() {
        App app = new App();
        assertEquals(5, app.add(2, 3), "2 + 3 should equal 5");
    }
}
```

### D. GitHub Actions Workflow Configuration (`ci.yml`)
```yaml
name: Java CI Build with Maven

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Step 1: Checkout code from the repository
      - name: Checkout Code
        uses: actions/checkout@v4

      # Step 2: Set up Java JDK 17 with Temurin distribution
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: 'maven' # Step 3: Cache dependency downloads to speed up builds

      # Step 4: Compile the application and execute JUnit tests
      - name: Build and Test with Maven
        run: mvn -B clean package

      # Step 5: Archive the compiled JAR file for future deployment use
      - name: Archive Build Artifacts
        uses: actions/upload-artifact@v3
        with:
          name: my-maven-app-jar
          path: target/*.jar
```

---

## 4. Explaining the Workflow Step-by-Step

* **`on.push.branches` & `on.pull_request.branches`**: Triggers this workflow whenever code is merged or a PR is generated targeting the default `main` branch.
* **`uses: actions/checkout@v4`**: Pulls the project source code onto the GitHub runner VM so that the subsequent Maven commands can access the codebase.
* **`uses: actions/setup-java@v3`**: Downloads, installs, and configures the environment path for JDK 17. The `cache: 'maven'` parameter configures the action to save a hash of `pom.xml` dependencies and store the `~/.m2/repository` files in the GitHub cache.
* **`run: mvn -B clean package`**:
  * `mvn clean`: Wipes existing compiled files (`/target/`) to ensure a fresh compilation.
  * `mvn package`: Resolves dependencies, compiles classfiles, runs all unit tests, and compresses the outputs into a `.jar` package inside the `/target/` folder.
  * `-B`: Non-interactive batch mode (avoids spamming progress bars in CI logs).
* **`uses: actions/upload-artifact@v3`**: Saves the generated JAR file from the builder runner's transient filesystem back to the GitHub dashboard interface, preserving the compiled artifact for 90 days.

---

## 5. Local Compilation & Verification

You can verify the build commands locally by running them in your project directory:

```bash
# 1. Compile the project files
mvn compile

# 2. Execute unit tests
mvn test

# 3. Compile, test, and package into a JAR
mvn clean package
```

* **What they do:**
  * `mvn compile`: Compiles `.java` files into `.class` files in `/target/classes/`.
  * `mvn test`: Runs `AppTest.java` and prints results to console.
  * `mvn clean package`: Combines clean, compile, test, and packages the app as `target/my-maven-app-1.0.0.jar`.

---

## 6. Key Viva / Interview Questions

### Q1: What is the benefit of the `mvn -B` flag in CI/CD pipelines?
The `-B` (or `--batch-mode`) flag stops Maven from printing download progress indicator lines (percent completion text) to stdout. This keeps build execution logs clean, concise, and easy to parse inside the CI/CD console logs.

### Q2: What is the difference between the Maven `package` phase and the `install` phase?
* `mvn package` compiles the code, runs tests, and packages it in its distributable format (e.g., JAR) inside the project's local `/target` folder.
* `mvn install` performs the package phase first, then installs the package into the local Maven repository path (`~/.m2/repository`) so it can be reference-imported as a dependency by other Java projects on the same machine.

### Q3: Why is caching Maven dependencies important in a GitHub Action job?
By default, GitHub runner VMs start as fresh nodes, meaning they have to download every single plugin and library declared in `pom.xml` over the internet. Caching the local `.m2` folder saves these files, reducing build execution times from minutes to seconds.
