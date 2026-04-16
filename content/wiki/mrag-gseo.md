# 多模態 RAG 與 G-SEO 挑戰

**Summary**: 多模態 RAG（MRAG）擴展了生成式搜尋引擎同時處理文字與圖片的能力，也帶來了純文字 G-SEO 方法無法應對的新挑戰。本頁介紹 MRAG 架構如何改變 G-SEO 問題的本質，以及 MRAMG benchmark 的評估框架。

**Sources**:
- Caption Injection for Optimization in Generative Search Engine.pdf（Xiaoluchen et al.；arXiv:2511.04080v2，2026-03-18）

**Last updated**: 2026-04-16

---

## 什麼是 MRAG？

**Multimodal Retrieval-Augmented Generation（MRAG）** 是 RAG 技術的多模態延伸。傳統 RAG 只檢索文字片段，MRAG 則同時檢索並整合文字與圖片，讓 GSE 的 LLM 在生成回應時能「看到」視覺資訊（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

MRAG 的技術路線主要有三類：

| 路線 | 做法 | 代表 |
|---|---|---|
| **文字映射** | 將圖片轉為 caption，映射到文字語義空間 | MRAMG (Yu et al., 2025) |
| **圖文融合** | 整合多張圖片的 caption + 上下文，生成檢索文字 | MuRAR (Zhu et al., 2025) |
| **聯合解碼** | 圖片與文字同時輸入，聯合解碼生成回應 | MuRAG (Chen et al., 2022) |

無論哪種路線，**圖片語意的映射與對齊**都是核心技術挑戰，也是 Caption Injection 的切入點。

---

## MRAG 如何改變 GSE 的運作模式

GSE 的標準工作流程為：

1. 使用者提交查詢 q
2. 系統從網頁內容中檢索相關來源集 S = {s₁, s₂, ..., sN}
3. LLM 整合來源，生成帶引用的回應 r = generate(S, q)
4. 回應由多個句子組成，每句對應一個或多個引用來源

在 **MRAG 架構**下，步驟 2-3 擴展為同時處理文字與圖片：
- 來源 sᵢ 可能包含文字 + 圖片
- LLM 接收「文字 + 圖片 caption」的組合輸入
- 回應的部分句子可附加語義相關的圖片

這讓 GSE 的回應語意更豐富、信息密度更高，但也使 G-SEO 的難度增加（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

---

## 為什麼 MRAG 讓 G-SEO 更困難

**核心矛盾**：現有 G-SEO 方法都針對文字進行優化，但 MRAG 引入了圖片這個新的語意通道。

| 問題 | 純文字 GSE | 多模態 GSE |
|---|---|---|
| 可優化的輸入 | 文字 | 文字 + 圖片 |
| LLM 的注意力 | 集中於文字 | 分散至文字和圖片 |
| 純文字優化策略的效果 | 正常發揮 | 被圖片語意稀釋 |
| 跨模態語意一致性 | 無需考慮 | 成為關鍵因素 |

實驗數據直接佐證了這個困難：大多數 G-SEO 方法在單模態場景可達 -0.37% 至 -1.12% 的改善，但在多模態場景卻進一步惡化（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

### 主觀可見度（Subjective Visibility）的定義

由於 GSE 的回應是連貫段落而非有序列表，使用者的注意力由主觀感知驅動——**主觀可見度**因此成為 MRAG 場景下 G-SEO 的核心評估指標，取代傳統 SEO 的「排名」。

主觀可見度衡量「來源在 GSE 生成回應中受使用者關注和感知的程度」，以 G-EVAL 2.0 框架的七個維度量化（詳見 [[geo-visibility-metrics]]）。

---

## MRAMG Benchmark 介紹

由於多模態 G-SEO 是新興研究方向，目前尚無專屬公開資料集。Caption Injection 論文採用 **MRAMG**（Multimodal Retrieval-Augmented Multimodal Generation Benchmark）作為實驗基礎（Yu et al., SIGIR 2025）（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

### MRAMG 的特點

| 特性 | 數值 / 描述 |
|---|---|
| 總查詢-內容對 | 4,800 個 |
| 領域數量 | 6 個 |
| 輸入模態 | 文字 + 圖片（模態均衡） |
| 設計初衷 | MRAG 上游任務，查詢-內容對結構與 GSE 生成場景高度吻合 |

### 六個領域

| 領域 | 特點 | 平均文字長度 |
|---|---|---|
| Arxiv | 學術論文 | 中等 |
| Manual | 技術手冊 | **6,365 字元（最長，難度最高）** |
| Recipe | 食譜 | 中短 |
| Web | 通用網頁 | 中等 |
| Wiki | 百科 | 中等 |
| Wit | 圖文並茂的百科 | 中等 |

**Manual 領域是所有方法的共同弱點**：超長文字稀釋了關鍵信息密度，G-SEO 優化難以找到並強化核心語意。

### 評估設定

Caption Injection 論文使用 **GLM-4-9B**（低幻覺率的開源模型）模擬 GSE 回應生成，以排除模型幻覺對優化效果評估的干擾。

評估分兩個場景對比：

| 場景 | 輸入給 LLM | 模擬對象 |
|---|---|---|
| 單模態（Unimodal）| 查詢 + 文字內容 | 純文字 GSE（如早期 Perplexity）|
| 多模態（Multimodal）| 查詢 + 文字 + 圖片 caption | MRAG-enabled GSE（如現代 Perplexity、ChatGPT 搜尋）|

---

## G-EVAL 2.0 在多模態場景的應用

G-EVAL 2.0 沿用 KDD '24 GEO 論文提出的主觀印象七維度框架（relevance、influence、diversity、uniqueness、click-follow probability、positional salience、content volume），在多模態場景下重新應用（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

評分改善量計算公式：

$$\text{improvement}_{(s,s')} = \frac{\text{impressions}_{s'}(r') - \text{impressions}_s(r)}{\text{impressions}_s(r) + 1} \times 100\%$$

每個樣本取三次實驗的平均值以降低隨機性。

在多模態場景中，**唯一性（Uniqueness）**是 Caption Injection 改善最顯著的維度（+3.50%），因為視覺語意提供了純文字來源無法給予的差異化資訊（詳見 [[caption-injection-gseo]]）。

---

## G-SEO 方法分類（依優化手段）

在 MRAG 脈絡下，G-SEO 方法可以分類如下（source: Caption Injection for Optimization in Generative Search Engine.pdf）：

### 文字層級：內容改寫型
- **流暢度優化（Fluency Expression）**：改寫提升可讀性
- **引語添加（Quotation Addition）**：引入名言或引用
- **統計添加（Statistics Addition）**：加入數據
- **傳統 SEO（Traditional SEO）**：關鍵詞優化（在 GSE 中無效甚至有害）

### 文字層級：指令注入型
- **Prompt Injection**：在文字中嵌入引導 LLM 生成的指令（對抗性）

### 多模態：視覺語意注入型（新興）
- **Caption Injection**（本論文）：提取圖片語意、對齊文字語境、注入文字——合作性

---

## 未來研究方向

（source: Caption Injection for Optimization in Generative Search Engine.pdf）

1. **深層跨模態特徵融合**：超越 caption 文字化，探索圖片特徵向量與文字語意空間的直接融合
2. **統一語意空間建構**：讓 G-SEO 能同時在文字和圖片維度優化，而非以文字為主
3. **GSE 偏好分析**：理解不同 LLM 對文字 vs. 圖片輸入的內部偏好，制定針對性的跨模型策略
4. **長文優化方法**：針對高密度技術文件（如手冊類）開發信息密度保持型優化方法

---

## 相關頁面

- [[caption-injection-gseo]] — Caption Injection 三階段流程、實驗結果、實踐意涵
- [[geo-visibility-metrics]] — 主觀可見度七維度指標的完整定義（G-EVAL 框架）
- [[multimodal-geo]] — VLM 排名中圖片可見度的量化影響
- [[geo-optimization-methods]] — 純文字 G-SEO 方法全覽（與 Caption Injection 的對比基線）
- [[generative-engine-architecture]] — 生成式引擎架構類型
