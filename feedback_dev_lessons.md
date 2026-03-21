---
name: 开发经验教训
description: 论文检索系统开发过程中积累的避坑经验（2026-03-19 ~ 2026-03-21）
type: feedback
---

**1. Python 包安装前先确认版本兼容性**
有 Rust/C 扩展的包（如 anthropic SDK 依赖的 jiter）在新版 Python 可能没有预编译 wheel。
**Why:** Python 3.14 上 anthropic==0.34.0 无法编译，浪费大量时间。
**How to apply:** 遇到含原生扩展的包，优先用 requests 直接调 HTTP API 替代 SDK；或先确认目标 Python 版本有对应 wheel。

**2. 使用第三方 API 前先用 curl 验证可用性和响应结构**
**Why:** PWC API 已并入 HF，返回 HTML；HF API 响应结构与猜测不同，都是写完代码才发现。
**How to apply:** 动手前先 curl 一个真实端点，打印字段确认结构，再写解析代码。

**3. Windows 环境进程管理：用 PowerShell Stop-Process，不用 pkill/taskkill**
**Why:** pkill 在 Windows 无效；taskkill 在 bash 里因路径问题经常失败；PowerShell Stop-Process 最可靠。
**How to apply:** 重启服务用 `powershell -Command "Stop-Process -Id <PID> -Force"`，再用 `netstat -ano | grep ":端口"` 确认端口释放后才启动新进程。

**4. 翻译 API 选型：MS Edge 免费 Token 最稳**
**Why:** Google Translate 在国内返回 302 重定向（不可用）；MyMemory 500 字符限制且慢；LLM 翻译费钱。
**How to apply:** 学术场景翻译用 Microsoft Edge 免费 Token（GET edge.microsoft.com/translate/auth → POST MS Translate API），无字符限制，国内可访问，无需 API Key，Token 缓存 9 分钟。

**5. 数据库迁移函数在建表语句之后调用**
**Why:** 放在 executescript 之前会对不存在的表执行 PRAGMA，逻辑错误。
**How to apply:** 顺序固定为：建表 → 迁移补列。

**6. S2 论文无 PDF URL：运行时动态查找而非存储时**
**Why:** Semantic Scholar 论文入库时没有 pdf_url，存 None；下载时才用 S2 API 查 openAccessPdf 或 ArXiv ID，并缓存回 DB。
**How to apply:** 下载模块对 pdf_url 为空的论文自动走 S2 API 查询，加 sleep(1) 避免 429。

**7. Windows bash 环境 venv 路径用 bin 不用 Scripts**
**Why:** 2026-03-21 启动 webapp 时先用了 `venv/Scripts/python`（Windows CMD 路径），在 bash 环境下报 exit code 127。
**How to apply:** 在 bash/Git Bash 环境下，venv 激活路径统一用 `venv/bin/python`。

**8. Flask 开发服务器必须加 threaded=True，后台批量 API 调用必须加 try/catch + 并发限制**
**Why:** Flask dev server 默认单线程，后台 autoExtractKeywords 批量调 Qwen 时会占满请求队列，导致用户的翻译/热度排行请求排队等待超时，表现为功能"用不了"。
**How to apply:** `app.run()` 必须加 `threaded=True`；所有后台批量 async 函数必须：① 用 try/finally 释放锁，② 每次最多处理5篇，③ 延迟≥500ms，防止雪崩。

**9. CSS 修改后 Flask 模板不自动更新，必须重启服务**
**Why:** Flask dev server 对 templates/ 变更需要重新加载进程才能生效（static/ 文件则浏览器刷新即可）。
**How to apply:** 修改 HTML 模板后必须重启服务；修改 CSS/JS 只需刷新浏览器。

**10. 全局 CSS .hidden 规则缺失导致双视图同时显示**
**Why:** view-papers 和 view-trending 用 JS 的 classList.toggle("hidden") 切换，但 CSS 里没有全局 .hidden { display: none } 规则，导致两个视图同时显示，页面只有一半。
**How to apply:** 使用 hidden class 切换显示时，必须在 CSS 里有对应规则。统一加 `.hidden { display: none !important; }` 到全局 CSS。
