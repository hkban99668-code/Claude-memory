---
name: Research Workflow System
description: Setup of research assistant workflow at D:\research\ with paper management web app (fully functional as of 2026-03-20)
type: project
---

Research workspace at D:\research\, git remote: git@github.com:hkban99668-code/research-workspace.git

**Web app** (`D:\research\webapp\`, Flask, port 5001):
- Start: `cd /d/research/webapp && venv/bin/python app.py`
- DB: `papers.db` (SQLite), .gitignored
- Config: `user_config.json` (API keys etc.), .gitignored

**Architecture as of 2026-03-20:**
- `llm_client.py` — unified LLM abstraction, auto-detects Anthropic/Qwen/OpenAI from model name
- `analyzer.py` — normal analysis via Qwen (normal_api_key + normal_model)
- `explorer.py` — multi-turn advanced exploration via Claude (advanced_api_key + advanced_model), saves markdown to `papers/notes/advanced/`
- `keyword_extractor.py` — structured keyword extraction via Qwen, returns domain/tasks/methods/datasets/venues
- `trending.py` — static 30-keyword ML hotspot ranking (4 tiers) + on-demand AI enrichment
- `translator.py` — Microsoft Edge free token translation (no API key, works in CN)
- `downloader.py` — PDF download with dynamic S2 API lookup for papers without pdf_url
- `scheduler.py` — daily fetch scheduler
- `database.py` — tables: papers, analyses, sessions, session_messages, fetch_logs

**Config keys:** normal_api_key (Qwen/DashScope), normal_model, advanced_api_key (Anthropic), advanced_model

**Why:** User wants a self-hosted research paper tracking and AI analysis system that grows with their research.
**How to apply:** When user mentions paper webapp issues, check these modules. Keyword extraction requires normal_api_key to be configured in settings.
