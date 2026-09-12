# Social Lead Agent — Multi-Platform AI Marketing Agent

**Autonomous Multi-Platform AI Marketing & Lead Agent** for Instagram, Facebook, and TikTok.

Transforms a single content brief into platform-optimized posts, publishes via official APIs, captures & qualifies organic leads from comments and DMs, with UTM tracking, SQLite persistence, and rule-based + optional LLM intelligence.

---

## Overview

Phase 1 provides a production-grade content engine that maps a single high-level content brief into platform-tailored posts with automated UTM tracking links, handles scheduling/publishing via official API adapters and dry-run stubs, enforces platform-specific constraints (such as TikTok's 2,200 character API-safe limit), and provides robust persistence with SQLite and a modern CLI.

Phase 2 (core implemented) adds organic lead capture from comments, Private Replies, contact extraction, intent & region qualification (South Asia + Africa focus), and lead export.

---

## Key Features

- **Deterministic Rules & LLM Engine**: Works 100% offline out-of-the-box using pure platform rules, with optional LLM copy generation (Ollama, OpenAI, Gemini).
- **Platform Constraints**:
  - **TikTok**: Caption strictly locked at 2,200 characters (API-safe) with vertical (9:16) video recommendations.
  - **Instagram**: 2,200 characters max, 30 max hashtags, "Link in bio" CTA.
  - **Facebook**: 63,206 characters max, inline clickable UTM tracking link.
- **TikTok Asynchronous 3-Stage Flow**: Full implementation and simulation of `init` -> `transfer` -> `status` posting lifecycle.
- **Lead Capture Pipeline**: Comment listener (poller + webhook), Private Reply, contact extractor, rule-based qualifier, CSV exporter.
- **Repository Idempotency**: Built-in duplicate publish prevention.
- **Resilience**: Centralized exponential backoff with jitter for rate limits and API retries.
- **Zero-Ops Persistence**: SQLite database with foreign key integrity and SQLAlchemy ORM models.

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                    Agent CLI / Orchestrator                 │
│              (Typer CLI + Scheduler + Publisher)            │
└─────────────────────┬───────────────────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
┌──────────────┐ ┌──────────────┐ ┌──────────────┐
│ Content      │ │ Platform     │ │ Storage      │
│ Mapping      │ │ Adapters     │ │ Layer        │
│ Engine       │ │ (IG / FB /   │ │ (SQLite /    │
│ (AI + Rules) │ │  TikTok)     │ │  SQLAlchemy) │
└──────────────┘ └──────────────┘ └──────────────┘
        │             │             │
        └─────────────┼─────────────┘
                      ▼
               Config & Secrets
               (Pydantic + YAML)
```

Lead path: Engagement → Listeners → Private Reply → Extractor → Qualifier → Lead Store → Export

---

## Quickstart

### 1. Environment Setup

```bash
cd ai-marketing-agent
python -m venv .venv
source .venv/bin/activate   # or .venv\Scripts\activate on Windows
pip install -e ".[dev]"
cp .env.example .env
```

### 2. Initialize Database

```bash
python scripts/init_db.py
```

### 3. CLI Usage

```bash
# Create a content brief
python -m social_lead_agent.main create-brief \
  --title "Agentic AI Scholarship 2026" \
  --body "100% funded technical AI training program for developers in Pakistan, Nigeria, and Kenya." \
  --platforms instagram,facebook,tiktok \
  --url "https://example.com/apply" \
  --campaign "scholarship_2026" \
  --tone educational \
  --regions "Pakistan,Nigeria"

# Map into platform variants
python -m social_lead_agent.main map-content 1

# Dry-run publish
python -m social_lead_agent.main publish 1 --dry-run

# Check status
python -m social_lead_agent.main status 1
```

---

## Technology Stack

- Python ≥ 3.11, Pydantic v2, SQLAlchemy 2, Typer + Rich, httpx, structlog
- FastAPI + Uvicorn (webhooks)
- Meta Graph API + TikTok Content Posting API
- Optional: Ollama or any OpenAI-compatible LLM

---

## Project Status

- **Phase 1 (Content Engine)**: Completed
- **Phase 2 (Lead Capture & Qualification)**: Core modules implemented (listeners, private reply, extractor, qualifier, exporter)
- Future: Full CRM lite, multi-turn nurture, additional platforms, web dashboard

See `AI_Marketing_Agent_Project_Documentation.docx` and `PHASE2_IMPLEMENTATION.md` for full details.

---

**Maintainer**: Muhammad Zaheer ([zaheerxai](https://github.com/zaheerxai))
