# 🔧 Self-Healing CI/CD Pipeline with AI

> An autonomous pipeline that detects, analyzes, and fixes build failures using AI — no human intervention required.

---

## 📌 Overview

This project demonstrates how to build a **fully autonomous, self-healing CI/CD pipeline** by integrating **GitHub Actions**, **OpenAI**, and **n8n**. When a pipeline failure occurs, instead of alerting a developer, the system:

1. **Detects** the failure via webhook
2. **Analyzes** the logs using AI
3. **Generates** a patch/fix automatically
4. **Opens a Pull Request** with the fix on GitHub

---

## 🎯 Problem Statement

Traditional CI/CD pipelines stop on failure and wait for a human to debug. This causes:
- ❌ Increased downtime
- ❌ Developer time wasted on repetitive debugging
- ❌ Slower release cycles

This project solves that by making the pipeline **intelligent and self-correcting**.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| **GitHub Actions** | Triggers CI/CD pipeline on code push |
| **n8n** | Workflow automation — catches webhook on failure |
| **OpenAI API** | Analyzes logs and generates code fixes |
| **Node.js / Express** | Backend application being tested & deployed |
| **GitHub Secrets** | Secures API keys and credentials |
| **GitHub API** | Auto-creates Pull Requests with AI-generated fixes |
| **Slack Webhooks** | Sends real-time notifications on failure, fix, and PR status |

---

## 🏗️ Architecture

```
Code Push
    │
    ▼
GitHub Actions (CI/CD Pipeline)
    │
    ├── ✅ Success → Deploy
    │
    └── ❌ Failure → Webhook Triggered
                          │
                          ▼
                      n8n Workflow
                          │
                          ├──► 📣 Slack: "Failure Detected — AI Analyzing..."
                          │
                          ▼
                    OpenAI Log Analysis
                          │
                          ▼
                    AI Generates Fix?
                          │
                ┌─────────┴──────────┐
               YES                   NO
                │                    │
                ▼                    ▼
        Auto PR Opened       📣 Slack: "Manual Review Needed"
                │
                ├──► 📣 Slack: "Fix Ready — Review PR #42"
                │
                ▼
         Pipeline Re-runs ✅
```

---

## 🚀 Getting Started

### Prerequisites

- Node.js (v18+)
- GitHub Account
- OpenAI API Key
- n8n (self-hosted or cloud)

### Installation

```bash
# Clone the repository
git clone https://github.com/your-username/self-healing-cicd.git
cd self-healing-cicd

# Install dependencies
npm install
```

### Environment Variables

Create a `.env` file in the root directory:

```env
OPENAI_API_KEY=your_openai_api_key
GITHUB_TOKEN=your_github_personal_access_token
GITHUB_REPO=your-username/your-repo-name
N8N_WEBHOOK_URL=your_n8n_webhook_url
SLACK_WEBHOOK_URL=your_slack_incoming_webhook_url
```

> ⚠️ Never commit `.env` to version control. Use **GitHub Secrets** for production.

---

## ⚙️ How It Works

### Step 1 — GitHub Actions Pipeline
The pipeline runs on every push. On failure, it sends a POST request to the n8n webhook URL with the failure logs.

### Step 2 — n8n Catches the Failure
n8n receives the webhook, extracts the error logs, and forwards them to the OpenAI API.

### Step 3 — AI Analyzes & Fixes
OpenAI reads the logs and generates:
- A diagnosis of the failure
- A code patch to resolve the issue

### Step 4 — Auto Pull Request
The system uses the GitHub API to create a new branch, commit the fix, and open a Pull Request automatically.

### Step 5 — Slack Notifications (Added Feature ⭐)
At every key event, a Slack message is sent to your team channel:

| Event | Slack Message |
|---|---|
| ❌ Failure detected | `"Pipeline failed — AI is analyzing logs..."` |
| ✅ Fix generated | `"AI fix ready — PR #42 opened for review"` |
| ⚠️ AI confidence low | `"Manual review needed — AI could not auto-fix"` |

This was added as an original contribution on top of the base project, giving teams **real-time visibility** without having to check GitHub manually.

---

## 📁 Project Structure

```
self-healing-cicd/
├── .github/
│   └── workflows/
│       └── ci.yml          # GitHub Actions workflow
├── src/
│   ├── app.js              # Express application
│   └── routes/             # API routes
├── n8n/
│   └── workflow.json       # Exportable n8n workflow
├── scripts/
│   ├── auto-fix.js         # AI fix + PR creation logic
│   └── slack-notify.js     # ⭐ Slack notifications (added feature)
├── .env.example            # Sample environment file
├── package.json
└── README.md
```

---

## 🌍 Real-World Impact

| Benefit | Description |
|---|---|
| ⏱️ **Saves Time** | Eliminates manual debugging of repetitive failures |
| 🔄 **Faster Recovery** | Pipeline self-corrects within minutes |
| 💰 **Reduces Costs** | Less developer time on firefighting |
| 🚀 **Boosts Velocity** | Teams focus on features, not failures |
| 🔒 **Secure** | Credentials managed via GitHub Secrets |

---

## 📣 Setting Up Slack Notifications

### Step 1 — Create a Slack Incoming Webhook
1. Go to [api.slack.com/apps](https://api.slack.com/apps) → **Create New App**
2. Choose **From Scratch** → name it (e.g. `CI/CD Bot`) → select your workspace
3. Go to **Incoming Webhooks** → toggle **Activate Incoming Webhooks** ON
4. Click **Add New Webhook to Workspace** → pick a channel (e.g. `#ci-alerts`)
5. Copy the **Webhook URL** — it looks like:
   ```
   https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX
   ```

### Step 2 — Add to GitHub Secrets
In your repo: **Settings → Secrets → Actions → New secret**
```
Name:  SLACK_WEBHOOK_URL
Value: https://hooks.slack.com/services/your/webhook/url
```

### Step 3 — Test It Locally
```bash
# Test failure notification
SLACK_WEBHOOK_URL=your_url node scripts/slack-notify.js failure

# Test fix applied notification
SLACK_WEBHOOK_URL=your_url node scripts/slack-notify.js fixed

# Test fix failed notification
SLACK_WEBHOOK_URL=your_url node scripts/slack-notify.js failed
```

### What You'll See in Slack

**On Failure:**
> 🚨 **Pipeline Failure Detected**
> Repository: `your-repo` | Branch: `main` | Commit: `abc1234`
> 🤖 Self-healing agent is now analyzing the failure...

**On Fix:**
> ✅ **AI Fix Generated & PR Opened**
> PR #42 is ready for review → [Review Pull Request]

**On Low Confidence:**
> ⚠️ **AI Could Not Auto-Fix — Action Required**
> Manual review needed.

---

## 📚 Learning Resources

- [freeCodeCamp Course (YouTube)](https://youtu.be/vj68el9hRvU)
- [GitHub Actions Docs](https://docs.github.com/en/actions)
- [n8n Documentation](https://docs.n8n.io)
- [OpenAI API Reference](https://platform.openai.com/docs)

---

## 🤝 Contributing

Pull requests are welcome! For major changes, please open an issue first to discuss what you'd like to change.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

## 👤 Author

Built with ❤️ while learning from the freeCodeCamp Self-Healing CI/CD course by **Farzeen Ali**.

---

> *"Don't just automate your deployments — make them intelligent."*
