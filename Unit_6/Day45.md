# Day 45: Declarative vs. Scripted Pipelines 💻📜

---

## 1. Two Types of Pipeline Syntax

Jenkins supports two types of syntaxes to write a `Jenkinsfile`: **Declarative** and **Scripted**.

```text
               ┌───────────────────────┐
               │   JENKINS PIPELINE    │
               └───────────┬───────────┘
                           │
             ┌─────────────┴─────────────┐
             ▼                           ▼
┌─────────────────────────┐ ┌─────────────────────────┐
│  DECLARATIVE PIPELINE   │ │    SCRIPTED PIPELINE    │
│  - Strict structure     │ │  - Groovy-based code    │
│  - Easier to write/read │ │  - Highly customized    │
│  - Modern standard      │ │  - Legacy support       │
└─────────────────────────┘ └─────────────────────────┘
```

---

## 2. Declarative Pipeline (Modern Standard)

Provides a simplified, structured, and opinionated syntax. It makes it easier to read and write pipeline configurations.

### Declarative Anatomy:
```groovy
pipeline {
    agent any // Declares where to run the job (any available agent)

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/my-user/my-repo.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}
```

### Core blocks:
- **`pipeline`:** The root block containing all declarations.
- **`agent`:** Specifies where the pipeline runs (e.g., `any`, `none`, or a specific node label).
- **`stages`:** Groups one or more `stage` blocks.
- **`stage`:** A logical segment of the pipeline (e.g., Build, Test, Deploy).
- **`steps`:** The actual commands (DSL scripts or shell steps) executed in the stage.

---

## 3. Scripted Pipeline (Legacy/Advanced)

Uses a procedural execution model powered by Apache Groovy. It is highly flexible but has a steeper learning curve and is harder to maintain.

### Scripted Anatomy:
```groovy
node { // Specifies node selection
    try {
        stage('Checkout') {
            checkout scm
        }
        stage('Build') {
            sh 'mvn clean package'
        }
    }
    catch (err) {
        currentBuild.result = 'FAILURE'
        throw err
    }
}
```

---

## 4. Syntax Comparison Matrix

| Aspect | Declarative Pipeline | Scripted Pipeline |
| :--- | :--- | :--- |
| **Structure** | Strict pre-defined block structure. | Free-form Groovy code. |
| **Complexity** | Easier for beginners. | Requires understanding of Groovy programming. |
| **Error Handling** | Handled natively via `post` actions. | Handled via try-catch-finally code blocks. |
| **Validation** | Schema checked before execution. | Errors caught only at runtime during execution. |

---

## 5. Summary

- **Declarative Pipelines** are highly readable and the standard for most modern CI/CD needs.
- **Scripted Pipelines** provide extreme flexibility using raw Apache Groovy scripts, but require careful coding.
- Always wrap declarative steps inside `pipeline { ... }` blocks.
