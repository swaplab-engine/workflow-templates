# SwapLab Engine: Integration Workflows

![Integration](https://img.shields.io/badge/integration-required-red.svg)
![Security](https://img.shields.io/badge/security-isolated-green.svg)
![Billing](https://img.shields.io/badge/billing-user%20minutes-blue.svg)

## 📖 Overview

This repository contains the standardized **GitHub Actions Workflows** required to connect your GitHub repository with the [SwapLab SaaS Build Service](https://swaplab.net).

These YAML files act as a **bridge**. They allow our UI to trigger secure build environments directly within **your** GitHub account. This ensures that all build processes run in isolated virtual machines under your control, using your own GitHub Actions quotas.

---

## ⚠️ CRITICAL: Integration Rules

To ensure the connection between the SwapLab Dashboard and your repository remains active, you must adhere to the following rule:

> **⛔ DO NOT RENAME THE YAML FILES**
>
> The filenames (e.g., `cordova-workflow-cache.yml`, `capacitor-workflow-cache.yml`) serve as **Unique Identifiers (IDs)** for our API triggers.
>
> * **❌ If you rename the file:** The "Select Builder" button on the dashboard will fail (404 Error).
> * **✅ Content Modification:** You generally should not modify the internal logic (`steps`), but you may add comments or change the display `name` inside the file if necessary.

---

## 📂 Available Workflows

Select and copy the workflow that matches your project requirements:

| Filename | Purpose | Recommended For |
| :--- | :--- | :--- |
| `cordova-workflow-cache.yml` | Standard Cordova build with **dependency caching** (Gradle/NPM). | **Most Users** (Faster builds) |
| `cordova-workflow-no-cache.yml` | Cordova build **without cache**. Forces fresh download of all deps. | Debugging dependency issues |
| `capacitor-workflow-cache.yml` | Standard Capacitor build with caching. | **Most Users** (Faster builds) |
| `capacitor-workflow-build-no-cache.yml`| Capacitor build **without cache**. | Debugging dependency issues |
| `both-workflow-repo-cache.yml` | Universal workflow for repositories containing both frameworks. | Hybrid Projects |

---

## 🚀 Setup Instructions

Choose the method that best fits your situation:

### Option A: Start with a Template (Recommended for New Projects)
If you are starting a fresh project, use our **Official Starter Kits**. These repositories already contain the correct workflow files pre-installed.

> **🔒 PRIVACY NOTE:** When forking a template, please set your new repository visibility to **PRIVATE** to protect your source code.


* **Capacitor:** [Use template-capacitor](https://github.com/swaplab-engine/template-capacitor) (React, Angular, Next.js, SolidJS)
* **Cordova:** [Use template-cordova](https://github.com/swaplab-engine/template-cordova)

### Option B: Manual Integration (For Existing Projects)
If you already have an existing repository and want to connect it to SwapLab:

1.  **Create Folder:** Inside your repository, create the directory structure: `.github/workflows/`.
2.  **Create File:** Create a new file matching one of the names above (e.g., `capacitor-workflow-cache.yml`).
3.  **Copy Content:** Copy the **exact content** from the corresponding file in this repository and paste it into your new file.
4.  **Commit:** Push the changes to your repository.
5.  **Connect:** Log in to [SwapLab.net](https://swaplab.net), select **Repository Builder**, and your repo will be ready to build.

---



## ⚙️ Build Ecosystem & Providers

SwapLab is designed to be runner-agnostic. While GitHub Actions is the default, we are expanding support to other providers to offer better performance options.

| Provider | Status | Specs (Free Tier) | Quota / Month | Best For |
| :--- | :--- | :--- | :--- | :--- |
| **GitHub** | ✅ **Default** | 2 vCPU, 7GB RAM | ~2,000 mins | **Standard Use.** integrated directly with your repo. Enough for ~400 builds/month. |
| **CircleCI** | 🚧 *Coming Soon* | Up to **4 vCPU, 16GB RAM** (Large) | 30,000 Credits (~6,000 mins) | **High Performance.** Includes *Docker Layer Caching* for blazing fast builds (skips re-downloading images). |
| **GitLab** | 🚧 *Coming Soon* | Shared Runners | ~400 mins | GitLab Users. |

---

## 💰 Usage & Billing

It is important to distinguish between **GitHub Costs** and **SwapLab Service Costs**.

### A. GitHub Actions Quota (Compute)
The build process runs inside *your* GitHub account using *your* minutes.
* **Free Accounts:** GitHub typically provides **2,000 free minutes/month**.
* **Capacity:** Approx. 300-400 builds/month (assuming 3-5 mins per build).
* **Upgrade:** If you exceed this, you pay GitHub directly via [GitHub Actions Billing](https://docs.github.com/en/billing/concepts/product-billing/github-actions).

### B. SwapLab Subscription (Orchestration)
We offer a **Free Tier** (Debug builds unlimited) and **Paid Tiers** (Release builds).
* **Philosophy:** Our fees are **not** for reselling GitHub minutes. They cover our physical resources (vCPU, RAM, NVMe, Storage) and the security infrastructure that orchestrates the build.
* **How to Upgrade:** You can view plans and upgrade your account directly within the **SwapLab Dashboard**.

---

## 🖥️ Advanced: Bring Your Own Runner (VPS)

For power users who need **Unlimited Builds** or extreme performance, you can run the SwapLab Engine on your own infrastructure (VPS/Dedicated Server) instead of using GitHub's shared runners.

### Why use Self-Hosted?
* **Unlimited Minutes:** Bypass GitHub's 2,000-minute monthly quota.
* **High Performance:** Use high-spec servers (e.g., 8 vCPU) for faster compilation.
* **Cost Control:** Flat monthly fee for your VPS, regardless of how many builds you run.

### ⚙️ Configuration Steps
1.  **Setup Runner:** Install the GitHub Actions Runner on your VPS (Settings > Actions > Runners > New self-hosted runner).
2.  **Update Workflow:** Edit your `.github/workflows/*.yml` file. Change the `runs-on` value:
    ```yaml
    jobs:
      build:
        # Change from 'ubuntu-latest' to 'self-hosted'
        runs-on: self-hosted 
    ```

### 💻 Recommended Hardware Specs
* **CPU:** 4 vCPU (Minimum)
* **RAM:** 8 GB (Minimum) - 16 GB (Recommended)
* **Storage:** 50 GB NVMe (Minimum) - **100 GB+ (Recommended)**

> **⚠️ CRITICAL MAINTENANCE WARNING: DISK SPACE**
> Self-hosted runners persist data between builds. Docker images and Gradle caches can quickly fill up your storage.
> * **Cleanup:** You must implement aggressive cleanup scripts (e.g., `docker system prune -af`) to avoid "No Space Left on Device" errors.
> * **Recommendation:** Use **100GB+ NVMe** if possible. You are responsible for monitoring your own server's disk usage.

---

## 🔐 Security & Privacy Architecture

We prioritize data privacy by isolation. Here is how the data flows when you trigger a build:

The build engine used to process these templates is part of the SwapLab Open Ecosystem. Our architecture prioritizes **Zero-Knowledge Storage** and **Just-In-Time (JIT) Security**.


<img width="2048" height="1117" alt="diagram" src="https://github.com/user-attachments/assets/fc878e6c-b39c-44d0-836f-328c05452163" />


### 1. Isolated Execution
Unlike traditional cloud builders that queue everyone's code on a single central server, SwapLab triggers a **private runner inside your own GitHub account**.
* Your code remains in your ecosystem.
* No other SwapLab user can access your build environment.

### 2. Ephemeral Source Handling (R2)
For builds triggered via **Zip Upload**
1.  Your project is uploaded to a secure, temporary R2 Storage bucket.
2.  The runner downloads and unzips the project.
3.  **IMMEDIATE DELETION:** The runner immediately sends a signal to delete the source file and keystore from R2 storage *before* the build even starts.

> **Log Verification:** You will see this confirmation in the Live Logs:
> ```text
> > Requesting immediate deletion of source files from R2 storage...
> > ✅ Source files deletion successfully requested.
> ```

### 3. Log Suppression & Live Streaming
To prevent sensitive data leakage (especially if you accidentally make your repository Public), the **GitHub Actions Job Logs are suppressed**.
* The standard GitHub console will only show generic Docker pull logs.
* Real-time build logs are encrypted and streamed exclusively to the **SwapLab Live Dashboard**.

---




## 🛡️ NPM Script Security Policy

To prevent Supply Chain Attacks (malicious post-install scripts), our build engine enforces a strict **Allowlist** for `package.json` scripts.

If your build fails with `SECURITY ALERT: Unrecognized command`, please ensure your scripts start with one of the following allowed prefixes:

| Category | Allowed Prefixes |
| :--- | :--- |
| **Package Managers** | `npm run`, `npx`, `yarn`, `pnpm` |
| **Frameworks** | `next`, `ng`, `ionic`, `react-scripts`, `vue-cli-service`, `vite`, `nuxt`, `gatsby`, `webpack` |
| **Utilities** | `tsc` (TypeScript), `serve` |
| **Testing/Linting** | `eslint`, `prettier`, `jest`, `cypress`, `vitest`, `lint` |
| **Standard** | `dev`, `build`, `start`, `test`, `preview` |

> **Need a new script allowed?**
> If your project uses a legitimate tool not listed here, please open a [Feature Request Issue](https://github.com/swaplab-engine/workflow-templates/issues/new) to verify and add it.





## ⚖️ Service Limits & Fair Use Policy

To ensure service stability, speed, and fairness for all SwapLab users, we enforce strict resource limits at multiple layers of our infrastructure.

Our services are protected by **Cloudflare WAF** (Web Application Firewall) and internal **Server-Side Rate Limiting**.

### 1. Infrastructure Protection (The Front Door)
We utilize enterprise-grade protection to mitigate DDoS attacks and bot traffic before they reach our core servers.
* **DDoS Protection:** Automatic mitigation of high-volume traffic spikes.
* **Bot Fight Mode:** Aggressive challenges (CAPTCHA) for suspicious non-human traffic.
* **WAF Rules:** Blocks malicious URI patterns and SQL injection attempts.

### 2. API Rate Limits (Server-Side)
We enforce the following strict limits per IP address/User ID to prevent abuse and ensure fair resource allocation (vCPU/RAM) for everyone.

| Action | Limit | Cooldown / Reset | Purpose |
| :--- | :--- | :--- | :--- |
| **Global API Requests** | 30 requests | per 10 seconds | Prevents Denial-of-Service (DoS) attacks on our API. |
| **Build Triggers** | **3 builds** | per **3 minutes** | **Fairness & Burst Policy.** Allows you to trigger parallel builds (e.g., Debug & Release simultaneously), but prevents queue flooding. |
| **Login Failures** | 10 attempts | Blocked for **1 hour** | **Brute Force Protection.** Protects your account from password guessing. |

> **⚠️ Note on Build Limits & Stuck Processes:**
>
> 1.  **Rate Limit:** You can trigger up to **3 builds per 3 minutes**. Note that using "Force Stop" still counts as 1 attempt against your quota.
> 2.  **Stuck in Live Logs?** If you uploaded the wrong file or the UI is stuck, click the **Profile Icon** (Sidebar) → select **Force Stop Build**. This will exit the log stream and return you to the upload form immediately.
> 3.  **UI Timeout:** The Live Log Streaming view has a hard limit of **30 minutes** (`MAX_BUILD_DURATION`). If a build takes longer, the UI will automatically reset for safety.

---


## 🔗 Legal & Governance

By using SwapLab services and this build environment, you agree to our policies. Please review the documents below for detailed information regarding data handling, repository access, and usage terms.

* **📄 Privacy Policy** [Read Privacy Policy](https://swaplab.net/privacy-policy/privacy-policy.html)
* **⚖️ Terms and Conditions** [Read Terms & Conditions](https://swaplab.net/privacy-policy/terms-and-conditions.html)
* **🔐 Repository Permissions** [View Repository Permissions Policy](https://swaplab.net/privacy-policy/repository-permissions.html)


## 👨‍💻 About the Creator

SwapLab is built and maintained by **EMI (EMI-INDO)**, a dedicated developer in the Hybrid Mobile App ecosystem.

This service was built to solve the real-world build problems I faced while developing plugins and games.

* **Cordova Plugins:** I maintain various open-source [Cordova Plugins on GitHub](https://github.com/EMI-INDO?tab=repositories).
* **Game Assets:** Verified seller of [Construct 3 Addons](https://www.construct.net/en/game-assets/users/emiindo-378213).
* **Community:** Active member of the [Construct Community Forums](https://www.construct.net/en/forum).

---
<p align="center">
  Provided by the <b>SwapLab Engineering Team</b>
</p>