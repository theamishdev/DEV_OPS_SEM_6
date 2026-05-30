# Day 48: Jenkins & Maven Integration ⚙️☕

---

## 1. Configuring Maven in Jenkins

Before a Jenkins pipeline can execute Maven commands, the Maven toolchain must be registered on the Jenkins controller.

### Step 1: Global Tool Configuration
1. Go to **Manage Jenkins ➔ Global Tool Configuration**.
2. Scroll to the **Maven** section.
3. Click **Add Maven**.
4. Name the installation (e.g., `M3` or `Maven_3.9`).
5. Check **Install automatically** and select the version (e.g., `3.9.1`).
6. Click **Save**.

---

## 2. Using Maven in a Declarative Pipeline

Once defined, you load Maven into your pipeline script using the `tools` directive:

```groovy
pipeline {
    agent any
    
    tools {
        # Loads Maven installation matching the name configured in Global Tools
        maven 'Maven_3.9' 
        jdk 'JDK17'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/myusername/my-java-app.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
    }
}
```

---

## 3. Best Practice: Archiving Test & Build Outputs

We can configure Jenkins to save the generated JAR file and parsing test results:

```groovy
pipeline {
    agent any
    tools { maven 'Maven_3.9' }
    
    stages {
        stage('Build & Test') {
            steps {
                sh 'mvn clean package'
            }
            post {
                always {
                    # Parses JUnit XML test results and prints reports on dashboard
                    junit '**/target/surefire-reports/*.xml'
                    
                    # Saves the compiled JAR file on Jenkins server
                    archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                }
            }
        }
    }
}
```

---

## 4. Summary

- Register Maven under **Global Tool Configuration** first.
- Reference it in a `Jenkinsfile` using the **`tools`** block.
- Use **`junit`** step to display test coverage statistics and **`archiveArtifacts`** to store the built package.
