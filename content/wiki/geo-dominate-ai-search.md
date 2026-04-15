# GEO：學術研究綜覽

**Summary**: GEO 領域兩篇核心學術論文的整合摘要頁：KDD '24 奠基論文（Princeton/IIT Delhi）首次定義 GEO 框架與可見度指標；多倫多大學 2025 年論文透過大規模實驗揭示 AI 搜尋引擎行為差異。

**Sources**:
- GEO - Generative Engine Optimization.pdf（Aggarwal et al., KDD '24）
- Generative Engine Optimization - How to Dominate AI Search.pdf（Chen et al., 2025）
- Diagnosing and Repairing Citation Failures in Generative Engine Optimization.pdf（Tian et al., 2026）

**Last updated**: 2026-04-15

---

## 一、KDD '24 奠基論文：GEO 框架的起源

### 論文基本資訊

- **標題**：GEO: Generative Engine Optimization
- **作者**：Pranjal Aggarwal\*、Vishvak Murahari\*（\*共同第一作者）、Tanmay Rajpurohit、Ashwin Kalyan、Karthik Narasimhan、Ameet Deshpande
- **機構**：IIT Delhi + Princeton University
- **發表**：KDD '24，2024-08-25，Barcelona（arXiv:2311.09735）
- **研究性質**：首次提出 GEO 範式，定義可見度指標，建立評測基準

### 核心問題

生成式引擎（BingChat、Google SGE、Perplexity.ai）直接回答問題，不再要求使用者點入網站，**網站流量因此消失**。內容創作者對引擎如何引用自己的內容幾乎毫無控制。GEO 是第一個以**黑箱優化框架**賦權內容創作者的方案。

### 生成式引擎的技術架構

生成式引擎（GE）的標準流程：

```
使用者查詢
    ↓
查詢改寫模型（G_qr）：拆解為多個子查詢
    ↓
搜尋引擎（SE）：取回 top-K 來源文件
    ↓
摘要模型（G_sum）：對每個來源生成摘要
    ↓
回應生成模型（G_resp）：整合摘要，產出帶 inline citation 的結構化回應
```

與傳統搜尋引擎的根本差異：傳統搜尋給出「排名清單」，GE 給出「合成答案」。可見度的意義因此完全不同——不是排在第幾，而是**在回應中被引用了多少、在哪裡、如何被引用**。

詳見 [[generative-engine-architecture]]

### GEO-bench：評測基準

| 規格 | 數值 |
|---|---|
| 查詢總數 | 10,000 |
| 訓練 / 驗證 / 測試 | 8K / 1K / 1K |
| 來源資料集數 | 9 個（MS MARCO、ORCAS-1、Natural Questions、AllSouls、LIMA、Davinci-Debate、Perplexity Discover、ELI5、GPT-4 生成）|
| 涵蓋領域 | 25 個（藝術、健康、法律、科學…） |
| 查詢類型 | 9 種（資訊型 80%、交易型 10%、導覽型 10%）|
| 標籤維度 | 7 個（難度、性質、類型、主題、敏感度、意圖、答案格式）|

### 可見度指標體系

GEO 提出三層可見度指標，專門設計給 GE 環境（傳統 SEO 的「排名」在 GE 中無意義）：

| 指標 | 說明 | 特點 |
|---|---|---|
| **字數佔比**（Word Count） | 引用該來源的句子字數 / 回應總字數 | 客觀、直觀 |
| **位置調整字數**（Position-Adjusted Word Count） | 字數佔比 × 位置衰減係數（越早出現越重要） | 客觀、反映閱讀行為 |
| **主觀印象**（Subjective Impression） | 7 個子指標的 LLM 評估綜合分 | 主觀、全面 |

詳見 [[geo-visibility-metrics]]

### 9 種 GEO 優化策略

論文測試了 9 種內容改寫策略，最有效的三種為引語添加（+41%）、統計添加（+31%）、引用添加（+27%）；關鍵詞堆砌是唯一反效果的方法（-8%）。低排名網站（Rank-5）從 GEO 中的獲益遠高於高排名網站，體現「民主化效應」。

完整效果表、組合矩陣、排名位置分析詳見 [[geo-optimization-methods]]。

### 真實引擎驗證（Perplexity.ai）

| 方法 | PAWC 提升 | 主觀印象提升 |
|---|---|---|
| 引語添加 | **+22%** | +30% |
| 統計添加 | +9% | **+37%** |
| 引用添加 | +11% | -23%（特例）|
| 關鍵詞堆砌 | -10% | +14% |

### 論文核心貢獻摘要

1. **GEO 範式**：首次定義內容創作者在 GE 時代的優化框架
2. **可見度指標**：三層指標系統（字數、位置調整字數、主觀印象），可客製化
3. **GEO-bench**：10K 查詢基準，25 個領域，公開可用

---

## 二、多倫多大學 2025 年論文：大規模行為研究

### 論文基本資訊

- **標題**：Generative Engine Optimization: How to Dominate AI Search
- **作者**：Mahe Chen、Xiaoxuan Wang、Kaiwen Chen、Nick Koudas（多倫多大學）
- **發表時間**：2025 年 9 月（arXiv:2509.08919）
- **研究方法**：大規模受控實驗，跨多個垂直領域、語言、查詢改寫變體

## 核心研究問題

傳統 SEO 技術（針對 Google 關鍵詞排名優化的方法）在 AI 搜尋引擎（ChatGPT、Perplexity、Gemini、Claude）中是否仍然有效？品牌的 AI 搜尋能見度是否與 Google 排名一致？

## 主要發現摘要

### 發現一：AI 搜尋系統性偏向 Earned Media

所有 AI 引擎都對第三方媒體（評測網站、媒體報導、機構網站）有壓倒性偏好，而 Google 則維持 Brand / Earned / Social 的均衡分佈。這是本研究最一致、最重要的發現。

詳見 [[ai-search-earned-media-bias]]

### 發現二：AI 引擎彼此差異顯著

ChatGPT、Perplexity、Gemini、Claude 在來源多樣性、跨語言穩定性、對查詢改寫的敏感度上各有不同行為模式，無法用單一策略覆蓋所有引擎。

詳見 [[ai-search-engine-comparison]]

### 發現三：語言切換對結果影響巨大

相同查詢換成不同語言後，各引擎的引用 domain 變化幅度遠大於改寫同語言查詢的影響。Claude 最穩定，GPT 最敏感（幾乎完全換掉引用生態）。

詳見 [[geo-language-strategy]]

### 發現四：AI 查詢有固定分類模式

從 Reddit 分析出 12 種主要 AI 查詢類型，購物類查詢進一步細分為 14 種子類別，顯示使用者正在將 AI 從查詢工具升級為購買決策代理人。

詳見 [[ai-query-taxonomy]]

### 發現五：大品牌偏見（Big Brand Bias）

無品牌限定的查詢中，主流大品牌佔 AI 推薦提及的 62.2%，利基品牌只有 9%。利基品牌面臨系統性劣勢。

詳見 [[big-brand-bias]]

## GEO 策略框架（本文提出）

本文提出四個 GEO 核心戰略方向：

1. **主導 Earned Media**：將公關、媒體覆蓋、反向連結建設列為核心 GEO 策略（而非附加選項）
2. **為機器可讀性設計**：Schema.org 標記、比較表、結構化規格，讓 AI 能輕鬆提取佐證
3. **引擎特定策略**：Claude/ChatGPT 靠英文權威媒體；GPT/Perplexity 需本地語言媒體；Gemini 可兼顧品牌官網
4. **全生命週期內容**：從認知（最佳 X for Y 指南）→考慮（比較表）→決策（規格/保固）→售後（FAQ/故障排除）→忠誠（配件、進階使用）

## 三、AgentGEO 2026：診斷式引用修復

### 論文基本資訊

- **標題**：Diagnosing and Repairing Citation Failures in Generative Engine Optimization
- **作者**：Zhihua Tian\*、Yuhan Chen\*、Yao Tang\*（\*共同第一作者）、Jian Liu、Ruoxi Jia
- **機構**：Virginia Tech + Zhejiang University
- **發表時間**：2026-03-11（arXiv:2603.09296v1）

### 核心問題

既有 GEO 方法（包括 KDD '24 的 9 種方法和 AutoGEO 的通用規則）都套用「通用」改寫策略。但**43% 的相關頁面根本得不到任何引用**——這些頁面的問題不是「引用質量」，而是「為什麼完全沒被引用」。

### 三大貢獻

**1. 引用失敗分類法**：首個橫跨 Fetching→Parsing→Generation 流水線的系統性分類，基於 949 組對比案例，識別四大類十二種失敗模式：Technical Integrity（10.1%）、Semantic Alignment（62.2%）、Content Quality（27.1%）、Systemic Exclusion（0.6%）。

**2. AgentGEO 框架**：基於診斷結果的迭代修復代理，配備 9 種針對性工具，帶記憶機制避免重複無效嘗試，批量聚合防止過擬合。

**3. MIMIQ 基準**：文件中心式評測基準，每個文件 60 個查詢（訓練/測試分割），支援 HTML 結構評估。

### 核心成果

| 指標 | Vanilla | AutoGEO | AgentGEO |
|---|---|---|---|
| 引用率（GPT） | 56.58% | 68.80% | **79.52%** |
| 引用率（Claude） | 42.40% | 46.80% | **54.80%** |
| 平均內容修改量 | — | 25% | **5%** |

### 三個反直覺發現

1. **診斷優先原則**：大多數頁面需要針對性修復，而不是全面重寫——5% 的修改勝過 25% 的修改
2. **通用規則的長尾陷阱**：AutoGEO 等通用方法在健康、旅遊等特定主題上反而降低引用率
3. **存在不可修復的失敗**：當競爭者是 Coursera/Wikipedia 等高權威平台時，任何內容優化都無法改變結果——這暗示 GE 存在超出頁面內容控制的系統性偏見

詳細內容：
- 分類法詳見 [[citation-failure-taxonomy]]
- 框架詳見 [[agentgeo-framework]]
- 基準詳見 [[mimiq-benchmark]]
- 指標討論詳見 [[geo-citation-vs-contribution]]

---

## 研究限制

- 資料收集時間點：2025 年 8 月，AI 引擎行為持續演變，結論不應視為永久事實
- Brand / Earned / Social 三分類為研究者建構，不同分類系統會產生不同數字
- 外部觀察研究，無法取得引擎內部排名邏輯

## 相關頁面

### AgentGEO 2026 論文延伸
- [[citation-failure-taxonomy]] — 四類十二種引用失敗分類
- [[agentgeo-framework]] — 診斷修復框架
- [[mimiq-benchmark]] — 文件中心式評測基準
- [[geo-citation-vs-contribution]] — 引用率 vs 貢獻度

### KDD '24 論文延伸
- [[geo-visibility-metrics]] — GE 可見度指標系統：字數佔比、位置調整字數、主觀印象 7 維度
- [[geo-optimization-methods]] — 9 種 GEO 方法詳解與組合策略
- [[geo-domain-specific-strategies]] — 25 個領域的優化地圖與查詢類型指南
- [[generative-engine-architecture]] — 引擎架構與結構偏好

### 多倫多大學 2025 年論文延伸
- [[ai-search-earned-media-bias]] — AI 偏向 Earned Media 的核心發現
- [[ai-search-engine-comparison]] — 四大 AI 引擎行為特性比較
- [[geo-language-strategy]] — 多語言 GEO 策略
- [[ai-query-taxonomy]] — AI 查詢分類與購物行為
- [[big-brand-bias]] — 大品牌偏見與利基品牌突破策略
