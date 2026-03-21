---
name: Research Workflow System
description: Setup of research assistant workflow at D:\research\ with paper management web app (updated 2026-03-21)
type: project
---

Research workspace at D:\research\, git remote: git@github.com:hkban99668-code/research-workspace.git

**Web app** (`D:\research\webapp\`, Flask, port 5001):
- Start: `cd /d/research/webapp && venv/bin/python app.py`
- DB: `papers.db` (SQLite), .gitignored
- Config: `user_config.json` (API keys etc.), .gitignored
- 重启服务：用 PowerShell `Stop-Process -Id <PID> -Force`，改模板后必须重启，改 CSS/JS 只需刷新浏览器

**Architecture as of 2026-03-21:**
- `llm_client.py` — unified LLM abstraction，支持 `base_url` 参数（用于国际版 DashScope）
- `analyzer.py` — normal analysis via Qwen，传 `base_url=config.get("normal_base_url")`
- `explorer.py` — multi-turn advanced exploration via Claude (advanced_api_key + advanced_model)
- `keyword_extractor.py` — structured keyword extraction via Qwen，提取后持久化到 papers.keywords 列
- `trending.py` — 基于已读/已分析论文生成趋势总结（LLM），30分钟缓存，替代原静态关键词列表
- `translator.py` — Microsoft Edge free token translation (no API key, works in CN)
- `downloader.py` — PDF download with dynamic S2 API lookup for papers without pdf_url
- `scheduler.py` — daily fetch scheduler
- `database.py` — tables: papers, analyses, sessions, session_messages, fetch_logs

**Config keys:**
- normal_api_key: Qwen/DashScope 国际版 key
- normal_model: qwen3.5-flash
- normal_base_url: https://dashscope-intl.aliyuncs.com/compatible-mode/v1/chat/completions（国际版必填）
- advanced_api_key: Anthropic API key
- advanced_model: claude-sonnet-4-6

**前端功能（2026-03-21新增）：**
- 论文卡片标题下方自动显示关键词标签（后台逐篇提取，每次最多5篇，500ms间隔）
- 搜索框下方有关键词热点集群（top6，点击过滤论文）
- 热度排行页改为"我的阅读趋势"——基于已读/已分析论文的 AI 趋势总结

**Why:** User wants a self-hosted research paper tracking and AI analysis system that grows with their research.
**How to apply:** When user mentions paper webapp issues, check these modules. 翻译/热度排行/关键词提取均需 normal_api_key 配置。Flask 必须以 threaded=True 启动，否则批量关键词提取会阻塞翻译和热度排行请求。
