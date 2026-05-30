# Day 46: Jenkinsfile Structure & Post-Actions ⚙️🔔

---

## 1. Advanced Declarative Directives

A complete Declarative Pipeline utilizes several directives to configure environments, tools, and run-time parameters.

### A. Environment (`environment`)
Used to declare variables available to all steps or specific stages.
```groovy
environment {
    DB_NAME = 'testdb'
    API_KEY = credentials('my-api-key-id') // Retrives credential from Jenkins store
}
```

### B. Parameters (`parameters`)
Allows users to enter input variables when triggering a build manually (Build with Parameters).
```groovy
parameters {
    string(name: 'BRANCH', defaultValue: 'main', description: 'Branch to build')
    choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: 'Deploy Target')
}
```

### C. Tools (`tools`)
Automatically sets up build tool paths on the executor node.
```groovy
tools {
    maven 'M3' // Refers to the Maven installation name configured in Global Tool Config
    jdk 'JDK17'
}
```

---

## 2. Handling Conditional Execution (`post` Block)

The `post` section is defined at the end of the pipeline or inside a stage. It runs automatically depending on the execution outcome of the preceding steps.

```groovy
pipeline {
    agent any
    
    stages {
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }
    }

    // Post-execution hooks
    post {
        always {
            echo 'Wiping workspace resources...'
            cleanWs() // Deletes files to free up space
        }
        success {
            echo 'Build succeeded! Sending notification email...'
            mail to: 'devs@mycompany.com', subject: 'Build Success!'
        }
        failure {
            echo 'Build failed! Triggering alerts...'
        }
    }
}
```

### Common Post Hooks:
- **`always`:** Executes regardless of the completion status.
- **`success`:** Executes only if the current run completed successfully.
- **`failure`:** Executes only if the current run failed.
- **`changed`:** Executes only if the status of this run differs from the previous one.

---

## 3. Summary

- **`environment`** blocks decouple credentials and configuration parameters from code.
- **`tools`** automates JDK/Maven path configurations on Jenkins executors.
- Use **`post`** blocks to manage cleanups, email alerts, and webhook notifications based on job statuses.
