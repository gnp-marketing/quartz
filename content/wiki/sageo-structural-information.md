# 網頁結構資訊與 SAGEO 優化

**Summary**: 真實網頁包含五類結構資訊（title、meta description、headings、Schema/JSON-LD、body text），這些欄位在 SAGE pipeline 的各個階段扮演截然不同的角色，是 SAGEO Arena 研究的核心發現之一。

**Sources**:
- SAGEO Arena - A Realistic Environment for Evaluating Search-Augmented Generative Engine Optimization.pdf（Kim et al., 延世大學，arXiv:2602.12187，2026-02-12）

**Last updated**: 2026-04-16

---

## 五類結構欄位

依據 Google 和 Bing Webmaster Guidelines，SAGEO Arena 保留以下五個欄位：

### 1. Title（頁面標題）

- **作用**：頁面主題的簡潔摘要，搜尋系統的初始相關性評估主要信號
- **SEO 意義**：BM25 等詞彙檢索器對 title 賦予高權重
- **GEO 意義**：摘要性強，但因過於簡短而被生成模型引用的比例相對低

### 2. Meta Description（元描述）

- **作用**：搜尋結果頁的摘要片段，補充 title 的簡潔頁面描述
- **優化潛力**：SAGEO Arena 發現，將 meta description 改寫為包含具體事實的句子（如加入「1983 年、喜劇、Rotten Tomatoes、票房 $61M」等實體）可大幅提升檢索命中率
- **典型案例**：模糊的 meta description → 含年份、類型、評分的具體摘要

### 3. Headings（標題層級 H1–H6）

- **作用**：文件主題結構的層級標記，搜尋引擎視其為比 body text 更強的主題信號
- **密度優勢**：headings 天然集中查詢相關詞彙，BM25 的詞彙匹配在此特別有效
- **生成引用**：headings 被生成模型直接引用的比例低，但有助於文件被檢索進入候選集

### 4. Schema/JSON-LD（結構化標記）

- **作用**：以機器可讀格式明確定義實體、屬性和關係，讓搜尋引擎理解純文字無法推斷的語義
- **例子**：商品頁的 price、rating、availability；文章的 author、datePublished
- **引擎依賴**：Bing Copilot 和 Google Search 均有依賴結構化資料的文獻記錄

### 5. Body Text（主要內容）

- **作用**：文件的主要資訊內容，生成模型引用的主要來源
- **Pipeline 角色**：在重排和生成階段更重要；結構資訊主要主導檢索階段
- **處理方式**：切分成 256 token 段落（64 token 重疊），段落層級參與檢索和重排

---

## 各欄位在 Pipeline 中的角色分工

| 欄位 | 檢索（BM25） | 重排（Cross-encoder） | 生成（LLM） |
|------|------------|---------------------|-----------|
| Title | ★★★ 關鍵 | ★★ 有助 | ★ 少量引用 |
| Meta Description | ★★★ 關鍵 | ★★ 有助 | ★ 少量引用 |
| Headings | ★★★ 關鍵 | ★★ 有助 | ★ 少量引用 |
| Schema/JSON-LD | ★★ 有助 | ★ 有限 | ★ 少量引用 |
| Body Text | ★ 有限 | ★★★ 關鍵 | ★★★ 主要來源 |

**核心發現**：結構資訊主導檢索，body text 主導生成。兩者在 SAGE pipeline 中是互補關係，缺一不可（source: SAGEO Arena）。

---

## 引用密度分布（Citation Source Analysis）

SAGEO Arena 針對引用來源進行實驗：要求生成模型提供每個引用的原文摘錄，再用模糊匹配定位到原始文件的具體位置。

**結果（Figure 6）**：
- body text 的引用密度在文件整體範圍均明顯高於基準線（y > 1.0）
- 結構資訊欄位的引用密度普遍低於基準線
- 結構資訊在「讓文件被檢索到」上貢獻巨大，但生成模型在回答時更依賴 body text 的資訊密度

**解釋**：結構欄位設計上偏向簡潔關鍵字，資訊密度不如 body text 豐富，生成模型不傾向直接引用。

---

## 結構資訊優化的效果數據

以「僅優化結構資訊」vs「僅優化 body text」的對比（來自 Table 2）：

| 指標 | 僅 body text | 僅結構資訊 |
|------|------------|-----------|
| 檢索 H@20 | 0.53（−9%） | **0.71（+22%）** |
| 檢索 ΔRank | −4.54 | **+2.72** |
| 重排 H@10 | 0.84（−16%） | 0.83（−17%） |
| 生成 Citation Rate | 0.47（−6%） | **0.52（+2%）** |

**關鍵洞察**：
- 結構資訊優化在檢索階段帶來 +22% 的命中率，是 body text 優化的**逆轉**（body text −9%）
- 即使只優化結構資訊，生成階段也有小幅改善——因為更多文件通過了檢索這一關
- 結構資訊優化後，文件在頂部放置了更有組織性的摘要，符合「前置資訊幫助生成」的既有發現

---

## 優化結構欄位的實踐建議

### 統計添加策略的效果

在結構欄位中加入統計數據（Stats 策略）：
- 將冗長的 meta description 改寫為含具體數字的簡潔摘要
- 例：「1983 年上映的喜劇電影，在 Rotten Tomatoes 評分 78%，票房達 $61M」
- 檢索 H@20 提升至 **+29%**，ΔRank **+6.03**

### 引語策略的效果

在 title 或 headings 中加入實體特定詞彙（Quote 策略）：
- 將模糊標題「Panel Clarifies Advice」→「IOM Panel Clarifies Vitamin D Guidance」
- 引入更具體的實體詞彙，提升與用戶查詢的詞彙重疊
- 檢索 H@20 提升至 **+27%**，ΔRank **+5.47**

### Stage-Aware SAGEO 的結構欄位原則

詳見 [[stage-aware-sageo]]，核心是「Entity Mirroring」：
> 將 body text 中的核心實體、數字和領域術語整合進 title、meta description、headings 和 JSON-LD，同時保持結構欄位的精簡。

---

## 相關頁面

- [[sageo-arena-benchmark]] — SAGEO Arena benchmark 完整說明
- [[stage-aware-sageo]] — 階段感知優化方法與主要實驗發現
- [[macro-structure-optimization]] — 文件層級結構優化（標題、導航、交叉參考）
- [[micro-structure-optimization]] — 句子層級優化（關鍵詞位置、強調）
- [[geo-optimization-methods]] — 各種優化策略的效果數據
