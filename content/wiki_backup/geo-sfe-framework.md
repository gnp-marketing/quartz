# GEO-SFE 框架概述

**Summary**: 結構特徵工程用於生成式引擎優化（GEO-SFE）是一套系統化方法，通過優化內容結構（獨立於語義內容）來提升 LLM 生成式引擎的引用率。

**Sources**: Structural Feature Engineering for Generative Engine Optimization - How Content Structure Shapes Citation Behavior.pdf

**Last updated**: 2026-04-15

---

## 研究背景與動機

傳統搜尋引擎優化（SEO）的時代已逝。LLM 驅動的生成式引擎（如 ChatGPT、Google SGE、Perplexity.ai）根本改變了資訊發現方式：

- **舊模式**：用戶點擊排名結果（點擊率經濟）
- **新模式**：LLM 合成多個來源的資訊，生成直接答案並選擇性地引用源文件（引用率經濟）

**業界數據**（source: Structural Feature Engineering for Generative Engine Optimization.pdf）：
- 有機點擊率從 28% 下降到 19%（針對排名第一的結果）
- 零點擊搜尋現已佔所有查詢的 58% 以上

## 現有研究的缺口

現存的 GEO 研究（如 Aggarwal et al. 2024）專注於**語義內容修改**：
- 添加統計數據
- 引入權威引用
- 使用更權威的語言

這些方法能帶來最多 40% 的引用率改進，但**內容結構對引用行為的系統性影響仍未被探索**。

LLM 系統研究表明，內容結構（獨立於語義）顯著影響 LLM 的處理方式，但現有 GEO 工作將生成式引擎視為黑盒子，缺乏結構優化的科學指導。

## GEO-SFE 的核心創新

GEO-SFE 框架引入了第一套**系統化的結構優化方法**，將內容結構分解為三個層級：

### 三層結構分解

| 層級 | 定義 | 優化目標 |
|------|------|--------|
| **Macro-structure** | 文檔整體架構 | 標題層級、導航、段落流程 |
| **Meso-structure** | 段落級組織 | 段落長度、列表/表格比例、資訊密度 |
| **Micro-structure** | 句子級特徵 | 加粗/斜體、關鍵詞位置、句式結構 |

## 實驗成果

在 6 個主流生成式引擎上的評估顯示：
- **引用率提升**：17.3%（p < 0.001）
- **感知品質提升**：18.5%
- **層級貢獻度**：宏觀 45% + 中觀 40% + 微觀 15%

## 相關概念

- [[macro-structure-optimization]]
- [[meso-structure-optimization]]
- [[micro-structure-optimization]]
- [[generative-engine-architecture]]
- [[citation-performance-metrics]]
