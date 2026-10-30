# ⚡ JobEasyApply — Local-First AI Job Application Agent for LinkedIn, Greenhouse, Lever & Ashby

<p align="center">
  <a href="https://chromewebstore.google.com/detail/jobeasyapply-%E2%80%94-ai-job-app/aegajdjmemfodffcjbcbefkppjobeeoa">
    <img src="https://img.shields.io/badge/Google_Featured-5.0_%E2%98%85_Rating-22c55e.svg?style=for-the-badge&logo=googlechrome&logoColor=white" alt="Google Featured Extension" />
  </a>
  <a href="https://chromewebstore.google.com/detail/jobeasyapply-%E2%80%94-ai-job-app/aegajdjmemfodffcjbcbefkppjobeeoa">
    <img src="https://img.shields.io/badge/Chrome_Web_Store-v4.0.7_Autopilot-blue.svg?style=for-the-badge&logo=googlechrome&logoColor=white" alt="Chrome Web Store v4.0.7" />
  </a>
  <a href="https://jobeasyapply.com">
    <img src="https://img.shields.io/badge/Production-Live_at_jobeasyapply.com-6366f1.svg?style=for-the-badge&logo=vercel&logoColor=white" alt="Website" />
  </a>
  <a href="LICENSE">
    <img src="https://img.shields.io/badge/License-MIT-gray.svg?style=for-the-badge" alt="License: MIT" />
  </a>
</p>

<p align="center">
  <strong>The autonomous, residential AI job application copilot.</strong><br />
  Applies to matched roles across LinkedIn Easy Apply, Greenhouse, Lever, and Ashby on 100% hands-free autopilot — executing locally from your browser with zero account ban risk.
</p>

<p align="center">
  <a href="https://jobeasyapply.com">🌐 Official Website</a> •
  <a href="https://chromewebstore.google.com/detail/jobeasyapply-%E2%80%94-ai-job-app/aegajdjmemfodffcjbcbefkppjobeeoa">📦 Install Chrome Extension</a> •
  <a href="https://jobeasyapply.com/free-tools">🛠️ 8 Free Developer Tools</a> •
  <a href="https://jobeasyapply.com/affiliates">💵 30% Affiliate Program</a>
</p>

---

## 💡 Why JobEasyApply?

Most job application bots run on remote cloud servers (AWS, DigitalOcean, Hetzner). Hiring portals like LinkedIn, Greenhouse, and Lever easily detect these datacenter IP addresses, resulting in automated candidate shadowbans or account restrictions.

**JobEasyApply is architected differently.** It is built around a **Split-Plane Architecture**:
* **100% Residential Local Execution:** Runs directly inside your local Chrome browser (Manifest v3) using your authentic IP address, logged-in session cookies, and viewport fingerprint.
* **1-Click Full Autopilot:** Automatically matches jobs, auto-selects the optimal resume, synthesizes recruiter screening answers in <400ms, and submits forms without freezing or manual pauses.
* **0% Ban Risk:** Form mutations emulate human typing cadence, randomized micro-delays (120ms–340ms), and prototype event dispatchers.
* **Zero Credential Exposure:** We never ask for, view, or store your LinkedIn or ATS passwords.

---

## 🏗️ System Architecture & Engineering

## 🏗️ Architecture & Engineering Design

                         ┌──────────────────────────────────────────────────────────────┐
             │                 User's Chrome Browser (Local)                │
             │                                                              │
             │   ┌────────────────────┐            ┌────────────────────┐   │
             │   │ LinkedIn EasyApply │            │  Greenhouse/Lever  │   │
             │   │     Active Tab     │            │     Active Tab     │   │
             │   └─────────┬──────────┘            └─────────┬──────────┘   │
             │             ▲                                 ▲              │
             │             │ Synthetic Prototype Events      │              │
             │             │ (Pointer / Input / Change)      │              │
             │   ┌─────────┴─────────────────────────────────┴──────────┐   │
             │   │          JobEasyApply Engine (Manifest v3)            │   │
             │   │   • Full Autopilot Form Traversal                     │   │
             │   │   • Smart-Resume Selector & Dropdown Matcher          │   │
             │   │   • Web Audio Anti-Throttling Heartbeat               │   │
             │   │   • Floating HUD with Manual Pause/Resume             │   │
             │   └──────────────────────────┬────────────────────────────┘   │
             └──────────────────────────────┼───────────────────────────────┘
                                            │
                            HTTPS + TLS 1.3 │ Queued Jobs & Embeddings
                                            ▼
             ┌──────────────────────────────────────────────────────────────┐
             │             Cloud Intelligence Plane (FastAPI VPS)           │
             │                                                              │
             │   ┌────────────────────┐            ┌────────────────────┐   │
             │   │ Groq LPU Inference │            │ PostgreSQL 16      │   │
             │   │ (<400ms Screening) │            │ + asyncpg Pool     │   │
             │   ├────────────────────┤            ├────────────────────┤   │
             │   │ 6D Match Algorithm │            │ Redis 7 LRU Cache  │   │
             │   │ + 40% Buffer Queue │            │ + Celery Workers   │   │
             │   └────────────────────┘            └────────────────────┘   │
             └──────────────────────────────────────────────────────────────┘


### 1. Zero-Cloud-Credential Execution (Anti-Ban)
* **No Session Hijacking:** The backend never receives or proxies your session cookies or credentials.
* **Authentic Fingerprinting:** Every HTTP request and DOM event originates from your real residential IP, active cookies, TLS fingerprint, and screen viewport.
* **Synthetic Event Pipeline:** Emulates native human keystrokes with randomized micro-delays (120ms–340ms) using `Object.getOwnPropertyDescriptor(HTMLInputElement.prototype, 'value').set` to satisfy React 19 and Vue controlled input state lifecycles.

### 2. Solving Greenhouse, Lever & Ashby Combobox Traps
Modern ATS portals render custom virtualized comboboxes with dynamic ARIA listboxes instead of native `<select>` dropdowns. JobEasyApply solves this with a multi-tier event dispatcher:
1. Simulates pointer interaction on the combobox trigger.
2. Injects character inputs matching target country/residence values.
3. Listens for rendered option items and triggers native click and change events with custom blur handlers.

### 3. Sub-400ms Neural Screening Intelligence
Recruiters gate candidates behind complex screening questions (years of experience with specific frameworks, salary expectations, US work authorization, visa sponsorship, and situational architecture questions). JobEasyApply queries Groq LPUs and Gemini in real-time to generate contextually accurate, recruiter-grade answers tailored to your exact resume in <400ms.

### 4. 6-Dimensional Algorithmic Job Matching
Before applying, the engine evaluates openings against six mathematical vectors:
* **Target Title Hierarchy**
* **Location & Remote Taxonomy**
* **Compensation Thresholds**
* **Technical Stack Density**
* **Company Growth Stage**
* **Experience Bounds**

---

## ⚔️ Comparison Matrix: JobEasyApply vs. Legacy Bots

| Feature | 🚀 JobEasyApply | Simplify Copilot | Jobright.ai | LazyApply / LoopCV |
| :--- | :--- | :--- | :--- | :--- |
| **Execution Sandbox** | **Local Browser (Manifest v3)** | Local Browser | Cloud Redirect | Cloud Datacenter Bot |
| **LinkedIn Easy Apply** | ✅ **100% Full Autopilot** | ⚠️ Semi-Auto (Manual click) | ⚠️ Manual Review | ❌ High Ban Risk (Server IPs) |
| **Greenhouse, Lever & Ashby** | ✅ **Automated Combobox + AI** | ⚠️ Autofill only | ❌ Unsupported | ❌ Unsupported |
| **Account Ban Risk** | 🟢 **Zero (Residential IP)** | 🟢 Zero | 🟡 Medium | 🔴 High (Datacenter flags) |
| **Screening Intelligence** | ⚡ **<400ms Groq LPU AI** | ⚠️ Static Strings | ⚠️ Basic Keywords | ❌ Canned Templates |
| **Smart-Resume Routing** | ✅ **Automated 6D Matching** | ❌ Manual Selector | ❌ None | ❌ None |
| **Queue Fault Tolerance** | ⚡ **40% Buffer Queue** | ❌ None | ❌ None | ❌ Wasted Quotas |
| **Free Forever Tier** | 🎁 **10 AI Apps / Day (300/mo)** | ⚠️ Autofill only | ⚠️ 5-10 Apps / Day | ❌ Paid Only ($99–$149) |

---

## 🛠️ 8 Free AI Career Tools (Zero Cost, No Login Required)

As part of the platform, we maintain 8 free developer utilities running on edge LLMs:
* 🎯 [ATS Resume Score Checker](https://jobeasyapply.com/free-tools/ats-resume-checker) — Instant ATS compatibility scoring and parsing check.
* 🔍 [Resume Keyword Gap Scanner](https://jobeasyapply.com/free-tools/resume-keyword-scanner) — Identifies missing keywords from job postings.
* 📝 [AI Cover Letter Generator](https://jobeasyapply.com/free-tools/cover-letter-generator) — Role-specific, tailored cover letters in seconds.
* 💡 [LinkedIn Headline Optimizer](https://jobeasyapply.com/free-tools/linkedin-headline-optimizer) — High-converting recruiter-magnet headlines.
* 🎯 [Interview Prep & Behavioral Generator](https://jobeasyapply.com/free-tools/interview-prep-generator) — Custom behavioral and technical interview questions.
* ⚡ [Resume Bullet Optimizer](https://jobeasyapply.com/free-tools/resume-bullet-optimizer) — Rewrites bullet points into quantified impact metrics.
* 📄 [Resume Summary Generator](https://jobeasyapply.com/free-tools/resume-summary-generator) — Executive, punchy career summaries.
* ✂️ [Targeted Resume Tailor](https://jobeasyapply.com/free-tools/resume-tailor) — Re-aligns your resume to any specific job description.

---

## 🚀 Quickstart Guide (3 Minutes)

1. Create your account at [jobeasyapply.com](https://jobeasyapply.com) (Free Forever, zero credit card required).
2. Install the [JobEasyApply Chrome Extension](https://chromewebstore.google.com/detail/jobeasyapply-%E2%80%94-ai-job-app/aegajdjmemfodffcjbcbefkppjobeeoa).
3. Upload your resume (PDF) to extract your experience, skills, and background.
4. Set your job titles, target locations (Remote/Hybrid/On-site), and salary expectations.
5. Open LinkedIn Jobs or any Greenhouse/Lever/Ashby board and click **"Start Auto Apply"**.
6. Track submissions in real time from your centralized telemetry dashboard at [jobeasyapply.com/dashboard](https://jobeasyapply.com/dashboard).

---

## 🛡️ Security, Privacy & Enterprise Compliance

* **Zero Password Storage:** JobEasyApply never requests, intercepts, or stores your passwords.
* **Encryption Standards:** All profile data and communication are encrypted with TLS 1.3 in transit and AES-256 at rest.
* **Strict Privacy Policy:** We never sell, broker, or monetize candidate data.
* **Data Sovereignty:** Full GDPR and CCPA compliance with 1-click permanent account deletion.
* **Disclaimer:** JobEasyApply is an independent open-source project and is not affiliated with, endorsed by, or sponsored by LinkedIn, Greenhouse Software, Lever, or Ashby.

---

⭐ **Star this repository** if JobEasyApply saved you hundreds of hours of repetitive job applications!
