# AI 搜尋引擎行為比較

**Summary**: ChatGPT、Claude、Perplexity、Gemini 四大 AI 搜尋引擎在來源選擇、跨語言穩定性、查詢改寫敏感度和領域行為上有顯著差異，需要引擎特定的 GEO 策略。

**Sources**: Generative Engine Optimization - How to Dominate AI Search.pdf

**Last updated**: 2026-04-15

---

## 引擎特性總覽

| 特性 | ChatGPT (GPT-4o) | Claude | Perplexity | Gemini |
|------|-----------------|--------|-----------|--------|
| **Earned 傾向** | 極強（93–95%） | 強（86–87%） | 中（67–73%） | 中（63–66%） |
| **Brand 引用** | 極低（5–7%） | 低（7–11%） | 中（9–29%） | 中–高（21–25%） |
| **Social 引用** | 0% | ~3% | 17–24% | 11–13% |
| **跨語言 domain 穩定性** | 最低（近乎 0） | 最高 | 低–中 | 低–中 |
| **查詢改寫穩定性** | 中 | — | 中（部分類別敏感） | 高 |
| **網站語言偏好** | 本地語言為主 | 英文為主 | 本地語言為主 | 均衡 |

（來源：Generative Engine Optimization - How to Dominate AI Search.pdf）

---

## 各引擎詳細行為

### ChatGPT（GPT-4o）

**Source Mix 特徵**：最 Earned-heavy，Social 接近 0%，以知識庫型網站（Wikipedia）和新聞媒體（AP News、Reuters）為主。

**跨語言行為**：Jaccard overlap 接近 0，即中文查詢和英文查詢會引用完全不同的網站生態系。這是四個引擎中語言敏感度最高的。

**查詢改寫穩定性**：domain 穩定性中等，但品牌推薦列表相對穩定（大多數改寫變體不會改變核心品牌推薦）。

**消費電子頂級引用網站**：TechRadar、Tom's Guide、Wikipedia

**汽車頂級引用網站**：Wikipedia、Automoblog、AP News

**GEO 策略重點**：
- 必須在目標語言的本地權威媒體建立 Earned 存在
- 英文市場：鎖定百科型、新聞型媒體的評測或報導
- 非英文市場：翻譯官網內容不夠，需要在當地語言媒體被報導

---

### Claude

**Source Mix 特徵**：Earned 強（87%），但稍低於 ChatGPT，極少 Social。

**跨語言行為**：四個引擎中跨語言 domain 穩定性最高——英文查詢和中文/日文/德文/法文查詢引用的 domain 大量重疊（Jaccard overlap 常在 0.4–0.8 之間）。意味著 Claude 會跨語言繼續引用同一批英文權威網站。

**網站語言**：非英文查詢下，Claude 引用的網站仍以英文為主（獨特行為，其他引擎都更傾向本地語言）。

**消費電子頂級引用網站**：TechRadar、Tom's Guide、RTINGS

**汽車頂級引用網站**：Consumer Reports、Car and Driver、US News

**GEO 策略重點**：
- 獲得頂級英文 Earned 媒體的覆蓋，可以跨語言帶來 Claude 的 AI 可見度
- 不需要為每種語言都建立本地 Earned 媒體，英文權威地位可以「帶跨語言」

---

### Perplexity

**Source Mix 特徵**：最多元，Earned 約 67–73%，但 Brand 和 Social 佔比遠高於其他引擎。YouTube 常出現在引用列表中，零售商（BestBuy）也會被引用。

**跨語言行為**：跨語言 domain 穩定性低，傾向引用目標語言的本地媒體。

**查詢改寫敏感度**：品牌推薦列表相對穩定，但某些垂直領域（如遊戲主機）對查詢改寫較敏感，domain 選擇有較大變化。

**特殊行為**：消費電子中引用 YouTube 和 BestBuy 這類零售商，這在其他引擎幾乎不出現。汽車查詢中引用 autozone.com、cars.com 等商業網站。

**GEO 策略重點**：
- YouTube 視頻評測是有效的 GEO 策略（Perplexity 的特殊入口）
- 在主要零售商（如 BestBuy、Cars.com）確保產品資訊正確和完整
- 需要在目標語言的本地媒體建立存在

---

### Gemini

**Source Mix 特徵**：四個引擎中最傾向 Brand 的，Brand 佔比常達 20–30%，允許更多品牌官網出現在引用中。

**跨語言行為**：跨語言 domain 穩定性低–中，部分語言（如中文）有較高交叉，其他語言則較低。

**查詢改寫穩定性**：品牌推薦最穩定（多數垂直領域的 Jaccard 在 0.4–0.7 之間）；domain 穩定性也高。

**銀行查詢特例**：Gemini 在銀行查詢中是最 Brand-leaning 的引擎，Brand 佔比常超過 50%（其他引擎在銀行查詢中 Earned 仍居主導）。

**GEO 策略重點**：
- Gemini 是四個引擎中品牌官網最能直接影響的
- 品牌官網的深度內容和良好結構化，對 Gemini 有較大槓桿效應
- 同時維持 Earned 策略，因為 Earned 仍佔多數

---

## 跨引擎共識：核心 Earned 網站

雖然各引擎的 domain 生態系大部分不重疊（各有 50–68% 的獨佔 domain），但存在一小批所有引擎都會引用的「核心網站」。在這些核心網站獲得覆蓋是影響所有引擎的最高效策略。

**消費電子核心網站**：TechRadar、Tom's Guide、RTINGS

**汽車核心網站**：Car and Driver、Edmunds、Consumer Reports

**金融核心網站**：Bankrate、NerdWallet

**一般新聞/知識**：Wikipedia、AP News、Reuters

---

## 引擎選擇策略矩陣

| 目標場景 | 優先引擎策略 |
|---------|-----------|
| 英文市場、全球品牌 | 鎖定 Claude/ChatGPT 的英文 Earned 核心網站 |
| 非英文市場 | 建立當地語言 Earned 媒體（GPT、Perplexity 本地化強） |
| 有 YouTube 內容策略 | 對 Perplexity 特別有效 |
| 電商/零售場景 | Perplexity 引用零售商，確保零售商資訊準確 |
| 品牌官網深度內容 | Gemini 較願意引用品牌官網 |

---

## 相關頁面

- [[geo-dominate-ai-search]] — 來源論文摘要
- [[ai-search-earned-media-bias]] — Earned Media 偏好的詳細分析
- [[generative-engine-architecture]] — 引擎架構（先搜後合、迭代細化、集成搜生）
- [[geo-language-strategy]] — 語言策略（跨語言穩定性詳解）
- [[big-brand-bias]] — 大品牌偏見
