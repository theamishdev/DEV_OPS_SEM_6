# Day 42: Introduction to Jenkins & Architecture 🏗️⚙

---

## 1. What is Jenkins?

Jenkins is an open-source automation server written in Java. It is used to automate the software development lifecycle (SDLC) processes, including:
- Building code.
- Running unit and integration tests.
- Analyzing code quality.
- Deploying artifacts to cloud platforms or production servers.

### History:
- Originally created in 2004 by Kohsuke Kawaguchi under the name **Hudson**.
- Renamed to **Jenkins** in 2011 after a community dispute with Oracle (which acquired Sun Microsystems).
- Today, it is one of the most widely used self-hosted build automation platforms.

---

## 2. Core Architecture: Jenkins Master-Agent Model

To handle heavy build volumes, Jenkins separates orchestration from execution using a distributed **Master-Agent** (formerly Master-Slave) architecture.

```text
               ┌──────────────────────┐
               │    JENKINS MASTER    │  <-- Schedules jobs, UI, stores configurations
               └──────────┬───────────┘
                          │
         ┌────────────────┼────────────────┐ (TCP / SSH Connection)
         ▼                ▼                ▼
┌────────────────┐ ┌────────────────┐ ┌────────────────┐
│ JENKINS AGENT  │ │ JENKINS AGENT  │ │ JENKINS AGENT  │  <-- Compiles, runs tests
│ (Linux VM)     │ │ (Windows VM)   │ │ (Docker Node)  │
└────────────────┘ └────────────────┘ └────────────────┘
```

### A. Jenkins Master (Controller)
- The main control plane of the Jenkins installation.
- **Roles:**
  - Serves the Jenkins Web UI dashboard.
  - Hosts configuration settings and manages user authentication.
  - Schedules build jobs.
  - Monitors the state of connected agents.

### B. Jenkins Agents (Nodes)
- Small agent service applications running on separate servers.
- **Roles:**
  - Pull code from source repositories.
  - Execute build, test, and packaging commands sent by the Master.
  - Report console logs and build outcomes back to the Master.
- **Benefits:** Prevents the Master from running out of CPU/Memory by offloading resource-heavy compilations to separate execution machines.

---

## 3. Jenkins Plugins System

Jenkins is highly customizable because of its **Plugin Architecture**.
- Out-of-the-box, Jenkins is basic.
- Over **1,800+ plugins** are available in the Jenkins Update Center to integrate it with version control systems (Git, SVN), build tools (Maven, Gradle), containerization (Docker, Kubernetes), and cloud providers (AWS, Azure, GCP).

---

## 4. Summary

- **Jenkins** is an industry-standard self-hosted CI/CD automation server.
- The **Master** manages configurations and schedules builds; **Agents** execute the build commands.
- The **Plugin System** allows Jenkins to extend its capabilities to integrate with virtually any development stack.
