# Day 30: Maven Plugins & Execution 🔌⚙️

---

## 1. What are Maven Plugins?

All work in Maven is performed by **Plugins**. Plugins provide goals that bind to lifecycle phases.
- A **Plugin** is a collection of one or more **Goals**.
- A **Goal** is a specific task (like compiling code, or running tests).

### Example:
- The phase `compile` binds to `maven-compiler-plugin:compile`.
- The phase `test` binds to `maven-surefire-plugin:test`.

---

## 2. Core Maven Plugins Explained

### A. Maven Compiler Plugin (`maven-compiler-plugin`)
Used to compile the source code of your project. It lets you specify target Java versions.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.11.0</version>
            <configuration>
                <source>17</source>
                <target>17</target>
            </configuration>
        </plugin>
    </plugins>
</build>
```

### B. Maven Surefire Plugin (`maven-surefire-plugin`)
Used during the `test` phase of the lifecycle to execute unit tests (JUnit or TestNG). It generates plain-text and XML reports in `target/surefire-reports/`.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>3.0.0-M9</version>
</plugin>
```
*Note:* You can skip tests during building by running:
```bash
mvn package -DskipTests
```

### C. Maven Shade Plugin (`maven-shade-plugin`)
Used to create an **Uber JAR** (also called a Fat JAR or Shade JAR).
- **What is an Uber JAR?** A single, self-contained executable JAR file that packages your application classfiles along with ALL the classfiles from all your dependencies.
- This is useful for deployments because you only need to copy one single JAR file to run the entire app.

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>3.4.1</version>
    <executions>
        <execution>
            <phase>package</phase> <!-- Binds to package phase -->
            <goals>
                <goal>shade</goal>
            </goals>
            <configuration>
                <transformers>
                    <!-- Sets the Main Class for the executable JAR -->
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                        <mainClass>com.company.app.MainApp</mainClass>
                    </transformer>
                </transformers>
            </configuration>
        </execution>
    </executions>
</plugin>
```

---

## 3. Summary

- **Plugins** provide the actual execution engines for Maven lifecycles.
- **Surefire plugin** manages unit testing pipelines.
- **Shade plugin** merges class structures from multiple libraries into one single executable **Uber JAR**.
