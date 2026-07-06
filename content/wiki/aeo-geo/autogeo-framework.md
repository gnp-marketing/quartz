# AutoGEO 框架

**Summary**: CMU 提出的自動化 GEO 框架，用 LLM 從數萬筆生成式引擎行為觀察中提煉偏好規則，再以規則驅動文件改寫，包含 AutoGEO_API（即插即用）和 AutoGEO_Mini（強化學習訓練的輕量模型）兩個實作。

**Sources**: What Generative Search Engines Like and How to Optimize Web Content Cooperatively.pdf（Wu et al., CMU, arXiv:2510.11438v1, 2025-10-13）

**Last updated**: 2026-04-16

---

## 背景與動機

現有 [[geo-optimization-methods]] 中的 9 種手工規則方法（引語添加、統計添加等）依賴人工設計啟發式規則，缺乏對生成式引擎底層偏好的系統性理解，也無法自動適應不同引擎和領域的差異。

AutoGEO 的核心主張：
- GE 偏好規則可以從行為觀察中**自動提煉**，不需要人工猜測
- 不同 LLM 引擎和不同內容領域有**不同的偏好規則**，通用規則次優
- GEO 應該是**合作性的**：提升可見度同時不損害引擎回應品質

---

## 框架概覽

AutoGEO 分兩大模組：

```
【模組一：偏好規則提煉】
生成式引擎行為觀察
    ↓ Explainer（比較高/低能見度文件對）
    ↓ Extractor（蒸餾結構化 insight）
    ↓ Merger（層級合併，處理數萬筆 insight）
    ↓ Filter（去除 query 相依規則，保留通用原則）
    → 規則集 S

【模組二：規則導向改寫】
規則集 S
    ├── AutoGEO_API（嵌入 prompt，呼叫強力 LLM）
    └── AutoGEO_Mini（規則作 RL 獎勵，訓練 Qwen3-1.7B）
```

---

## 模組一：偏好規則提煉

### 可見度計分公式

給定查詢 q，生成式引擎從文件庫取回候選集 D_q，生成回答 a。每份文件 d 的可見度：

```
Vis(d, a) = Word(d, a) + Pos(d, a) + Overall(d, a)
```

- **Word**：回答中引用 d 的句子佔總字數比例
- **Pos**：位置加權字數（前段引用權重更高，指數遞減）
- **Overall**：Word + Pos 的綜合分數

對每個查詢，選取可見度差異最大的文件對 (d_i, d_j) 作為分析素材。

### 四個提煉元件

#### Explainer
- **輸入**：查詢 q、文件對 (d_i, d_j)、生成回答 a、獲勝文件標記
- **輸出**：自然語言比較分析，解釋引擎為何偏好其中一篇
- **考量面向**：直接性、完整性、相關性、結構、準確度、簡潔性
- **LLM**：gemini-2.5-flash-lite

#### Extractor
- **輸入**：Explainer 的比較分析
- **輸出**：結構化 JSON insight 列表（可複用的通用原則）
- **範例產出**：「文件應在開頭直接陳述關鍵結論」
- **LLM**：gemini-2.5-flash-lite

#### Merger（層級合併策略）
- **問題**：數萬筆 insight 超過 LLM 輸入上限
- **解法**：分塊合併 → 遞迴層級整合，直到規模可管理
- 每塊 ≤ 12,000 tokens；合併後再合併，直到收斂
- **輸出**：去重、語義聚合後的候選規則集
- **LLM**：gemini-2.5-pro

#### Filter
- **目的**：移除與特定 query 綁定的規則，只保留領域通用原則
- **邏輯**：
  - 整條規則若完全圍繞 query → 整條刪除
  - 規則部分含 query 參照 → 刪除該部分，保留通用部分
- **範例**：「文件應提供與 query 相關的事實」→ 「文件應提供具體事實」
- **LLM**：gemini-2.5-pro

---

## 模組二：規則導向改寫模型

### AutoGEO_API（即插即用）

將規則集 S 嵌入 prompt，呼叫強力 LLM 改寫文件：

```
Here is the source: <Target Document>

You are given a website document as a source. This source, along
with other sources, will be used by a language model (LLM) to
generate answers to user questions... Your task is to rewrite your
document to maximize its visibility and impact...

## Quality Guidelines to Follow:
<Rule Set S>
```

- 無需額外訓練，可直接跨引擎使用
- 使用 Gemini-2.5-Pro 作為改寫 LLM

### AutoGEO_Mini（強化學習輕量模型）

成本僅為 AutoGEO_API 的 **~0.0071x**，可在 CPU 離線推論。

#### 階段一：Cold Start（監督微調）
1. 用 AutoGEO_API 作為教師，生成改寫後的文件對
2. 篩選條件：改寫後三項 GEO 指標均嚴格大於原始文件 + KPR > 0.8 且 KPC = 0
3. 從 Researchy-GEO 訓練集 10,000 篇中取得 4,976 對高品質樣本
4. 用這些樣本對 Qwen3-1.7B 做全參數微調（5 個 epoch，學習率 5×10⁻⁵）

#### 階段二：GRPO 強化學習
對每份目標文件，從當前策略抽樣 8 個改寫候選，計算三種獎勵：

| 獎勵 | 計算方式 | 目的 |
|---|---|---|
| **Outcome reward** R_out | 三項 GEO 指標之和 | 提升可見度 |
| **Rule reward** R_rule | 規則遵循比例（gpt-4o-mini 驗證） | 遵守偏好規則 |
| **Semantic reward** R_sem | KPR + KPC（gpt-4o-mini 抽關鍵點） | 保留語義忠實度 |

最終獎勵：R = R̃_out + R̃_rule + R̃_sem（各成分 z-score 正規化後相加）

使用 GRPO（Group Relative Policy Optimization）優化，clip 範圍 ε=0.2，KL 係數 β=0.02。

---

## 實驗結果

### 與既有方法比較（Gemini 引擎）

| 方法 | E-commerce Overall | GEO-Bench Overall | Researchy-GEO Overall |
|---|---|---|---|
| Vanilla（無 GEO） | 18.32 | 19.44 | 20.18 |
| Fluency Optimization（最強基準線） | 22.99 | 23.73 | 27.75 |
| **AutoGEO_API** | **34.05** | **34.92** | **43.76** |
| **AutoGEO_Mini** | **25.25** | **27.12** | **38.53** |

AutoGEO_API 比最強基準線最高提升 **+50.99%**；平均提升 **+35.99%**。

### 跨引擎穩定性

在 Gemini、GPT、Claude 三種引擎上均一致提升，證明框架不依賴單一引擎。

### 低能見度文件的改善

對最難改善的低能見度文件（Vanilla Overall = 9.46），AutoGEO_API 仍可提升至 35.83，而最強基準線只到 16.78。

---

## 成本比較

| 模型 | 每小時/API 費用 | 相對成本 |
|---|---|---|
| AutoGEO_API（Gemini-2.5-Pro） | $1.25/M input + $10/M output tokens | 1x（基準） |
| AutoGEO_Mini（Qwen3-1.7B on A6000 Ada） | ~$0.75/hr GPU | **~0.0071x** |

AutoGEO_Mini 可在 CPU 離線推論，API 方法受 throughput 限制。

---

## 與對抗性方法的差異

見 [[geo-cooperative-vs-adversarial]] 的詳細比較。

---

---

## 🆕 2026 年更新：SAGEO Arena——AutoGEO 在現實 Pipeline 中的嚴重退步

SAGEO Arena（Kim et al., 延世大學，2026-02-12）首次在含完整 pipeline（BM25 檢索 → 重排 → 生成）的環境中評估 AutoGEO，結果是所有測試策略中**表現最差**的：

| 指標 | AutoGEO（Body Text Only） | 其他策略均值 |
|------|------------------------|------------|
| 檢索 ΔRank | **−22.35** | −4.54 |
| 檢索 H@20 | 0.37（**−36%**） | 0.53（−9%） |
| 重排 H@10 | 0.58（**−42%**） | 0.84（−16%） |
| 生成 Citation Rate | 0.39（**−22%**） | 0.47（−6%） |

**AutoGEO 退步嚴重的原因**：

AutoGEO 的偏好規則傾向**大幅擴展文件內容**，生成冗長的改寫。這導致：
1. 關鍵字密度被稀釋——文件詞彙偏離原始查詢語言
2. BM25 的長度正規化懲罰長文件
3. 大量引入與查詢無直接關係的詞彙，削弱詞彙重疊

**重要脈絡**：AutoGEO 的 benchmark（AutoGEO 論文原始評估）使用**固定候選集**，不包含檢索階段——在那個設定下 AutoGEO 表現優異（Overall +79.6%）。SAGEO Arena 揭示，這個表現無法遷移到現實的全 pipeline 環境。

**結論**：AutoGEO 的規則提煉針對生成階段優化，但在現實部署中，文件必須先通過檢索這一關。在現實環境中，AutoGEO 的重寫策略是**反效果**的。

詳見 [[sageo-arena-benchmark]] 和 [[stage-aware-sageo]]。

---

## 🆕 2026 年更新：被 IF-GEO 超越

IF-GEO（Zhou et al., USTC, 2026-01-20）以 AutoGEO 作為最強基線，並在多查詢評估設定下大幅超越：

| 方法 | Obj. Overall | WCP | WTR | DR |
|------|-------------|-----|-----|----|
| **AutoGEO** | 7.59 | -0.0511 | 73.56% | 0.0043 |
| **IF-GEO** | **11.03** | **-0.0090** | **80.50%** | **0.0023** |

**AutoGEO 的根本限制**：AutoGEO 從排名信號提煉**全域偏好規則**，再對所有文件套用相同規則集。這在單查詢設定下表現優異，但無法處理「不同查詢對同一段落要求互斥修改」的多查詢衝突問題。IF-GEO 明確協調跨查詢衝突，在穩定性指標（WCP、DR、WTR）上全面領先。

詳見 [[ifgeo-framework]] 和 [[geo-multi-query-optimization]]。

---

---

## 🆕 2026 年更新：被 AgenticGEO 超越

AgenticGEO（Yuan et al., Beihang University, 2026）以 AutoGEO 作為最強基準，在 in-domain 和 cross-domain 設定下全面超越：

| 數據集 | AutoGEO Overall | AgenticGEO Overall | 提升 |
|---|---|---|---|
| GEO-Bench（in-domain） | 23.71 | **25.48** | +7.5% |
| MS-MARCO（cross-domain） | 30.67 | **34.10** | +11.2% |
| E-Commerce（cross-domain） | 21.18 | **26.58** | +25.5% |

**AutoGEO 在 AgenticGEO 中被診斷的根本限制**：

AutoGEO 蒸餾出**固定的偏好規則**，再對所有文件套用相同規則集。這在單一域的 in-domain 設定表現尚可，但面臨：
1. **Instance heterogeneity**：規則適合某類文件，但對其他文件無效（近 50% 樣本用任何靜態策略都效果差）
2. **策略不進化**：規則一旦蒸餾完成，無法根據新文件或引擎行為變化更新
3. **跨域泛化差**：E-Commerce 比 GEO-Bench 更難：AutoGEO 的偏好規則來自訓練域，跨域時策略不再適用

AgenticGEO 的解法是維護一個**可進化的策略 Archive**（MAP-Elites），搭配 Co-Evolving Critic 降低引擎查詢成本，同時保持高語義一致性（不依賴激進改寫）。

詳見 [[agenticgeo-beihang]]、[[map-elites-geo]]。

---

## 相關頁面

- [[geo-preference-rules]] — 框架提煉出的完整規則集與跨引擎分析
- [[geo-cooperative-vs-adversarial]] — 合作性 GEO vs 對抗性攻擊
- [[geo-optimization-methods]] — 既有 9 種手工規則方法（AutoGEO 的基準線）
- [[geo-visibility-metrics]] — GEO 指標體系詳解
- [[geo-dominate-ai-search]] — GEO 學術研究背景
- [[ifgeo-framework]] — IF-GEO 多查詢框架（超越 AutoGEO）
- [[geo-multi-query-optimization]] — 多查詢競爭衝突問題（AutoGEO 的盲點）
- [[sageo-arena-benchmark]] — SAGEO Arena：AutoGEO 在現實 pipeline 中表現最差的評估
- [[stage-aware-sageo]] — 階段感知優化（現實環境中比 AutoGEO 更佳的策略）
- [[agenticgeo-beihang]] — Beihang AgenticGEO：進化策略 Archive（全面超越 AutoGEO）
