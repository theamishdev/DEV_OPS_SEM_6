# Day 43: Jenkins Installation & Setup ⚙️🐳

---

## 1. Prerequisites for Jenkins

Since Jenkins is written in Java, you must have the Java Development Kit (JDK) installed on the system:
- Jenkins versions >= 2.357 require **Java 11 or Java 17**.

---

## 2. Installation Methods

### Method A: Running Jenkins as a Docker Container (Recommended)
This is the fastest, cleanest installation method as it packages all dependencies automatically.

```bash
docker run -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  --name my-jenkins \
  jenkins/jenkins:lts-jdk17
```

- `-p 8080:8080`: Exposes the Jenkins Web Dashboard.
- `-p 50000:50000`: Used by Jenkins master to communicate with inbound Java-based agents.
- `-v jenkins_home:/var/jenkins_home`: Persists all jobs, configurations, and plugins on the host machine.

---

### Method B: Installing on Linux VM (Ubuntu/Debian)

```bash
# 1. Install Java JDK 17
sudo apt update
sudo apt install openjdk-17-jdk -y

# 2. Add Jenkins Debian Repository GPG Key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# 3. Add Jenkins package repository
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# 4. Update apt database and install Jenkins
sudo apt update
sudo apt install jenkins -y

# 5. Start and enable Jenkins service
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

---

## 3. Initial Web Setup & Configuration

Once installed, open your browser and navigate to `http://localhost:8080` (or `http://your-server-ip:8080`).

### Step 1: Unlock Jenkins
Jenkins is locked to prevent unauthorized installation.
- Retrieve the initial administrator password from the server logs or file path:
  ```bash
  # Inside Docker container:
  docker exec my-jenkins cat /var/jenkins_home/secrets/initialAdminPassword
  
  # On standard Linux installation:
  sudo cat /var/lib/jenkins/secrets/initialAdminPassword
  ```
- Copy the 32-character string and paste it into the Web UI.

### Step 2: Install Suggested Plugins
- Select **Install Suggested Plugins** to install core dependencies (Git, Pipeline, Email Extension, etc.).

### Step 3: Create First Admin User
- Set up your administrator username, password, email address, and complete the setup.

---

## 4. Summary

- Run Jenkins inside **Docker** for clean dependency isolation.
- Open port **8080** to access the web console.
- Retrieve the setup password from `/var/jenkins_home/secrets/initialAdminPassword` to unlock the dashboard.
