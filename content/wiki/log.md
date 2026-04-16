# Wiki 操作日誌

**Last updated**: 2026-04-16

---

## [2026-04-16] ingest | Caption Injection — 第一個多模態 G-SEO 方法

**來源**：Caption Injection for Optimization in Generative Search Engine.pdf
**作者**：Xiaoluchen, Jie Bao, Haojie Wu, Zhen Chen, Yong Liao
**日期**：arXiv:2511.04080v2，2026-03-18

**新增頁面**：
- `caption-injection-gseo.md`：Caption Injection 方法詳解——三階段流程（Structural Generation / Alignment Refinement / Semantic Injection）、O-A-S 三元組、MRAMG 實驗結果（多模態 +1.12%）、消融研究（Refined > Structural；single-shot > multi-shot）、實踐意涵
- `mrag-gseo.md`：多模態 RAG 架構對 G-SEO 的挑戰、MRAMG benchmark 介紹（4,800 查詢、6 領域）、G-EVAL 2.0 評估框架、G-SEO 方法分類

**更新頁面**：
- `geo-optimization-methods.md`：新增「2026 年更新：Caption Injection」章節，含完整對比表和實踐建議；更新 Sources 和相關頁面連結
- `multimodal-geo.md`：「合作性多模態 GEO 的未來方向」改為「第一個方法：Caption Injection」，補充對比表（合作性 vs 對抗性）；更新相關頁面連結
- `geo-cooperative-vs-adversarial.md`：新增「合作性多模態 G-SEO：Caption Injection」章節，補充三欄對比表；更新 Sources 和相關頁面連結；實踐建議新增第 4 點
- `geo-visibility-metrics.md`：新增「G-EVAL 2.0 在多模態場景的應用」章節，含七維度增量對比表；更新 Sources 和相關頁面連結

**主要發現**：
- Caption Injection 在多模態 GSE 場景主觀可見度達 +1.12%，是唯一顯著正向改善的方法（純文字最強對手流暢度優化僅 +0.71%）
- 改善最集中在「唯一性（Uniqueness）」維度（+3.50%）——圖片語意提供差異化資訊
- 多模態場景中，精煉後 caption 優於結構性 caption；single-shot 注入優於 multi-shot
- 所有方法在長文（Manual，avg 6,365 字元）中效果均差，信息密度稀釋是瓶頸
- 傳統 SEO 關鍵詞優化在多模態 GSE 中仍有害（-0.66%）

---

## [2026-04-16] ingest | MGEO — 多模態 GEO：VLM 排名攻擊

**來源**：Multimodal Generative Engine Optimization - Rank Manipulation for Vision-Language Model Rankers.pdf
**作者**：Yixuan Du（Georgetown）、Chenxiao Yu、Haoyan Xu、Yue Zhao（USC）、Ziyi Wang（Maryland）、Xiyang Hu（Arizona State）
**日期**：arXiv:2601.12263v1，2026-01-18

**新增頁面**：
- `multimodal-geo.md`：圖片與媒介對 VLM 排名可見度的影響；跨模態耦合效應；按類別量化數據；實踐意涵（圖文一致性 > 美感優化）
- `vlm-ranking-attack.md`：MGEO 框架技術詳解（PGD 圖片擾動、Soft Prompt 文字 suffix、交替優化機制、正則化消融）

**更新頁面**：
- `geo-cooperative-vs-adversarial.md`：新增 MGEO 多模態對抗性攻擊段落（與 Hijack/Poisoning 的對比）；更新 Sources 和 Last updated；新增相關頁面連結

---

## [2026-04-16] ingest | SAGEO Arena — 全流程 SAGEO 評估環境

**來源**：SAGEO Arena - A Realistic Environment for Evaluating Search-Augmented Generative Engine Optimization.pdf
**作者**：Sunghwan Kim、Wooseok Jeong、Serin Kim、Sangam Lee、Dongha Lee（延世大學 + 建國大學）
**日期**：2026-02-12（arXiv:2602.12187v1）

**建立的頁面**（3 頁）：
1. sageo-arena-benchmark — SAGEO Arena benchmark 設計：170K 文件、9 領域、BM25+重排+生成完整 pipeline、Hit Rate / Rank Change 指標
2. sageo-structural-information — 五類網頁結構欄位（title/meta description/headings/schema/body text）的定義、在 pipeline 各階段的角色分工、引用密度分析
3. stage-aware-sageo — 主要實驗發現（body text 優化損害檢索、結構資訊提升 +22%、重排瓶頸、購物領域全面有害、LLM 骨幹模型影響）+ 階段感知優化方法（4 原則）

**更新的頁面**（4 頁）：
- generative-engine-architecture — 新增「SAGE Pipeline 階段分工」章節，補充結構資訊 vs body text 的互補角色，更新相關頁面連結
- geo-optimization-methods — 新增「SAGEO Arena——現有方法在現實 Pipeline 中失效的警示」章節，補充現實 pipeline 下各策略數據
- autogeo-framework — 新增「AutoGEO 在現實 Pipeline 中的嚴重退步」章節（檢索 ΔRank −22.35，所有策略中最差）
- geo-domain-specific-strategies — 新增「SAGEO Arena 的跨領域發現」章節，詳述購物領域全面有害的機制與建議

**主要發現**：
- 僅優化 body text 導致檢索 H@20 平均下降 9%，AutoGEO 檢索排名大幅惡化（ΔRank −22.35）
- 僅優化結構資訊（title/meta/headings/schema）可提升檢索 H@20 +22%，Citation Rate +2%
- 重排是所有策略的持續瓶頸：5.8% 的目標文件從 Rank-10 降至 Rank-11，剛好掉出生成器輸入
- Shopping 領域是唯一所有優化策略均使 Citation Rate 下降的領域
- Stage-Aware SAGEO（論文提出）是最佳表現：H@20 +28%，ΔRank +4.86，Citation Rate +1.01
- 短關鍵字密集文件利於 BM25 檢索，但 LLM reranker 和生成器更偏好實質回應查詢的內容

---

## [2026-04-16] ingest | IF-GEO — 多查詢衝突感知指令融合優化

**來源**：IF-GEO - Conflict-Aware Instruction Fusion for Multi-Query Generative Engine Optimization.pdf
**作者**：Heyang Zhou、JiaJia Chen、Xiaolu Chen、Jie Bao、Zhen Chen、Yong Liao（中科大 + 合肥綜合國家科學中心）
**日期**：2026-01-20（arXiv:2601.13938v1）

**建立的頁面**（3 頁）：
1. ifgeo-framework — IF-GEO 技術框架（diverge-then-converge 六步流水線、消融研究、成本分析）
2. geo-multi-query-optimization — 多查詢 GEO 問題定義、競爭衝突量化診斷（202 文件實驗）
3. geo-risk-aware-stability-metrics — WCP / DR / WTR 三個風險感知穩定性指標定義與對比數據

**更新的頁面**（5 頁）：
- geo-optimization-methods — 新增「IF-GEO 多查詢衝突融合框架」章節，補充對比數據表，更新相關頁面連結
- citation-performance-metrics — 新增風險感知穩定性指標補充章節（WCP / DR / WTR），更新相關頁面連結
- autogeo-framework — 新增「被 IF-GEO 超越」章節，說明 AutoGEO 在多查詢設定下的根本限制
- geo-dominate-ai-search — 新增「四、IF-GEO 2026」完整章節，更新相關頁面連結
- wiki/index.md — 新增「多查詢優化（IF-GEO, 2026）」子分類，整合 3 個新頁面

**主要發現**：
- 單查詢調優後，69.2% 的非目標查詢出現相對退步，負增益率 30.6%（vs 目標查詢 12.4%）
- IF-GEO 比最強基線 AutoGEO 高出 +45.3%（Obj. Overall：11.03 vs 7.59）
- 穩定性指標全面領先：WCP -0.0090 vs -0.0511，WTR 80.50% vs 73.56%，DR 0.0023 vs 0.0043
- N=5 擴展查詢是最優甜蜜點，WTR 在 N=5 達到峰值（80%）後邊際收益遞減
- 無需引擎特定調整即可泛化到 Gemini-2.0-Flash（IF-GEO 仍最強：14.17 Mean，84.07% WTR）
- 組件分工明確：Conflict Resolution = 安全護欄，Instruction Fusion = 穩定器，Blueprint = 可執行性

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
