---
name: 开发经验教训
description: 2026-03-19 开发论文检索系统时总结的避坑经验
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

**4. 翻译/下载等 API 选型前查清字符限制和国内访问速度**
**Why:** MyMemory 500 字符限制 + 速度慢，学术摘要根本不适用，用户反馈"特别卡"后才换方案。
**How to apply:** 学术场景翻译优先用 Google Translate 非官方接口（无字符限制，国内可访问）。

**5. 数据库迁移函数在建表语句之后调用**
**Why:** 放在 executescript 之前会对不存在的表执行 PRAGMA，逻辑错误。
**How to apply:** 顺序固定为：建表 → 迁移补列。
