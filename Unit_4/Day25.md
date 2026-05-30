# Day 25: Maven Core - The POM & Directory Layout 📁📜

---

## 1. What is the Project Object Model (POM)?

The Project Object Model (POM) is the fundamental unit of work in Maven. It is an XML file named `pom.xml` located in the project's root directory. It contains configuration details used by Maven to build the project, such as:
- Project dependencies and plugins.
- Project coordinates (identifying keys).
- Build configurations and active profiles.

---

## 2. Structure of `pom.xml`

Here is a standard, bare-minimum `pom.xml` file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
    
    <modelVersion>4.0.0</modelVersion>

    <!-- 1. Maven Coordinates -->
    <groupId>com.company.app</groupId>
    <artifactId>my-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <!-- 2. Project Description -->
    <name>My Application</name>
    <description>A simple Java application built with Maven</description>

    <!-- 3. Properties (e.g. Java Version) -->
    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <!-- 4. Dependencies -->
    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter-api</artifactId>
            <version>5.9.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

</project>
```

### Explaining Coordinates (GAV):
Maven uses three coordinates to uniquely identify any artifact (project or library) in the universe:
- **`groupId`:** Identifies the organization or company (e.g., `com.google`, `org.apache`). Usually follows reverse domain naming.
- **`artifactId`:** Name of the project or module (e.g., `guava`, `maven-core`).
- **`version`:** Version of the project (e.g., `1.0.0`, `2.4-SNAPSHOT`). A `SNAPSHOT` version indicates that it is under active development.

---

## 3. Maven Standard Directory Layout

Maven enforces **Convention over Configuration**. If your project folder follows Maven's standard naming structure, you do not have to write custom scripts to specify where the source code or resources reside.

```text
my-project/
├── pom.xml                   # Project Configuration
├── src/                      # Source files directory
│   ├── main/                 # Production-ready code
│   │   ├── java/             # Java source files (*.java)
│   │   └── resources/        # Config files, properties, static assets
│   └── test/                 # Test code and mock resources
│       ├── java/             # Test files (JUnit tests)
│       └── resources/        # Resources used only during testing
└── target/                   # Generated artifacts (created on compilation)
    ├── classes/              # Compiled .class files
    └── my-app-1.0.jar        # Final packed package
```

- **`src/main/java`:** Production code goes here.
- **`src/main/resources`:** Files here will be placed in the final classpath (e.g., database connection configs).
- **`src/test/java`:** Code that executes unit tests. Excluded from final package.
- **`target/`:** Output folder. Created when you compile/package, and can be completely wiped clean via `mvn clean`.

---

## 4. Summary

- **`pom.xml`** configures everything about a Maven build.
- **GAV coordinates** (`groupId`, `artifactId`, `version`) identify artifacts.
- **Convention over Configuration** saves time by using a standard directory layout.
