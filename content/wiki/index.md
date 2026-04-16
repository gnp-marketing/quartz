# Wiki 目錄

**Summary**: LLM Wiki 的中央索引，涵蓋硬體、搜尋優化和生成式引擎優化主題。

**Last updated**: 2026-04-16

> 本次更新（2026-04-16）新增「自我進化框架（AgenticGEO, 2026）」子分類，包含 4 個新頁面（agenticgeo-beihang、map-elites-geo、co-evolving-critic、geo-strategy-genotype）；並更新 agentgeo-framework、autogeo-framework、geo-optimization-methods。

---

## 搜尋與 AI 優化

### 生成式引擎優化（GEO）

#### 基礎框架

- [[geo-sfe-framework]] — 結構特徵工程用於 GEO 的完整框架和研究背景
- [[geo-dominate-ai-search]] — GEO 學術研究綜覽：KDD '24 奠基論文 + 多倫多大學 2025 年大規模比較研究
- [[geo-visibility-metrics]] — GE 可見度指標系統：字數佔比、位置調整字數、主觀印象 7 維度（KDD '24）

#### AI 搜尋引擎行為

- [[ai-search-earned-media-bias]] — AI 引擎系統性偏向 Earned Media 的核心發現與數據
- [[ai-search-engine-comparison]] — ChatGPT、Claude、Perplexity、Gemini 行為特性比較
- [[ai-query-taxonomy]] — 12 種 AI 查詢類型和 14 種購物子類別
- [[big-brand-bias]] — AI 搜尋中的大品牌偏見與利基品牌突破策略
- [[geo-language-strategy]] — 多語言 GEO 策略：本地化權威而非翻譯內容

#### 優化方向：結構優化

- [[macro-structure-optimization]] — 文檔層級結構優化指南（標題、導航、交叉參考）
- [[meso-structure-optimization]] — 段落層級結構優化指南（段落長度、格式、密度）
- [[micro-structure-optimization]] — 句子層級結構優化指南（強調、關鍵詞位置、句式）

#### 優化方向：內容策略

- [[geo-optimization-methods]] — 9 種具體的內容優化方法和應用指南
- [[geo-content-strategy-vs-structure]] — 內容策略優化 vs 結構優化的對比與協同

#### 自動化框架（AutoGEO, 2025）

- [[autogeo-framework]] — CMU AutoGEO 框架：用 LLM 自動提煉 GE 偏好規則，驅動文件改寫（API + RL 雙模式）
- [[geo-preference-rules]] — 系統性提煉的 GE 偏好規則集（跨引擎共同規則 + 領域/引擎獨特規則）
- [[geo-cooperative-vs-adversarial]] — 合作性 GEO vs 對抗性攻擊（GEU 指標、Hijack/Poisoning Attack 分析）

#### 自我進化框架（AgenticGEO, 2026）

- [[agenticgeo-beihang]] — Beihang AgenticGEO：MAP-Elites 進化策略 Archive + Co-Evolving Critic，跨域平均 +46.4%，超越 AutoGEO
- [[map-elites-geo]] — MAP-Elites 質量-多樣性進化 Archive：Value-Novelty Gate、PND Score、12 維行為描述符
- [[co-evolving-critic]] — Co-Evolving Critic（Qwen2.5-1.5B）：代理評估器設計、Hybrid Objective、泛化界 O(1/√T)
- [[geo-strategy-genotype]] — 5 維基因型策略表示（I/C/R/F/T）、兩種渲染函數、突變與交叉操作

#### 診斷式優化（AgentGEO, 2026）

- [[citation-failure-taxonomy]] — 首個引用失敗分類法：四大類十二種模式，橫跨 Fetching/Parsing/Generation 流水線（949 案例分析）
- [[agentgeo-framework]] — Virginia Tech AgentGEO：診斷-修復迭代代理，9 種工具，+40% 引用率，只改 5% 內容
- [[geo-citation-vs-contribution]] — 引用率（CR，二元）vs 貢獻度（Word Count，連續）的根本差異
- [[mimiq-benchmark]] — 文件中心式評測基準：204 文件 × 60 查詢，支援 HTML 評估和訓練/測試分割

#### 全流程評估框架（SAGEO Arena, 2026）

- [[sageo-arena-benchmark]] — SAGEO Arena：170K 文件、9 領域、完整 pipeline 的首個階段級 SAGEO benchmark
- [[sageo-structural-information]] — 五類網頁結構欄位（title/meta/headings/schema/body）在 SAGE pipeline 各階段的角色
- [[stage-aware-sageo]] — 主要實驗發現：body text 優化損害檢索；結構資訊是關鍵；階段感知優化方法

#### 多模態 GEO（MGEO, 2026）

- [[multimodal-geo]] — 圖片與媒介對 VLM 排名可見度的量化影響、跨模態耦合效應、實踐意涵
- [[vlm-ranking-attack]] — MGEO 框架技術詳解：PGD 圖片擾動 + Soft Prompt 文字 suffix 交替優化

#### 多模態合作性 G-SEO（Caption Injection, 2026）

- [[caption-injection-gseo]] — 第一個多模態 G-SEO 方法：三階段流程（O-A-S→精煉→注入）、實驗結果、實踐指南
- [[mrag-gseo]] — 多模態 RAG 架構對 G-SEO 的挑戰、MRAMG benchmark（4,800 查詢，6 領域）

#### 多查詢優化（IF-GEO, 2026）

- [[ifgeo-framework]] — USTC IF-GEO：diverge-then-converge 衝突融合框架，Obj. Overall 11.03，WTR 80.5%
- [[geo-multi-query-optimization]] — 多查詢 GEO 問題定義：跨查詢競爭衝突診斷與量化分析
- [[geo-risk-aware-stability-metrics]] — WCP / DR / WTR：三個風險感知穩定性指標，超越均值評估

#### 實踐指南

- [[geo-domain-specific-strategies]] — 按領域、查詢類型和排名位置的優化策略
- [[generative-engine-architecture]] — 生成式引擎的三種架構類型及各自的結構偏好
- [[citation-performance-metrics]] — 測量和追蹤引用性能的指標和方法
- [[geo-optimization-results]] — 實驗結果、性能數據和層級貢獻度分析

---

## 硬體

（待補充）
