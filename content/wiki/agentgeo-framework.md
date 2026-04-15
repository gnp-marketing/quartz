# AgentGEO 診斷修復框架

**Summary**: Virginia Tech 提出的 GEO 代理框架，針對每個未被引用的頁面診斷根本失敗原因，從工具庫中選取針對性修復，迭代直到獲得引用。相較通用重寫方法，只修改 5% 的內容即可達到超過 40% 的引用率相對提升。

**Sources**: Diagnosing and Repairing Citation Failures in Generative Engine Optimization.pdf（Tian et al., Virginia Tech + Zhejiang University, arXiv:2603.09296v1, 2026-03-11）

**Last updated**: 2026-04-15

---

## 核心理念：從「什麼能幫助？」到「為什麼失敗？」

現有 GEO 方法的思維是：「通用的最佳實踐可能有幫助，套用它。」AgentGEO 的思維是：「這個頁面具體為什麼沒被引用？針對這個原因修復。」

這個視角轉換帶來三個實際優勢：

1. **精準**：只修改 5% 的內容，相比 AutoGEO 的 25% 修改量
2. **避免副作用**：通用規則在健康類等領域反而使引用率下降，診斷式優化則一致有效
3. **可解釋**：每次修復都有明確的失敗原因記錄，便於審計

---

## 問題定式

給定一個目標頁面 `d` 和一組相關查詢的分佈 `Q`，以及對應的生成式引擎 `G`，目標是找到最優化的頁面 `d*`，使其被引用的期望值最大化，同時保持與原始頁面的相似度：

```
d* = argmax E[V(q, d', G)]  s.t.  Sim(d, d') ≥ τ
```

其中 `V(q, d, G) ∈ {0, 1}` 是二元引用指標。實際操作中，用訓練查詢集 `Qtr` 優化，在保留查詢集 `Qte` 上評估。

---

## 三階段流水線

### 階段一：Diagnose-then-Repair 迭代循環

針對每個訓練查詢 `q`，執行以下迭代：

**Step 1：確認未被引用**
模擬 GE 流水線，若目標頁面已被引用則跳過。

**Step 2：失敗診斷（Failure Diagnosis）**
將目標頁面 `d` 與排名最高的被引用競爭者比較，識別為何競爭者被引用而目標頁面沒有。根據 [[citation-failure-taxonomy]] 的四類十二種模式分類漏洞。

**Step 3：帶記憶的工具選擇（Tool Selection with Memory）**
根據診斷出的失敗類型，從工具庫中選擇最匹配的修復工具。**查詢級記憶**（Memory `Mq`）追蹤本輪之前的所有嘗試，避免重複使用已失敗的工具。

**Step 4：修改與重測（Modify & Retest）**
在目標頁面的臨時副本上應用工具，重新測試是否被引用。
- 若被引用：提取修改摘要（修改了哪些位置、如何修改），進入批量聚合
- 若仍未引用：更新記憶，重新診斷，繼續迭代

循環直至達到引用或達到最大迭代次數。

---

### 階段二：跨查詢批量聚合

目的：防止針對單一查詢過擬合，生成**查詢無關的**（query-invariant）修改建議。

**流程**：
1. 將訓練查詢分成批次（batch size = 5 時效果最佳）
2. 每個批次中，每個查詢的修復在**獨立的臨時副本**上執行
3. 批次結束後，將各查詢的候選修改對映到共同的 chunk 索引空間
4. 對每個 chunk，選擇診斷嚴重程度最高且置信度最高的修改
5. 一次性將所選修改套用到共享頁面

**衝突解決策略**（多個查詢針對同一 chunk 提出不同修改）：
- 預設：按失敗嚴重程度 + 置信分數排序，取最高
- 替代方案：chunk 內啟發式投票，或 LLM 仲裁

---

### 階段三：Chunk 級局部編輯

目的：在保持頁面語義完整性的同時應用修改。

**原理**：LLM 處理長文檔時會出現品質退化（「Lost in the Middle」效應），直接重寫整頁反而有害。chunk 級編輯把每次修改限制在最相關的 HTML 結構單元內。

**執行方式**：
1. 用 HTML 標籤將頁面劃分為結構化 chunks（段落、列表、元資料塊）
2. 每個修改建議指定目標 chunk 索引
3. 只修改指定 chunks，其餘內容和整體 HTML 結構保持不變

---

## 工具庫（9 種修復工具）

工具按功能分為四類，每類對應不同的失敗模式：

### 類別 I：Information Augmentation（資訊增強）

#### `entity_injection`（實體注入）
**目標失敗**：Contextual Gap（缺少特定實體/術語）
**機制**：在語義最相關的位置「微量插入」缺失事實，用 `<strong>` 標籤語義高亮，不刪除現有內容。

#### `data_serialization`（資料序列化）
**目標失敗**：Unstructured Layout（資料呈現無結構）
**機制**：將敘述型資料（「A 花費 10 元，B 花費 20 元」）轉換為 HTML `<table>`，保留含有細節的文字部分。

---

### 類別 II：Structural Enhancement（結構強化）

#### `structure_optimization`（結構優化）
**目標失敗**：Unstructured Layout（密集純文字塊）
**機制**：插入 `<h3>/<h4>` 分節標題；將列舉型句子轉換為 `<ul>/<ol>`；對關鍵實體加 `<strong>/<em>`。**嚴格禁止改變文字內容，只加 HTML 標籤。**

#### `noise_isolation`（雜訊隔離）
**目標失敗**：Low Signal-to-Noise（雜訊過多）
**機制**：將導覽連結、Cookie 警告、廣告、頁尾等用 `<aside>`, `<nav>`, `<footer class="geo-noise">` 包裹；高價值內容用 `<article>` 標記。**不刪除雜訊**（頁面 UI 仍需要它們），只讓解析器能過濾。

---

### 類別 III：Content Positioning（內容定位）

#### `bluf_optimization`（底線先行）
**目標失敗**：Excessive Verbosity（關鍵事實被稀釋）/ Buried Answer
**機制**：實作「Bottom Line Up Front」原則，在頁面頂部生成 1-2 句直接事實摘要，包在 `geo-summary-box` div 中。

#### `content_relocation`（內容上移）
**目標失敗**：Window Truncation（答案超出 context window）
**機制**：在頁面頂部建立「TL;DR」或「Key Findings」摘要區塊，將深層的關鍵事實合成為 2-4 個 bullet points。風格根據頁面類型匹配（學術用「Summary」，部落格用「TL;DR」，電商用「Quick Facts」）。**保留所有原始內容不刪除。**

#### `intent_realignment`（意圖重對齊）
**目標失敗**：Intent Divergence（用戶目標不匹配）
**機制**：改寫開頭段落，第一句直接回應查詢意圖；不相關的細節移至段落末尾（不刪除）。

**範例**：
```
原始：In today's rapidly changing world, environmental concerns are paramount...
優化後：Reducing your carbon footprint can be achieved through three primary strategies: 
        transportation changes, energy efficiency, and consumption habits...
```

---

### 類別 IV：Persuasive Refinement（說服性精煉）

#### `persuasive_rewriting`（說服性改寫）
**目標失敗**：Trust Credibility（可信度不足）/ Information Scarcity（內容太淺薄）
**策略選項**：權威語氣、反駁論點、情感鉤、社會認同、稀缺性/緊迫性、邏輯結構

#### `historical_redteam`（歷史紅隊）
**目標失敗**：Outdated Information（內容過時）
**策略**：將過時資訊重新框架為「理解當前狀況所必需的歷史脈絡」，用時間線框架連接過去和現在。包含四種子策略：Timeline Framing、Implicit Prompt、Comparative Analysis、Completeness Emphasis、Knowledge Anchoring。

---

## 記憶驅動的策略管理

為確保收斂、防止循環，系統使用查詢級別的記憶軌跡 `Mq = {(v_k, ω_k)}` 執行三個限制：

1. **冪等性保護**（Idempotency Guard）：若工具 `ω` 在之前的迭代中已產生相同漏洞，則從候選集中排除
2. **預算上限**（Budget Threshold）：每個工具在同一查詢中最多呼叫 3 次
3. **穩定性修剪**（Stability Pruning）：若工具在連續兩個優化週期都失敗，從當前路徑中完全排除

**升級協議**（當初始工具失敗後自動升級）：
- 事實增強失敗 → 轉向說服性改寫
- 標準重組失敗 → 強制使用 BLUF 摘要
- 反覆截斷警告 → 用激進參數執行 `noise_isolation`

---

## 實驗結果

### 主要性能對比（GPT 引擎，In-context generation）

| 方法 | 引用率（CR） | 平均內容修改量 |
|---|---|---|
| Vanilla（無優化） | 56.58% | — |
| 最強單一方法（Easy-to-Understand） | 71.11% | ~25% |
| AutoGEO | 68.80% | **25%** |
| **AgentGEO** | **79.52%** | **5%** |

AgentGEO 以 **5% 的修改量**達到了所有方法中最高的引用率，比 AutoGEO 高出 10.72 個百分點。

### Claude 引擎對比

| 方法 | CR | 精確率 | 召回率 | 清晰度 | 洞察力 |
|---|---|---|---|---|---|
| Vanilla | 42.40% | 89.42 | 96.16 | 73.99 | 57.47 |
| AutoGEO | 46.80% | 88.79 | 95.51 | 75.74 | 61.05 |
| **AgentGEO** | **54.80%** | **90.33** | **96.02** | 74.99 | 60.76 |

**重要發現**：Claude 在所有方法下的答案品質指標（精確率、清晰度、洞察力）都優於 GPT，但引用率提升對 Claude 的反應更慢。可能原因是 Claude 對被優化內容的理解和引用有不同的機制。（source: Tian et al., 2026）

### 按內容長度的效果

AgentGEO 在所有內容長度上都能提升引用率，效果在「Very Long」（> 10k 字）頁面最顯著——這與 Chunk 級編輯特別適合長頁面的直覺一致。

### 按主題的效果

顯著提升的主題：藝術與娛樂、歷史、政治
效果有限的主題：健康（原始引用率已高達 81%，優化空間小）

---

## 關鍵限制

### 訓練查詢規模的影響

引用率隨訓練查詢數量單調增加，在 80 個訓練查詢時達到 17.65% 的相對提升，標準差也同時下降（0.2050 → 0.1625）。

### 不可修復的失敗

即使 AgentGEO 在訓練查詢上將引用率從 57.0% 提升到 83.7%，仍有 163 個查詢無法被修復。對這些案例的分析顯示，優化確實修改了頁面，但引擎仍偏向高權威競爭者（Coursera、edX 等），說明問題出在「領域層面的引擎內部偏見」，超出了頁面內容的控制範圍。

詳見 [[citation-failure-taxonomy]] 中的 Systemic Exclusion 類別和 [[geo-cooperative-vs-adversarial]] 的延伸討論。

---

## 相關頁面

- [[citation-failure-taxonomy]] — AgentGEO 診斷所基於的四類十二種失敗分類法
- [[geo-citation-vs-contribution]] — 為什麼 AgentGEO 聚焦引用率而非貢獻度
- [[mimiq-benchmark]] — AgentGEO 使用的文件中心式評測基準
- [[geo-optimization-methods]] — 9 種修復工具的詳細說明
- [[autogeo-framework]] — CMU 提出的通用規則自動提煉框架（對比參照）
- [[big-brand-bias]] — Competitive Redundancy 的品牌偏見背景
- [[citation-performance-metrics]] — 引用率（CR）指標的定義
