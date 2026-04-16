# Wiki 操作日誌

**Last updated**: 2026-04-15

---

## [2026-04-15] ingest | AgentGEO — 診斷式引用失敗修復框架

**來源**：Diagnosing and Repairing Citation Failures in Generative Engine Optimization.pdf
**作者**：Zhihua Tian\*、Yuhan Chen\*、Yao Tang\*（共同第一作者）、Jian Liu、Ruoxi Jia（Virginia Tech + Zhejiang University）
**日期**：2026-03-11（arXiv:2603.09296v1）

**建立的頁面**（4 頁）：
1. citation-failure-taxonomy — 引用失敗四類十二種分類法（949 個對比案例分析）
2. agentgeo-framework — AgentGEO 診斷修復框架（三階段流水線、9 種工具、記憶機制）
3. geo-citation-vs-contribution — 引用率（CR）vs 貢獻度（Word Count）的根本差異
4. mimiq-benchmark — 文件中心式評測基準（204 文件 × 60 查詢）

**更新的頁面**（6 頁）：
- citation-performance-metrics — 補充 CR 作為根本性指標及 AgentGEO 基線數據
- geo-optimization-methods — 補充 AgentGEO 9 種針對性工具，加「通用規則長尾陷阱」警告
- big-brand-bias — 補充 Competitive Redundancy 的 AgentGEO 案例和「不可修復失敗」分析
- generative-engine-architecture — 補充 Inline/Pre-hoc/Post-hoc Attribution 三種引用機制
- geo-cooperative-vs-adversarial — 補充合作性 GEO 的邊界（不可修復失敗場景）
- geo-dominate-ai-search — 新增「三、AgentGEO 2026」章節整合本論文核心發現

**主要發現**：
- 43% 的相關頁面在基線條件下完全未被引用；引用失敗最主要類別是 Semantic Alignment（62.2%）
- AgentGEO（診斷式）比 AutoGEO（通用規則）多出 10.72 個百分點引用率，且只需 5% 的內容修改量（vs 25%）
- 通用規則在高原始引用率的主題（健康類）反而降低引用率
- Competitive Redundancy（面對 Wikipedia/Coursera 等）是「不可修復失敗」——任何內容優化無效
- Claude 在答案品質指標上優於 GPT，但引用率對優化的反應比 GPT 更慢

---

## [2026-04-15] ingest | AutoGEO — 生成式引擎偏好規則自動提煉

**來源**：What Generative Search Engines Like and How to Optimize Web Content Cooperatively.pdf
**作者**：Yujiang Wu、Shanshan Zhong（共同第一）、Yubin Kim、Chenyan Xiong（CMU + Vody）
**日期**：2025-10-13（arXiv:2510.11438v1，預印本）

**建立的頁面**（3 頁）：
1. autogeo-framework — AutoGEO 技術框架（Explainer/Extractor/Merger/Filter pipeline + 兩種模型）
2. geo-preference-rules — 系統性偏好規則集（共同規則 15 條、引擎獨特規則、領域差異分析）
3. geo-cooperative-vs-adversarial — 合作性 GEO vs 對抗性攻擊（GEU 指標、實驗數據對比）

**更新的頁面**：
- geo-optimization-methods — 補充 AutoGEO 與既有 9 種方法的性能對比，加入相關頁面連結
- wiki/index.md — 新增「自動化框架（AutoGEO, 2025）」子分類，整合 3 個新頁面

**主要發現**：
- AutoGEO_API 比最強手工方法（Fluency Optimization）在 GEO-Bench 上再提升 +47.3%，平均提升 +35.99%
- AutoGEO_Mini 成本僅為 AutoGEO_API 的 ~0.0071x，仍達 +20.99% 平均提升
- 對抗性攻擊（Hijack/Poisoning）雖提升 GEO，但系統性損害 GEU（KPR、Clarity、Insight 全面下降）
- 跨引擎規則重疊率 79–84%；跨領域（開放域 vs 電商）重疊率僅 34–40%
- 電商領域需要 Step-by-Step、Production Details 等獨特規則；研究型需要 In-depth、Balanced View

---

## [2026-04-15] ingest | GEO 主導 AI 搜尋論文

**來源**：Generative Engine Optimization - How to Dominate AI Search.pdf
**作者**：Mahe Chen、Xiaoxuan Wang、Kaiwen Chen、Nick Koudas（多倫多大學，2025 年 9 月）

**建立的頁面**（6 頁）：
1. geo-dominate-ai-search — 論文摘要、核心發現、GEO 策略框架
2. ai-search-earned-media-bias — AI 引擎 Earned Media 偏好的實驗數據與策略涵義
3. ai-search-engine-comparison — ChatGPT / Claude / Perplexity / Gemini 行為特性比較
4. ai-query-taxonomy — 12 種 AI 查詢類型 + 14 種購物子類別
5. big-brand-bias — 大品牌偏見的成因與利基品牌突破策略
6. geo-language-strategy — 多語言 GEO 策略（各引擎跨語言穩定性分析）

**更新的頁面**：
- generative-engine-architecture — 補充各引擎 Earned Media 傾向數據和 Domain 多樣性統計
- geo-optimization-methods — 補充 Earned Media 建設是比內容優化更根本的 GEO 層級
- wiki/index.md — 新增「AI 搜尋引擎行為」子分類，整合 5 個新頁面

**主要發現**：
- AI 引擎對 Earned Media 的系統性偏好（70–93%），Social 幾乎為 0%，與 Google 均衡分佈形成強烈對比
- Claude 跨語言 domain 最穩定（英文權威網站可跨語言影響）；GPT 語言切換時 domain 幾乎完全替換
- 大品牌偏見：無品牌限定查詢中主流品牌佔 62.2%，利基品牌只有 9%
- 語言切換對引用結果的影響遠大於查詢改寫
- 各引擎 domain 生態 50–68% 為獨佔，無法用單一策略覆蓋所有引擎

---

## [2026-04-15] ingest | 結構特徵工程論文

**來源**：Structural Feature Engineering for Generative Engine Optimization - How Content Structure Shapes Citation Behavior.pdf

**建立的頁面**（7 頁）：
1. geo-sfe-framework — GEO-SFE 框架概述、研究動機和創新
2. macro-structure-optimization — 宏觀結構優化的 5 項原則和工作流
3. meso-structure-optimization — 中觀結構優化的 3 項原則和實踐指南
4. micro-structure-optimization — 微觀結構優化的 3 項原則和應用技巧
5. generative-engine-architecture — 三種生成式引擎架構（先搜後合、迭代細化、集成搜生）
6. citation-performance-metrics — 客觀指標（CR、VS）和主觀評估方法
7. geo-optimization-results — 實驗結果、統計數據和層級貢獻度分析

**更新的頁面**：
- wiki/index.md — 添加搜尋優化分類和 7 個新頁面的索引

**主要發現**：
- 結構優化帶來 17.3% 的引用率提升（p < 0.001）
- 三層結構貢獻度：宏觀 45% + 中觀 40% + 微觀 15%
- 感知品質提升 18.5%，其中影響力和點擊概率最高（> 31%）
- 優化在所有 6 個生成式引擎和 6 個內容領域間高度一致

---

## [2026-04-15] ingest | GEO 奠基論文（KDD '24）

**來源**：GEO - Generative Engine Optimization.pdf（Aggarwal et al., KDD '24，arXiv:2311.09735）
**作者**：Pranjal Aggarwal, Vishvak Murahari 等（IIT Delhi + Princeton University）

**建立的頁面**（4 頁）：
1. geo-visibility-metrics — GE 可見度指標系統：字數佔比（含數學公式）、位置調整字數（含指數衰減設計原理）、主觀印象 7 維度（含各方法全矩陣數據）、三層指標對比、與 GEO-SFE 指標的差異
2. geo-optimization-methods — 9 種內容優化方法、效果對標、方法組合策略
3. geo-content-strategy-vs-structure — 內容策略 vs 結構優化的區別與協同效應
4. geo-domain-specific-strategies — 25 個領域的優化地圖、查詢類型指南、排名位置分析

**更新的頁面**：
- geo-dominate-ai-search — 重大擴充：新增「KDD '24 奠基論文」完整章節，含 GE 架構圖解、GEO-bench 規格表、9 種方法完整效果表（含 PAWC 絕對分數）、最佳策略組合矩陣、民主化效應排名分析表、Perplexity.ai 真實驗證數據
- wiki/index.md — 重組 GEO 分類，新增「優化方向」子分類，整合新頁面連結

**主要發現**：
- 內容策略優化可帶來 40% 的可見性提升（對標結構優化的 17.3%）
- 最高效的三種方法：引語添加 (+41%)、統計添加 (+28%)、引用添加 (+30%)
- 傳統 SEO 的關鍵詞堆砌在生成式引擎上**反效果 -8%**
- 低排名網站（Rank-5）透過引用添加可實現 +115% 提升，而 Rank-1 網站 -30%（民主化效應）
- PAWC vs 主觀印象的分歧：權威風格 PAWC 僅 +10% 但主觀印象 +19%（流量目標 vs 品牌曝光目標應用不同指標）
- 所有指標正規化：各來源佔比總和 = 1（零和競爭），與 GEO-SFE 的非零和 CR 指標形成對比
- 流暢性優化 + 統計添加是最佳組合方案（>35% 提升）

---

## [2026-04-15] 初始化 | Wiki 架構建立

- 建立 wiki/index.md（目錄索引）
- 建立 wiki/log.md（操作日誌）
