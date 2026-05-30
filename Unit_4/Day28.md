# Day 28: Transitive Dependencies & Conflict Resolution 🔀🛡️

---

## 1. What are Transitive Dependencies?

If your project depends on **Library A**, and **Library A** depends on **Library B**, then your project transitively depends on **Library B**.
Maven automatically downloads all transitive dependencies for you.

```text
[ Your Project ] ➔ [ Library A ] ➔ [ Library B (Transitive) ]
```

---

## 2. Version Conflicts (The Diamond Dependency Problem)

What happens if your project depends on both **Library A** and **Library B**, and both of them depend on **Library C**, but different versions of it?

```text
                  ┌──> [ Library A ] ──> [ Library C (v1.0) ]
[ Your Project ] ─┤
                  └──> [ Library B ] ──> [ Library C (v2.0) ]  <-- Conflict!
```

---

## 3. Maven's Conflict Resolution: Nearest-Wins Strategy

Maven resolves dependency conflicts using the **"Nearest Wins" (Dependency Depth)** strategy. It selects the version of the artifact that is closest to your project root in the dependency tree.

### Example Scenario:
- **Path 1:** Project ➔ Library A ➔ Library C (v1.0) *(Depth: 2)*
- **Path 2:** Project ➔ Library B ➔ Library D ➔ Library C (v2.0) *(Depth: 3)*
- **Result:** Maven chooses **Library C (v1.0)** because Path 1 has a depth of 2 (closer to project), while Path 2 has a depth of 3.

*What if paths have the same depth?* The first declaration in the `pom.xml` wins.

---

## 4. How to Manually Overrule Resolution

### A. Explicit Declaration
You can declare the desired version directly in your project's `pom.xml`. Since it has a depth of 1 (nearest of all), it will always win.

### B. Dependency Exclusions
You can tell Maven to ignore specific transitive dependencies using `<exclusions>`:

```xml
<dependency>
    <groupId>com.example</groupId>
    <artifactId>library-a</artifactId>
    <version>1.5.0</version>
    <exclusions>
        <exclusion>
            <groupId>org.invalid</groupId>
            <artifactId>transitive-dep</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

---

## 5. Dependency Management (`<dependencyManagement>`)

The `<dependencyManagement>` section is used to centralize and declare versions of dependencies. It **does not** add the dependency to the project classpath by itself. Instead, it defines a lookup version template.

### Example (Parent POM):
```xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>6.0.4</version>
        </dependency>
    </dependencies>
</dependencyManagement>
```

### Example (Child POM):
The child POM can declare the dependency without specifying a version, inheriting the version defined in the parent's `dependencyManagement`.
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <!-- Version inherited automatically from parent -->
    </dependency>
</dependencies>
```

---

## 6. Summary

- **Transitive dependencies** are imported automatically by Maven.
- Maven resolves version disputes using **Nearest Wins** in the dependency hierarchy tree.
- Use **`<dependencyManagement>`** in parent POMs to declare standard versions across multiple sub-modules.
