# Day 29: Parent POM & Multi-Module Maven Projects 👨‍👩‍👧‍👦⚙️

---

## 1. What is a Parent POM?

In enterprise environments with multiple Maven projects, writing the same dependency declarations, version lists, and plugin configurations across multiple `pom.xml` files leads to duplication and config drifts.
A **Parent POM** is a common configuration file that sub-projects (child modules) inherit from.

### Properties Inherited by Child POMs:
- Dependencies and Dependency Management configurations.
- Plugin configurations.
- Properties (e.g., target Java versions).
- Repository configurations.

---

## 2. Parent POM Example (`pom.xml`)

A Parent POM has its packaging set to `<packaging>pom</packaging>`:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.mycompany</groupId>
    <artifactId>parent-project</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>  <!-- Essential for Parents -->

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.apache.commons</groupId>
                <artifactId>commons-lang3</artifactId>
                <version>3.12.0</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
```

---

## 3. Child POM Inheritance Example

To inherit from the parent, the child POM defines a `<parent>` block:

```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    
    <!-- Parent declaration -->
    <parent>
        <groupId>com.mycompany</groupId>
        <artifactId>parent-project</artifactId>
        <version>1.0.0</version>
        <relativePath>../pom.xml</relativePath> <!-- Path to Parent file -->
    </parent>

    <artifactId>child-service-api</artifactId>
    
    <dependencies>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <!-- Version is inherited from parent POM -->
        </dependency>
    </dependencies>
</project>
```

---

## 4. Multi-Module Projects (Aggregators)

In a microservice codebase, you might group multiple services (modules) in a single repository. An **Aggregator POM** is used to compile, test, and package all services with a single command.

```text
my-monorepo/
├── pom.xml (Parent & Aggregator)
├── auth-service/
│   └── pom.xml
└── payment-service/
    └── pom.xml
```

### The Aggregator POM Config:
```xml
<project>
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.mycompany</groupId>
    <artifactId>aggregator-root</artifactId>
    <version>1.0.0</version>
    <packaging>pom</packaging>

    <!-- Defining child modules -->
    <modules>
        <module>auth-service</module>
        <module>payment-service</module>
    </modules>
</project>
```

### Running the build:
Running `mvn clean package` at the root folder will automatically build and package `auth-service` followed by `payment-service` in the correct order based on inter-module dependencies.

---

## 5. Summary

- Parent POMs must declare `<packaging>pom</packaging>`.
- Use `<parent>` in child projects to inherit properties, dependency coordinates, and build configurations.
- Use `<modules>` in an aggregator POM to run operations across multiple sub-modules concurrently.
