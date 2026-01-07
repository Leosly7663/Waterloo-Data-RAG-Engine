# Semantic Search & Matching API
**FastAPI · SentenceTransformers · Supabase · Docker · Cloudflare Tunnel**

A production-ready **embedding, matching, and retrieval engine** built with **FastAPI**, designed for **RAG pipelines, resume–job matching, and geo/time-aware semantic search**.
The service auto-manages vector embeddings in **Supabase**, supports **hybrid (semantic + lexical) search**, and can be securely exposed to the internet using **Cloudflare Tunnel**.

---

## ✨ Features

- 🔎 **Semantic & Hybrid Search**
  - Vector similarity via SentenceTransformers
  - Optional hybrid semantic + lexical ranking (Supabase RPC)
  - Dataset + kind filtering
  - Geo-radius search with distance boosting
  - Optional time windows

- 🧠 **Model Management**
  - Lazy-loaded, thread-safe SentenceTransformer registry
  - Hot-swap models at runtime
  - Per-request model overrides

- 🔁 **Auto-Embedding Worker**
  - Background daemon refreshes stale or missing embeddings
  - Detects changes via text hash tracking
  - Batch upserts to Supabase

- 🧾 **Text Matching**
  - Resume → job sentence similarity scoring
  - Sentence combination for better semantic coverage
  - Top-K match support

- 🚀 **Production Deployment**
  - Dockerized API
  - Secure public access via Cloudflare Tunnel
  - Environment-driven configuration

---

## 🧱 Architecture Overview

```
Client
  |
  v
FastAPI API
  ├── /combine-match      (resume/job similarity)
  ├── /search             (semantic or hybrid RAG search)
  ├── /embed/*            (embedding admin)
  └── /model              (model control)
  |
  v
SentenceTransformers
  |
  v
Supabase (Postgres + pgvector)
  ├── Embeddings table
  └── RPC search functions
```

---

## 🚀 Quick Start

### 1) Prerequisites
- Docker Desktop
- Cloudflare account
- Domain added to Cloudflare (nameservers updated)
- `cloudflared` installed

```powershell
winget install --id Cloudflare.cloudflared
```

---

### 2) Configure Environment

Create `.env.local`:
```env
SUPABASE_URL=your_url
SUPABASE_KEY=your_key
EMBED_MODEL=sentence-transformers/all-MiniLM-L6-v2
EMBED_TABLE=rag_docs
```

---

### 3) Build & Run Locally

```powershell
docker-compose up -d
```

API available at:
```
http://localhost:8000
```

Health check:
```
GET /health
```

---

## 🌐 Cloudflare Tunnel (Secure Public Access)

### Authenticate
```powershell
cloudflared tunnel login
```

### Create or reuse tunnel
```powershell
cloudflared tunnel create semantic-api
```

or
```powershell
cloudflared tunnel token semantic-api
```

### Create config file

**Path**
```
C:\Users\YourUsername\.cloudflared\config.yml
```

**config.yml**
```yaml
tunnel: semantic-api
credentials-file: C:\Users\YourUsername\.cloudflared\<UUID>.json

ingress:
  - hostname: api.yourdomain.com
    service: http://localhost:8000
  - service: http_status:404
```

### Create DNS route
```powershell
cloudflared tunnel route dns semantic-api api.yourdomain.com
```

### Run tunnel
```powershell
cloudflared tunnel run semantic-api
```

---

## 📂 Project Structure

```
.
├── app.py
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── test.py
├── test_search.py
└── .env.local
```

---

## ⚠️ Notes

- Cloudflare Tunnel avoids exposing ports publicly
- Use `.env.local` for secrets (never commit)
- Supabase RPC functions must exist for hybrid search
- Designed for moderate-scale RAG workloads

---

## 📄 License

MIT License