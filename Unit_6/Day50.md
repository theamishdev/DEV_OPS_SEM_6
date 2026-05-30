# Day 50: Jenkins Distributed Builds (Master-Agent) 🖥️➕🖥️

---

## 1. What are Distributed Builds?

As development scale increases, running hundreds of build jobs on a single Jenkins controller server causes performance degradation, UI lag, or crashes due to resource exhaustion.
To solve this, Jenkins uses a **Distributed Build** model:
- The **Master** manages configuration, pipelines scheduling, and displays build logs.
- The **Agents (Executors)** run the actual build tasks.

---

## 2. Setting Up a Jenkins Agent Node

To add a new VM as a Jenkins agent:

### Step 1: Create Node in Jenkins UI
1. Go to **Manage Jenkins ➔ Manage Nodes and Clouds ➔ New Node**.
2. Name the node (e.g., `linux-agent-1`) and select **Permanent Agent**.
3. Configure the following fields:
   - **Remote root directory:** Path on the agent server where Jenkins will execute builds (e.g., `/home/jenkins/agent`).
   - **Labels:** Tag names used to identify this machine (e.g., `linux`, `high-cpu`).
   - **Launch method:** Select **Launch agents via SSH**.

### Step 2: Configure SSH Authentication
1. Enter the Agent VM's IP address in the **Host** field.
2. Select or add SSH credentials (SSH private key matching the public key authorized on the agent server).
3. Set **Host Key Verification Strategy** to *Non-verifying Verification Strategy* (for simple setups) or *Known hosts file Verification Strategy*.
4. Click **Save** and click **Launch Agent** to test connectivity.

---

## 3. Targeting Specific Nodes in a Jenkinsfile

You can restrict a pipeline or individual stage to execute only on a node with a specific label.

### Restricting the Entire Pipeline:
```groovy
pipeline {
    agent {
        label 'linux-agent-1' // Restricts all stages to run on this node
    }
    stages {
        stage('Build') {
            steps {
                sh 'echo "Running on linux-agent-1..."'
            }
        }
    }
}
```

### Dynamic Stage Routing (Heterogeneous Builds):
You can route different stages to run on different OS platforms:

```groovy
pipeline {
    agent none // Disable default global agent binding

    stages {
        stage('Linux Compilation') {
            agent { label 'linux' } // Runs only on Linux nodes
            steps {
                sh 'uname -a'
            }
        }

        stage('Windows Testing') {
            agent { label 'windows' } // Runs only on Windows nodes
            steps {
                bat 'dir'
            }
        }
    }
}
```

---

## 4. Summary

- **Distributed Builds** separate management (Master) from task execution (Agents).
- Connect agents securely using **Launch agents via SSH**.
- Bind pipelines or individual stages to nodes using **Labels** (e.g., `agent { label 'my-label' }`).
