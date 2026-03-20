---
name: 开发经验教训
description: 论文检索系统开发过程中积累的避坑经验（2026-03-19 ~ 2026-03-20）
type: feedback
---

**1. Python 包安装前先确认版本兼容性**
有 Rust/C 扩展的包（如 anthropic SDK 依赖的 jiter）在新版 Python 可能没有预编译 wheel。
**Why:** Python 3.14 上 anthropic==0.34.0 无法编译，浪费大量时间。
**How to apply:** 遇到含原生扩展的包，优先用 requests 直接调 HTTP API 替代 SDK；或先确认目标 Python 版本有对应 wheel。

**2. 使用第三方 API 前先用 curl 验证可用性和响应结构**
**Why:** PWC API 已并入 HF，返回 HTML；HF API 响应结构与猜测不同，都是写完代码才发现。
**How to apply:** 动手前先 curl 一个真实端点，打印字段确认结构，再写解析代码。

**3. Windows 环境进程管理用 taskkill，不用 pkill**
**Why:** pkill 在 Windows 无效，导致旧进程堆积（9 个进程同时监听同一端口），新代码路由全部 404。
**How to apply:** 重启服务前用 `taskkill /F /IM python.exe /T`，再用 `netstat -ano | grep ":端口"` 确认端口释放后才启动新进程。

**4. 翻译 API 选型：MS Edge 免费 Token 最稳**
**Why:** Google Translate 在国内返回 302 重定向（不可用）；MyMemory 500 字符限制且慢；LLM 翻译费钱。
**How to apply:** 学术场景翻译用 Microsoft Edge 免费 Token（GET edge.microsoft.com/translate/auth → POST MS Translate API），无字符限制，国内可访问，无需 API Key，Token 缓存 9 分钟。

**5. 数据库迁移函数在建表语句之后调用**
**Why:** 放在 executescript 之前会对不存在的表执行 PRAGMA，逻辑错误。
**How to apply:** 顺序固定为：建表 → 迁移补列。

**6. S2 论文无 PDF URL：运行时动态查找而非存储时**
**Why:** Semantic Scholar 论文入库时没有 pdf_url，存 None；下载时才用 S2 API 查 openAccessPdf 或 ArXiv ID，并缓存回 DB。
**How to apply:** 下载模块对 pdf_url 为空的论文自动走 S2 API 查询，加 sleep(1) 避免 429。
