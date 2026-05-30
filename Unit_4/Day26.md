# Day 26: Maven Build Lifecycles & Phases 🔄⚙️

---

## 1. Concept: Maven Lifecycles

Maven has three built-in lifecycles:
1. **default:** Handles project deployment (compilation, testing, packaging, etc.).
2. **clean:** Handles project cleaning (deletes the generated `target` directory).
3. **site:** Handles the creation of project documentation pages.

Each lifecycle is made of a sequence of **phases**. When you execute a phase command (e.g., `mvn package`), Maven runs **every phase preceding it** in the lifecycle order before executing the target phase.

---

## 2. The `default` Lifecycle Phases

The default lifecycle has many phases, but these 8 are the most critical:

| Lifecycle Phase | Description |
| :--- | :--- |
| **`validate`** | Validates the project is correct and all necessary information is available. |
| **`compile`** | Compiles the source code of the project (`src/main/java` -> `target/classes`). |
| **`test`** | Runs tests using a suitable testing framework (e.g., JUnit). |
| **`package`** | Takes the compiled code and packages it in its distributable format (e.g., JAR, WAR). |
| **`verify`** | Runs checks on results of integration tests to ensure quality criteria are met. |
| **`install`** | Installs the package into the **local repository** (`~/.m2/repository`) for use as a dependency in other local projects. |
| **`deploy`** | Copies the final package to the **remote repository** (e.g., Nexus, Artifactory, Maven Central) for sharing with other developers. |

### Execution Hierarchy Example:
If you run:
```bash
mvn package
```
Maven will execute:
`validate` ➔ `compile` ➔ `test` ➔ `package` sequentially.

---

## 3. The `clean` Lifecycle Phases

Used to clear up the project workspace.
- **`pre-clean`:** Prepares for cleaning.
- **`clean`:** Deletes the `target` folder.
- **`post-clean`:** Finalizes cleaning processes.

### Common Multi-Lifecycle Command:
```bash
mvn clean package
```
- First, the **clean** lifecycle executes its `clean` phase (deleting the old `target/` folder).
- Then, the **default** lifecycle executes all phases up to `package`, producing a clean, fresh JAR.

---

## 4. Where Maven Stores Dependencies

- **Local Repository:** Located at `~/.m2/repository` (Windows: `C:\Users\<username>\.m2\repository`). It caches all downloaded dependencies locally so they don't need to be downloaded again for other projects.
- **Central Repository:** The public remote repository managed by Maven community (`https://repo.maven.apache.org/`).
- **Remote Repository:** Custom repository (like Nexus) hosted by an organization.

---

## 5. Summary

- Run `mvn compile` to check syntax/compilation.
- Run `mvn test` to execute unit tests.
- Run `mvn clean install` to rebuild the project and make it available locally.
- Remember: **Executing a phase automatically runs all prior phases in that lifecycle.**
