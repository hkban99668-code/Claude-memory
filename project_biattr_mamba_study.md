---
name: BiAttr-Mamba 研究学习进度
description: 用户正在学习的研究课题 BiAttr-Mamba，当前进度和已掌握概念
type: project
---

研究课题：BiAttr-Mamba（基于双向自监督 Mamba 的反事实气候变化归因框架）
规划报告文件：C:\Users\ME\OneDrive\xwechat_files\wxid_d3k8zobnac8o12_018e\msg\file\2026-03\research_report.html
目标投稿：ICLR 2027（截稿 2026年10月）

**Why:** 用户从零开始学习该方向，需要系统性地建立领域知识。

**How to apply:** 每次继续学习时从上次进度接续，不要重复已讲过的内容。

## 推荐阅读顺序（6篇）

| 顺序 | 论文 | arXiv | 状态 |
|------|------|-------|------|
| 1 | AI for Extreme Events 综述 | 2406.20080 | 已完成 |
| 2 | RS Foundation Models 综述 | 2603.00988 | 未读 |
| 3 | Mamba | 2312.00752 | 未读 |
| 4 | I-JEPA | 2301.08243 | 未读 |
| 5 | GraphCast | 2212.12794 | 未读 |
| 6 | ChangeMamba | 2404.03425 | 未读 |

## 已掌握概念（2026-03-30）

**论文1（2406.20080）核心内容：**
- Attribution（归因）：把观测变化拆分为"人为"和"自然"两部分，实现方式：最优指纹法（传统线性回归）→ CNN归因 → BiAttr-Mamba 潜空间做差
- Counterfactual（反事实）：用 CMIP6-HistNat 构造"没有人类活动的平行地球"，归因信号 = f_obs - f_nat
- UQ（不确定性量化）：偶然不确定性 vs 认知不确定性，实现：MC-Dropout / Deep Ensemble / Gaussian Head
- XAI（可解释性）：SHAP、Grad-CAM、Integrated Gradients

**CMIP6-HistNat 理解：**
- CMIP6 是全球气候模型联合项目，HistNat 实验只含自然强迫（无人为排放）
- 分辨率约 100km，仅作训练时的监督信号，不参与推理
- BiAttr-Mamba 输入是高分辨率卫星数据（30m），CMIP6 是"方向指引"不是被超分的数据

**Mamba vs Transformer 理解：**
- Transformer：O(N²)，存所有位置两两关系，序列长了显存爆炸
- Mamba：O(N)，滚动隐状态 h，选择性门控决定记什么忘什么
- 用户已理解：Mamba 省在"存状态"而非"存关系"，选择性保存是核心创新
