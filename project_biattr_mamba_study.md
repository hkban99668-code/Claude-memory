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
| 2 | RS Foundation Models 综述 | 2603.00988 | 已完成 |
| 3 | Mamba | 2312.00752 | 已完成 |
| 4 | I-JEPA | 2301.08243 | 已完成 |
| 5 | GraphCast | 2212.12794 | 未读 |
| 6 | ChangeMamba | 2404.03425 | 未读 |

阅读笔记文件：D:\research\paper_reading_notes.md

## 已掌握概念（2026-03-30）

**论文1（2406.20080）：**
- f_obs - f_nat：归因信号 = 观测潜向量 - 反事实潜向量，在潜空间做差而非像素空间
- Counterfactual：CMIP6-HistNat 构造只含自然强迫的"平行地球"
- UQ：偶然/认知不确定性，MC-Dropout / Deep Ensemble / Gaussian Head
- XAI：SHAP、Grad-CAM、Integrated Gradients

**论文2（2603.00988）：**
- RS基础模型演化路线：单模态光学 → SAR → 多模态融合 → 时序 → VLM
- DOFA 异构 tokenizer：动态权重生成器，根据传感器元信息生成投影矩阵
- 预训练策略对比：对比学习 / MAE（学像素）/ I-JEPA（学语义），归因任务选 I-JEPA
- 关键数据集：SSL4EO-S12、MMEarth、Prithvi 预训练数据

**论文4（2301.08243）I-JEPA：**
- 三者分工：Target Encoder 出题（EMA慢更新）/ Context Encoder 答题（真正要用的）/ Predictor 翻译（训后丢弃）
- 遮 Target Encoder 输出而非输入：保证学习目标 g 在完整上下文下生成，稳定可靠
- EMA 防坍缩：momentum=0.996，为训练提供稳定但不断进步的学习目标
- BiAttr-Mamba 双支路坍缩风险：obs/nat 共享 encoder 时需 EMA 机制防止 f_obs≈f_nat

**论文3（2312.00752）Mamba：**
- SSM：滚动隐状态 h，h_t = Ā·h_{t-1} + B̄·x_t，O(N) 线性复杂度
- Δ（Delta）：控制写入/遗忘，大Δ=记住，小Δ=忽略，是选择性的核心
- S6：让 B、C、Δ 变成输入 x 的函数，实现内容相关的选择性记忆
- 硬件感知：Kernel Fusion，在 SRAM 内完成递推，比 Transformer 快 5 倍
- BiAttr-Mamba 关联：Δ 的空间分布图可作为归因显著性地图（XAI落点）
