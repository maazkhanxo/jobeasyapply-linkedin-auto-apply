# 🚀 JobEasyApply: AI-Powered Job Application Copilot & Career Suite

JobEasyApply is a production-ready, automated job application platform that helps job seekers find and apply to matching positions across LinkedIn, Indeed, and Naukri. It combines a local Manifest V3 Chrome Extension (to run applications safely from your own residential IP) with a FastAPI backend and a Celery worker pool.

Alongside the auto-applier, JobEasyApply features a suite of **8 Free AI-powered career tools** (ATS Resume Checker, Resume Tailor, Interview Prep Generator, and more) running at **$0/month in API costs** by leveraging **NVIDIA NIM** (Llama 3.3 70B & Nemotron 49B) and advanced caching.

🌐 **Try the Live Platform:** [jobeasyapply.com](https://jobeasyapply.com)  
🛠️ **Try the Free AI Tools:** [jobeasyapply.com/free-tools](https://jobeasyapply.com/free-tools)  
📖 **Read the Original Case Study:** [How I Built 8 AI Tools for $0/Month](https://jobeasyapply.com/blog/how-i-built-8-ai-tools-for-0-dollars-with-nvidia-nim)

---

## 🏗️ Technical Architecture & Case Study

### How We Run 8 AI Tools for $0/Month
Free LLM utilities are great for user acquisition, but high traffic can break a developer's budget. To run our suite of 8 optimization tools for free, we use **NVIDIA NIM** (NVIDIA Inference Microservices) developer APIs, backed by a dual-key failover client and strict atomic rate-limiting.

```mermaid
graph TD
    User([Job Seeker]) -->|Uploads Resume & JD| FE[Next.js Frontend]
    FE -->|API Request| BE[FastAPI Backend]
    BE -->|Slide Window Check| Redis{Redis Cache & ZSET}
    Redis -->|Allowed| Client[Dual-Key Failover Client]
    Redis -->|Rate Limited (5 req/hr)| Deny[HTTP 429 Error]
    Client -->|Primary Key / Llama 3.3 70B| NIM1[NVIDIA NIM Primary API]
    Client -.->|Failover Key / Nemotron 49B| NIM2[NVIDIA NIM Secondary API]
    NIM1 --> Success[Return Optimized Resume & Interview Prep]
    NIM2 --> Success
```

### 1. Dual-Key Failover Client (Python / FastAPI)
To ensure high availability and prevent rate-limit blockages, our FastAPI backend implements a dual-key + multi-model failover client:

```python
import logging
from openai import OpenAI

NVIDIA_BASE_URL = "https://integrate.api.nvidia.com/v1"
NVIDIA_MODELS = [
    "meta/llama-3.3-70b-instruct",
    "nvidia/llama-3.3-nemotron-super-49b-v1"
]

def call_nvidia(system_prompt: str, user_prompt: str, api_keys: list):
    """
    Call NVIDIA NIM with dual-key + multi-model failover.
    """
    for model in NVIDIA_MODELS:
        for key in api_keys:
            try:
                client = OpenAI(base_url=NVIDIA_BASE_URL, api_key=key)
                response = client.chat.completions.create(
                    model=model,
                    messages=[
                        {"role": "system", "content": system_prompt},
                        {"role": "user", "content": user_prompt}
                    ],
                    temperature=0.15,
                    max_tokens=2048
                )
                return response.choices[0].message.content
            except Exception as e:
                logging.error(f"NVIDIA Model {model} failed: {e}")
                continue
    return None
```

### 2. Atomic Sliding Window Rate Limiting (Redis + Lua)
To protect our developer API keys from scrapers and bots, we enforce a strict rolling rate limit of **5 requests per hour per IP address**. We use a Redis sorted set (`ZSET`) and run a Lua script atomically to prevent race conditions:

```lua
-- Redis Lua script for sliding window rate limiting
local key          = KEYS[1]
local window_start = tonumber(ARGV[1])
local now          = tonumber(ARGV[2])
local limit        = tonumber(ARGV[3])
local window       = tonumber(ARGV[4])

-- Remove requests older than the sliding window boundary
redis.call('ZREMRANGEBYSCORE', key, 0, window_start)

-- Check the current count of requests in the window
local count = redis.call('ZCARD', key)
if count >= limit then
    return 0 -- Deny request (Rate limit exceeded)
end

-- Record the new request timestamp
redis.call('ZADD', key, now, tostring(now))
redis.call('EXPIRE', key, window)
return 1 -- Allow request
```

---

## 💰 The Bootstrapped Developer Stack ($0/Month)

| Service | Role | Cost / Month |
| :--- | :--- | :--- |
| **NVIDIA NIM** | Llama 3.3 70B & Nemotron 49B Inference (Resume parser & ATS optimizer) | **$0.00** (Developer Quota) |
| **Vercel** | Static hosting & CDN for the Next.js Frontend | **$0.00** (Free Tier) |
| **Oracle Cloud** | Backend Host (FastAPI, Postgres database, Redis cache) | **$0.00** (Always Free Tier) |
| **GitHub Actions**| CI/CD Pipeline | **$0.00** (Free Plan) |
| **Total** | **Running a global AI career platform serving 50k+ pageviews/mo** | **$0.00** |

---

## 🛠️ Getting Started (Local Development)

### Prerequisites
- Python 3.11+
- Node.js 18+
- Docker & Docker Compose

### Step 1: Start Infrastructure
Run the local database, cache, and object storage:
```bash
docker compose up -d
```

### Step 2: Spin Up the FastAPI Backend
```bash
cd apps/backend
pip install -r requirements.txt
alembic upgrade head
uvicorn main:app --reload --port 8000
```
*API documentation will be available at [http://localhost:8000/docs](http://localhost:8000/docs).*

### Step 3: Run the Next.js Frontend
```bash
cd apps/frontend
npm install
npm run dev
```
*The client app runs locally at [http://localhost:3000](http://localhost:3000).*

### Step 4: Run Celery Workers
Start the background worker tasks for job application queue management:
```bash
celery -A workers.celery_app worker --loglevel=info
```

---

## 📁 Repository Structure

```text
autoJobsApply/
├── apps/
│   ├── backend/          # FastAPI (Python 3.11)
│   └── frontend/         # Next.js 16 (TypeScript / CSS Modules)
├── workers/              # Celery task runners for auto-applying
├── migrations/           # Alembic database migrations
├── docker/               # Docker configuration files
└── docker-compose.yml    # Development stack orchestrator
```

## 🔒 Security & Safe Automation
Unlike spammy mass-appliers that risk your account status, JobEasyApply is designed with safety first:
* **Local Browser Injection:** Submissions occur directly in your own browser instance, matching your standard residential IP and active session cookies.
* **Human-like Pacing:** Features randomized delays between fields and steps to match standard human interaction patterns.
* **No Database Storage of Credentials:** Your passwords and LinkedIn access credentials never touch cloud databases.

---

## 📄 License
This project is open-source. For commercial usage or custom integrations, please check the [JobEasyApply Website](https://jobeasyapply.com).
