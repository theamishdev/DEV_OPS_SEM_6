# Day 40: GitHub-Hosted vs. Self-Hosted Runners 🏃‍♂️🖥️

---

## 1. What is a Runner?

A runner is a virtual machine or server that hosts the GitHub Actions runner agent, pulling jobs from GitHub, executing steps, and reporting logs back to GitHub.

---

## 2. Comparing Runner Types

| Feature | GitHub-Hosted Runners | Self-Hosted Runners |
| :--- | :--- | :--- |
| **Hosting** | Hosted in Microsoft Azure (managed by GitHub). | Hosted on your own servers, local machine, VMs, or Kubernetes. |
| **Maintenance** | None. OS updates and packages are managed by GitHub. | High. You must patch the OS, install toolchains, and update agents. |
| **Costs** | Charged per minute of execution (free tier for public repos). | Free execution software, but you pay hosting/hardware costs. |
| **Performance** | Standard hardware sizes. Startup is immediate. | Highly customizable (run on huge servers, GPUs, local resources). |
| **Network Access**| Public internet only. Can't access private networks easily. | Can run inside private VPCs, local networks, or behind firewalls. |

---

## 3. Setting Up a Self-Hosted Runner

To add a self-hosted runner to your GitHub repository:
1. Navigate to **Settings ➔ Actions ➔ Runners** on your GitHub repository page.
2. Click **New self-hosted runner**.
3. Choose the operating system (Linux, macOS, Windows) and execute the configuration commands in your server's terminal:

### Setup Commands (e.g., Linux x64):
```bash
# Create directory and download package
mkdir actions-runner && cd actions-runner
curl -o actions-runner-linux-x64.tar.gz -L https://github.com/actions/runner/releases/download/v2.304.0/actions-runner-linux-x64.tar.gz

# Extract installer package
tar xzf ./actions-runner-linux-x64.tar.gz

# Configure runner (connects server to GitHub account/repository)
./config.sh --url https://github.com/myusername/my-repo --token MY_RUNNER_REGISTRATION_TOKEN

# Start the runner service
./run.sh
```

---

## 4. Runner Security & Management

> [!WARNING]
> **Self-hosted runners and Public Repositories:** Never use self-hosted runners for public repositories. Forked repositories can submit Pull Requests containing malicious workflow YAML code that executes arbitrary shell commands on your private server infrastructure.

### Security Best Practices:
1. **Use Ephemeral Runners:** Configure runners to run only one job and then auto-terminate and clean their files.
2. **Limit Privileges:** Run the runner agent process under a non-root user account with limited OS privileges.
3. **Network Isolation:** Restrict the runner VM from talking to internal corporate networks, except for mandatory endpoints.

---

## 5. Summary

- **GitHub-hosted runners** offer hassle-free, secure execution environments for standard CI builds.
- **Self-hosted runners** provide customized compute resources and network access to secure internal networks.
- Enforce **strict access and security rules** when using self-hosted runner fleets to prevent host execution breaches.
