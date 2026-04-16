# 大品牌偏見（Big Brand Bias）

**Summary**: AI 搜尋引擎在無品牌限定的查詢中，系統性地偏向主流大品牌，利基品牌的曝光比例遠低於其市場份額。了解此偏見的機制是利基品牌制定 GEO 策略的起點。

**Sources**:
- Generative Engine Optimization - How to Dominate AI Search.pdf
- Diagnosing and Repairing Citation Failures in Generative Engine Optimization.pdf（Tian et al., 2026）

**Last updated**: 2026-04-15

---

## 核心數據：汽水垂直領域實驗

研究用 50 個無品牌限定的查詢（「最受歡迎的汽水品牌」、「全球最暢銷飲料」等），向 ChatGPT 和 Perplexity 提問，統計各品牌被提及次數。

### 實驗品牌集合

**主流大品牌（20 個）**：Coca-Cola、Pepsi、Sprite、Mountain Dew、Dr Pepper、Fanta、7Up、Schweppes、A&W Root Beer、Barq's Root Beer、Canada Dry、Crush、Sunkist、Mirinda、Fresca、Mello Yello、Seagram's、Slice、Tango、Lilt

**利基/手工品牌（20 個）**：Jones Soda、Boylan Bottling、Sprecher、Faygo、Cheerwine、Moxie、Ale-8-One、Jarritos、Zevia、Fitz's、Maine Root、Bruce Cost Ginger Ale、Sioux City Sarsaparilla、Thomas Kemper、Dry Soda、Virgil's、Leninade、Cock 'n Bull、Blenheim Ginger Ale、Red Ribbon Soda

### 結果

| 引擎 | 主流大品牌 | 利基品牌 | 其他 |
|------|----------|---------|------|
| **ChatGPT** | 56.3% | 12.3% | 31.4% |
| **Perplexity** | 67.9% | 5.8% | 26.3% |
| **合併** | 62.2% | 9.0% | 28.8% |

**Coca-Cola 和 Pepsi 合計佔所有提及的主導地位**：ChatGPT 中 78 + 44 = 122 次；Perplexity 中 107 + 58 = 165 次。

---

## 偏見的成因機制

論文提出兩個主要成因，兩者相互強化：

**1. 來源顯著性（Source Prominence）**
AI 引擎引用的 Earned 媒體網站（Wikipedia、評測網站、新聞媒體），其文章天然偏向撰寫市場領導者。市場份額大的品牌本身就有更多 Earned 媒體報導，進一步強化其在 AI 知識庫中的地位。

**2. 模型先驗（Model Priors）**
LLM 訓練資料中，大品牌出現頻率遠高於利基品牌，即使不做搜尋，模型本身也傾向提及知名品牌。

即使 ChatGPT 和 Perplexity 使用了不同的來源生態（ChatGPT 以 Wikipedia 和評測網站為主；Perplexity 以 YouTube 和多元來源為主），**兩者都出現了大品牌偏見**，顯示偏見的成因是跨引擎的系統性問題。

---

## 各 AI 引擎的大品牌偏見程度

Perplexity 的偏見甚至比 ChatGPT 更強（67.9% vs 56.3% 主流品牌佔比），儘管 Perplexity 引用了更多元的來源。

在知名品牌查詢中，各引擎的一致性（agreement score）達 76–81%；利基品牌查詢的一致性稍低（71–76%），表示 AI 引擎對知名品牌有更一致的推薦。

---

## 利基品牌的突破策略

### 策略一：主導一個細分利基（Vertical Domination）

不要嘗試和大品牌在通用查詢上競爭。深耕一個特定細分市場，成為該細分的第一專家。

**具體行動**：
- 在特定細分（如「最佳有機手工汽水」、「低糖功能性飲料」）建立深度專家內容
- 尋找 Earned 媒體中對應的特定利基媒體（有機食品評測、健康生活雜誌）
- 讓 AI 能輕鬆提取「在 X 類別中最佳」的佐證屬性

### 策略二：Perplexity 優先的 YouTube 策略

Perplexity 會引用 YouTube，其他引擎幾乎不引用。YouTube 是利基品牌建立草根 AI 存在感的可操作入口。

**具體行動**：
- 建立高品質的 YouTube 評測影片（自有或合作 YouTuber）
- 確保影片內容可被 Perplexity 的搜尋索引到

### 策略三：建立可驗證的第三方佐證

AI 引擎的大品牌偏見根源是「大品牌有更多 Earned 媒體覆蓋」。打破偏見的唯一方法是主動增加 Earned 覆蓋。

**具體行動**：
- PR 外展至特定利基媒體（而非通用科技/商業媒體）
- 尋求產品被列入特定用途的「最佳清單」（由 Earned 網站撰寫的清單）
- 如有第三方獲獎、認證、機構背書，在內容中明確標示

### 策略四：結構化比較內容（Justification Engineering）

讓 AI 能輕鬆提取「利基品牌在特定屬性上優於大品牌」的佐證。

**具體行動**：
- 建立與大品牌的比較頁面，明確指出差異化優勢
- 使用「比大品牌更…」的句型，提供可直接引用的比較屬性
- 確保比較內容可被機器掃描（清單格式、表格格式）

---

## 對 GEO 從業者的整體結論

大品牌偏見不會消失，但可以在特定場景下被突破：

1. **利基品牌必須比大品牌更努力建立 Earned 覆蓋**，才能達到同等的 AI 可見度
2. **不同引擎有不同突破點**：Perplexity 對 YouTube 更開放；Gemini 對品牌官網更友善
3. **語言查詢可以幫助利基品牌**：在本地語言市場，大品牌的英文主導地位稍弱，本地利基品牌有機會出現

---

## 🆕 研究補充：Competitive Redundancy——大品牌偏見的系統性後果

AgentGEO 論文（Tian et al., 2026）從 GEO 優化失敗的角度，提供了對大品牌偏見的更細緻理解。

研究將「高權威競爭者壓制」列為 [[citation-failure-taxonomy]] 的第四類——**Systemic Exclusion**（0.6% 的失敗案例），並定義為：

> **Competitive Redundancy**：目標頁面的內容正確且完整，但生成式引擎選擇了更高權威的來源（如 Wikipedia、Coursera、edX）來引用相同事實。

**關鍵實驗案例**：

一個機器學習大學課程頁面針對查詢「最佳線上機器學習課程」：
- AgentGEO 成功優化了頁面，增加了詳細的課程描述，顯著提升了內容品質
- 但生成式引擎**持續選擇 Coursera、edX 等平台**，完全無視優化後的大學頁面
- 結論：GE 可能存在**領域層面的內部偏見**（domain-level bias），超出了頁面內容本身的控制範圍

**更深層的意涵**：

這不只是「大品牌有更多 Earned Media 覆蓋」的問題（多倫多大學論文的解釋），而是說明 GE 的引用偏見**已經被嵌入模型本身的先驗**。即使在相同的文件集中、相同的查詢下，高權威競爭者的存在本身就足以讓其他頁面的優化努力白費。

**對策的侷限性**：

與一般的大品牌偏見不同，Competitive Redundancy 沒有「內容側的解法」。當競爭者是 Wikipedia 或 Coursera 時，唯一的路線是：
1. 選擇這些平台**尚未深入覆蓋**的利基子主題
2. 在 Earned Media 上建立更強的第三方背書
3. 接受某些查詢場景的系統性劣勢

---

## 相關頁面

- [[geo-dominate-ai-search]] — 來源論文摘要
- [[ai-search-earned-media-bias]] — AI 對 Earned Media 的系統性偏好
- [[ai-search-engine-comparison]] — 各引擎對利基品牌的不同行為
- [[geo-language-strategy]] — 語言策略可如何幫助利基品牌
- [[geo-optimization-methods]] — 具體內容優化方法
- [[citation-failure-taxonomy]] — Systemic Exclusion 在引用失敗分類法中的位置
- [[agentgeo-framework]] — 對不可修復失敗案例的分析
