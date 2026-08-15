# ⚡ JobEasyApply — Local-First AI Job Application Agent for LinkedIn, Greenhouse & Lever

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Chrome Web Store](https://img.shields.io/badge/Chrome_Web_Store-v4.0.0-green.svg)](https://chromewebstore.google.com/detail/jobeasyapply-%E2%80%94-ai-job-app/aegajdjmemfodffcjbcbefkppjobeeoa)
[![Official Website](https://img.shields.io/badge/Website-jobeasyapply.com-purple.svg)](https://jobeasyapply.com)
[![Trustpilot](https://img.shields.io/badge/Trustpilot-4.8%20%E2%98%85-00b67a.svg)](https://jobeasyapply.com)

**JobEasyApply** is an autonomous, browser-native automation engine that fills and submits job applications across **LinkedIn Easy Apply**, **Greenhouse Boards** (GitLab, Stripe, Datadog), and **Lever Portals** (Palantir, Spotify) using resume-grounded AI screening answers.

Unlike cloud-based apply bots that route requests through datacenter IPs (triggering bot flags and account restrictions), JobEasyApply operates as a **local Manifest v3 Chrome extension** within your authenticated browser session.

🔗 **Live Platform:** [jobeasyapply.com](https://jobeasyapply.com)  
📦 **Chrome Web Store Extension:** [Install JobEasyApply](https://chromewebstore.google.com/detail/jobeasyapply-%E2%80%94-ai-job-app/aegajdjmemfodffcjbcbefkppjobeeoa)

---

## 🏗️ Architecture & Engineering Design

                 ┌──────────────────────────────────────────────┐
                 │         User's Chrome Browser (Local)        │
                 │                                              │
                 │  ┌─────────────────┐    ┌─────────────────┐  │
                 │  │   LinkedIn Web  │    │ Greenhouse/Lever│  │
                 │  │   Active Tab    │    │   Active Tab    │  │
                 │  └────────┬────────┘    └────────┬────────┘  │
                 │           ▲                      ▲           │
                 │           │ Synthetic Events     │           │
                 │           │ (Pointer/Input/Blur) │           │
                 │  ┌────────┴──────────────────────┴────────┐  │
                 │  │   JobEasyApply Engine (ats/linkedin)   │  │
                 │  │   • DOM / Combobox Traversal           │  │
                 │  │   • Profile-Aware Smart Answering      │  │
                 │  │   • Live Floating HUD & Review Mode    │  │
                 │  └────────────────┬───────────────────────┘  │
                 └───────────────────┼──────────────────────────┘
                                     │
                     HTTPS + AES-256 │ Queued Jobs & Embeddings
                                     ▼
                 ┌──────────────────────────────────────────────┐
                 │         JobEasyApply Backend Server          │
                 │                                              │
                 │  ┌─────────────────┐    ┌─────────────────┐  │
                 │  │ Celery Matching │    │ PostgreSQL      │  │
                 │  │ + 40% Buffer Q  │◄───┤ + pgvector      │  │
                 │  └─────────────────┘    └─────────────────┘  │
                 └──────────────────────────────────────────────┘

### 1. Zero-Cloud-Credential Execution (Anti-Ban)
* **No Password or Session Sharing:** The backend never receives, stores, or proxies your LinkedIn session tokens.
* **Authentic Fingerprinting:** Every request originates from your real residential IP, active cookies, TLS fingerprint, and screen viewport.
* **Human-Paced Interaction:** Form filling uses randomized human delays (150ms–600ms per input) with synthetic `mousedown`, `mouseup`, `focus`, `input`, and `change` event sequences to satisfy React/Vue synthetic event listeners.

### 2. Solving Greenhouse & Lever Combobox Traps
Modern ATS boards (Greenhouse, Lever, Ashby) do not use native `<select>` dropdowns. Instead, they render custom virtualized comboboxes with dynamic ARIA listboxes. JobEasyApply solves this through a multi-tier event dispatcher:
1. Simulates pointer interaction on the combobox trigger.
2. Focuses and injects character inputs matching target country/residence values.
3. Listens for rendered option items and triggers native click and change events with custom blur triggers.

### 3. Profile-Aware Smart Answering
Instead of pasting brittle canned strings, the engine extracts structured candidate metadata:
* **Dynamic Language Extraction:** Matches tech stack questions directly against your parsed resume skills (e.g. `Python, TypeScript, React, Go, SQL`).
* **Experience Computation:** Dynamically answers numeric and boolean experience questions based on work history.
* **Safety Memory:** Persists work authorization, visa sponsorship, and salary floor preferences once across hundreds of applications.

### 4. 40% Buffer Queue Fault Tolerance
Job listings close unexpectedly. If a user has a daily target of 10 applications, the backend generates **14 candidate slots** ($\text{Limit} + 40\%$). If an application encounters an expired listing or strict CAPTCHA, it automatically rolls to the next queued buffer job without consuming the user's daily quota.

---

## ⚔️ Comparison Matrix: JobEasyApply vs. Legacy Tools

| Feature | 🚀 JobEasyApply | Simplify Copilot | JobRight.ai | Sonara / LazyApply |
| :--- | :--- | :--- | :--- | :--- |
| **Execution Sandbox** | **Local Browser (Manifest v3)** | Local Browser | Cloud Redirect | Cloud Datacenter Bot |
| **LinkedIn Easy Apply** | ✅ **Full Autonomous Autopilot** | ⚠️ Semi-Auto (Manual click) | ⚠️ Manual review | ❌ Risky (Account bans) |
| **Greenhouse & Lever ATS** | ✅ **Automated Combobox + Questions** | ⚠️ Autofill only | ❌ Unsupported | ❌ Unsupported |
| **Account Ban Risk** | 🟢 **Zero (Residential IP & Cookies)** | 🟢 Low | 🟡 Medium | 🔴 High (Datacenter flags) |
| **Screening Intelligence**| 🧠 **Profile-Aware AI Engine** | ⚠️ Static Profile Strings | ⚠️ Basic Keyword | ❌ Static Templates |
| **Queue Fault Tolerance** | ⚡ **40% Extra Queue Buffer** | ❌ None | ❌ None | ❌ Wasted Quotas |
| **Free Forever Tier** | 🎁 **10 Jobs/Day (up to 15 with share)**| ⚠️ Autofill only | ⚠️ 5-10 Jobs/Day | ❌ Paid only ($49–$99/mo) |
| **Pricing** | **$5/week or $19/month** | Freemium | $19–$49/month | $49–$99/month |

---

## 🛠️ Free Developer Tools Built with NVIDIA AI ($0 Cost Stack)

As part of the platform, we maintain 8 free utilities running on edge LLMs (no login or credit card required):
* 🎯 [ATS Resume Score Checker](https://jobeasyapply.com/free-tools/ats-resume-checker)
* 🔍 [Resume Keyword Gap Scanner](https://jobeasyapply.com/free-tools/resume-keyword-scanner)
* 📝 [AI Cover Letter Generator](https://jobeasyapply.com/free-tools/cover-letter-generator)
* 💡 [LinkedIn Headline Optimizer](https://jobeasyapply.com/free-tools/linkedin-headline-optimizer)
* 🎯 [Interview Prep & Behavioral Generator](https://jobeasyapply.com/free-tools/interview-prep-generator)

---

## 🚀 Quickstart (30 Seconds)

1. Sign up at [jobeasyapply.com](https://jobeasyapply.com) (Free Forever, no credit card required).
2. Install the [JobEasyApply Chrome Extension](https://chromewebstore.google.com/detail/jobeasyapply-%E2%80%94-ai-job-app/aegajdjmemfodffcjbcbefkppjobeeoa).
3. Upload your resume (PDF) to parse your skills and experience.
4. Set your job titles, target platforms (**LinkedIn, Greenhouse, Lever**), and click **"Start Agent"**.

---

## 🛡️ Privacy, Security & Disclaimers

* **Zero Password Storage:** JobEasyApply never requests or stores your passwords.
* **Encrypted Profile Store:** User preferences and resume tokens are encrypted with AES-256.
* **Disclaimer:** JobEasyApply is an independent developer project and is not affiliated with, endorsed by, or sponsored by LinkedIn, Greenhouse Software, or Lever.co.

---

⭐ If this project saved you hours of form-filling, please star the repository on GitHub!
