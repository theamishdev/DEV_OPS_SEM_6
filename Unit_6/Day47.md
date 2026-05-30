# Day 47: Jenkins GitHub Integration & Webhooks 🔁🌐

---

## 1. Triggering Jenkins Jobs Automatically

Running build jobs manually in Jenkins is inefficient. Instead, we configure Jenkins to monitor Git repositories and trigger builds automatically when developers submit changes.

There are two main methods to automate triggers: **SCM Polling** (Pull) and **Git Webhooks** (Push).

---

## 2. Method 1: SCM Polling (Pull Method)

Jenkins periodically polls (queries) the remote Git repository to check if there are any new commits.

- **How to configure:** Under job properties, check **Poll SCM** and enter a cron-style schedule.
- **Cron example:** `H/5 * * * *` (polls every 5 minutes).

```text
┌────────────────┐                     ┌───────────────┐
│ JENKINS SERVER │ ──(Poll: new code?)──> │  GITHUB REPO  │
└────────────────┘                     └───────────────┘
```

### Disadvantages:
- **Inefficient:** Causes unnecessary traffic and CPU consumption if no code is pushed.
- **Delay:** If a change is pushed, the build will not start until the next polling cycle.

---

## 3. Method 2: GitHub Webhooks (Push Method - Recommended)

The GitHub server notifies Jenkins instantly when a developer pushes code.

```text
┌───────────────┐                       ┌────────────────┐
│  GITHUB REPO  │ ──(HTTP POST Webhook)─> │ JENKINS SERVER │
└───────────────┘                       └────────────────┘
```

### Steps to Configure GitHub Webhooks:

#### Step 1: Configure Jenkins Job
1. In your Jenkins job configuration, navigate to **Build Triggers**.
2. Enable **GitHub hook trigger for GITScm polling**.

#### Step 2: Configure Webhook on GitHub
1. Open your repository on GitHub.
2. Go to **Settings ➔ Webhooks ➔ Add webhook**.
3. Set **Payload URL** to: `http://<your-jenkins-public-ip>:8888/github-webhook/` (port is usually 8080 or custom port).
4. Set **Content type** to `application/json`.
5. Under "Which events would you like to trigger this webhook?", select **Just the push event**.
6. Click **Add webhook**.

*Note:* If Jenkins is running on local `localhost`, you must use a proxy tool like **Ngrok** to expose it to the internet so GitHub servers can send the HTTP POST payload to it.

---

## 4. Summary

- **SCM Polling** checks for updates on a timer; it is resource-intensive and slow.
- **GitHub Webhooks** push events instantly, creating immediate, event-driven pipelines.
- Ensure the webhook URL ends with `/github-webhook/` for correct routing in Jenkins.
