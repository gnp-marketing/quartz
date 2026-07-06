# IF-GEO 框架

**Summary**: 中科大提出的多查詢 GEO 框架，以「先發散、後收斂」的兩階段流水線協調跨查詢的衝突修改指令，生成統一的全域修訂藍圖，在提升平均可見度的同時最大化跨查詢穩定性。

**Sources**: IF-GEO - Conflict-Aware Instruction Fusion for Multi-Query Generative Engine Optimization.pdf（Zhou et al., USTC, arXiv:2601.13938v1, 2026-01-20）

**Last updated**: 2026-04-16

---

## 背景與動機

現有 GEO 方法（9 種手工規則、[[autogeo-framework]]、[[agentgeo-framework]]）都把優化視為**單一查詢問題**——針對某個查詢改寫文件。但在現實中，一份文件同時服務數十個乃至數百個不同查詢。這帶來一個根本矛盾：

> 針對查詢 A 的最佳修改，往往與針對查詢 B 的最佳修改相互衝突或競爭同一段落空間。

IF-GEO 用診斷實驗量化了這個問題：對 202 份文件、808 個（目標查詢，非目標查詢）配對進行單查詢調優實驗：

| 類別 | 平均增益 | 負增益率 | Downside Magnitude |
|------|---------|---------|-------------------|
| 目標查詢（優化對象） | +0.277 | 12.4% | 0.017 |
| 非目標查詢 | +0.087 | **30.6%** | 0.036 |
| 相對溢出（非目標 − 目標） | −0.189 | **69.2%** | 0.228 |

**結論**：針對單一查詢調優後，幾乎 70% 的非目標查詢在相對表現上出現退步，且退步幅度通常不小。這就是 IF-GEO 要解決的「多查詢競爭衝突」問題。

詳見 [[geo-multi-query-optimization]]。

---

## 框架概覽：diverge-then-converge

```
原始文件 D
    │
    ▼
【Phase I：發散】
    ├─ Step 1：Query Mining → 加權查詢集 Q(D) = {(q_i, w_i)}
    └─ Step 2：Query-specific Request Generation → 分散請求池 R = {r_{i,j}}
    │
    ▼
【Phase II：收斂】
    ├─ Step 3：Prioritization & Deduplication → 精簡候選集
    ├─ Step 4：Conflict Resolution → 無衝突指令集
    ├─ Step 5：Blueprint Construction → Global Revision Blueprint（JSON）
    └─ Step 6：Blueprint-Guided Revision → 優化文件 D'
```

所有步驟均透過 LLM 呼叫實現，模型扮演「結構化執行器」，輸出中間結構化產物而非自由改寫。

---

## Phase I：查詢發現與請求生成

### Step 1：Query Mining（查詢挖掘）

**核心概念**：反向檢索（reverse retrieval）——不是給查詢找文件，而是給文件找查詢。

LLM 被指示扮演「搜尋日誌分析師」，從文件主題推算出真實使用者可能輸入的多樣查詢，嚴格禁止簡單改寫。

輸出：加權查詢集
```
Q(D) = {(q_i, w_i)} — w_i ∈ [0, 100]，代表查詢普及度
```

預設 N=5 個查詢（最優甜蜜點，詳見消融研究）。

**查詢示例**（文件主題：coagulopathy）：
- "what is coagulopathy and its symptoms"（p=0.90）
- "causes and treatment options for coagulopathy"（p=0.85）
- "difference between coagulopathy and clotting disorders"（p=0.75）

### Step 2：Query-specific Request Generation（查詢特定請求生成）

針對每個查詢 q_i，LLM 分析文件的具體不足，生成結構化修改請求：

```
r_{i,j} = ⟨e_{i,j}, u_{i,j}, s_{i,j}⟩
```

- **e_{i,j}**：段落錨點（excerpt）——定位目標文字
- **u_{i,j}**：具體修改建議（revision suggestion）
- **s_{i,j}**：必要性評分（necessity score）∈ [0, 100]

所有查詢的請求匯總為**分散請求池** R = {r_{i,j}}，此時仍可能存在大量重複和衝突。

---

## Phase II：衝突感知指令融合

### Step 3：Prioritization & Deduplication（優先化與去重）

**全域優先分**（global priority score）：
```
g_{i,j} = w_i × s_{i,j}
```
- w_i：查詢普及度（查詢層面）
- s_{i,j}：必要性評分（指令層面）

**流程**：
1. 過濾 g_{i,j} 低於門檻 τ=0.7 的低優先請求
2. 語意去重：針對相同錨點、相似目標的請求合併為一個 meta-request，繼承最高必要性分
3. 為每個合併請求加上簡短主題標籤（topic tag）

### Step 4：Conflict Resolution（衝突解決）

識別針對同一內容的互斥指令（如「刪除該段」vs「展開該段」）：

- **Selection（選擇）**：若兩指令優先分差距明顯 → 保留高分、丟棄低分
- **Synthesis（合成）**：若兩指令優先分相近（g_i ≈ g_j）→ 生成折衷指令，同時滿足兩個查詢的合理需求

此步驟依賴模型的語意推理能力，比硬性規則更靈活。

### Step 5：Blueprint Construction（藍圖建構）

把衝突解決後的指令集映射到文件段落結構（Intro → Body → Conclusion），輸出 JSON 全域修訂藍圖：

```json
{
  "revision_blueprint": [
    {
      "section_name": "Introduction",
      "target_location": "...",
      "modification_intent": "...",
      "directives": [
        "Integrate instruction #1: ...",
        "Integrate instruction #3: ..."
      ],
      "format_note": "Keep as text paragraphs."
    }
  ]
}
```

**藍圖的作用**：將跨查詢的優化目標整合為一份一致的執行契約，防止各查詢修改互相干擾。

### Step 6：Blueprint-Guided Revision（藍圖導向修訂）

模型作為「受約束的編輯者」：
- 嚴格按藍圖指令逐段修改
- **未被提及的段落一字不動保留**
- 禁止自由發揮或無意識的內容漂移

---

## 風險感知最佳化目標

IF-GEO 的正式目標不只是最大化平均增益 E[Δv]，而是同時最佳化三個穩定性維度：

- **最大化** WCP（Worst-Case Performance）
- **最大化** WTR（Win-Tie Rate）
- **最小化** DR（Downside Risk）

三個指標的定義詳見 [[geo-risk-aware-stability-metrics]]。

---

## 實驗結果

### 整體可見度（GPT-4o-mini 引擎，1,000 個查詢）

| 方法 | Obj. Overall | Subj. Average |
|------|-------------|--------------|
| Cite Sources（最強啟發式） | 4.71 | 3.31 |
| Auto-GEO（最強基線） | 7.59 | 5.30 |
| RAID（意圖導向） | 0.88 | 1.36 |
| **IF-GEO** | **11.03** | **5.87** |

IF-GEO 比最強基線（AutoGEO）客觀指標高出 **+45.3%**。

### 跨查詢穩定性（風險指標）

| 方法 | WCP↑ | WTR↑ | DR↓ |
|------|------|------|-----|
| Auto-GEO | -0.0511 | 73.56% | 0.0043 |
| **IF-GEO** | **-0.0090** | **80.50%** | **0.0023** |

### Gemini-2.0-Flash 跨引擎泛化

| 方法 | Mean | WCP | WTR | DR |
|------|------|-----|-----|----|
| Auto-GEO | 12.99 | -0.0578 | 78.91% | 0.0083 |
| **IF-GEO** | **14.17** | **-0.0435** | **84.07%** | **0.0054** |

無需任何引擎特定調整，直接泛化到不同 GE。

### 初始排名分層分析

IF-GEO 在所有排名層（Rank 1–5）均保持穩健提升，不是「只對原本高排名文件有效」：

| 排名層 | Obj. Mean | WCP | WTR |
|--------|----------|-----|-----|
| Rank 1 | 13.49 | 0.0084 | 77.92% |
| Rank 4 | 12.24 | 0.0196 | 87.14% |
| Rank 5 | 12.14 | -0.0154 | 81.43% |

---

## 消融研究：組件分工

| 移除組件 | Mean↓ | 主要影響 |
|---------|-------|--------|
| 無（完整） | 9.24 | — |
| 移除 Blueprint Construction | 8.18 | 增益可執行性下降，穩定性不變 |
| 移除 Instruction Fusion | 7.07 | WTR 下降最多（主要穩定器）|
| 移除 Conflict Resolution | 6.14 | WCP 最差、DR 最高（安全護欄）|

**核心發現**：
- Conflict Resolution + Instruction Fusion = 穩定性保障
- Blueprint Construction = 增益可執行性

---

## 成本分析

IF-GEO 的多階段流程比單次基線消耗更多 token：

| 階段 | 平均 Token 消耗/文件 |
|------|---------------------|
| Query Mining | 1,270.6 |
| Edit Request Generation | 1,749.8 |
| Instruction Fusion | 4,487.6（主要成本）|
| Blueprint-Guided Revision | 2,819.8 |
| **總計** | **10,327.7** |

對比單次基線（2,200–2,800 tokens），IF-GEO 成本約為 4–5 倍。Instruction Fusion 是最主要的成本中心，因為它需要跨指令的聯合推理（去重、優先化、仲裁）。

---

## N 值（擴展查詢數）選擇

| N | Obj. Mean | WTR | DR |
|---|----------|-----|----|
| 1 | 8.06 | 低 | 高 |
| 3 | — | — | — |
| **5** | **~9.5** | **80.0%（峰值）** | 低 |
| 7 | — | 略降 | 略降 |
| 9 | 10.02 | 波動 | 邊際改善 |

N=5 是「穩定性收益/計算成本」的最優甜蜜點。

---

## 與其他框架的定位關係

| 框架 | 優化對象 | 核心機制 | 跨查詢穩定性 |
|------|---------|---------|------------|
| GEO（KDD '24） | 單查詢 | 手工啟發式規則 | 不處理 |
| AutoGEO | 單查詢（通用規則） | 自動偏好提煉 + 改寫 | 不處理 |
| AgentGEO | 單查詢（診斷修復） | 失敗分類 + 針對性工具 | 不處理 |
| RAID | 單聚合意圖 | 多角色意圖反射 | 部分（單意圖） |
| **IF-GEO** | **多查詢集** | **發散-收斂衝突融合** | **核心目標** |

---

## 限制

1. **推論成本高**：多階段 LLM 呼叫，token 消耗約為單次基線的 4–5 倍
2. **模擬環境差距**：實驗使用 GPT-4o-mini 模擬生成式引擎，可能不完全反映商業引擎行為
3. **依賴查詢挖掘品質**：全域修訂藍圖的質量取決於 Query Mining 是否準確捕捉真實查詢分佈；若初始擴展不足，後續優化可能偏離方向

---

## 相關頁面

- [[geo-multi-query-optimization]] — 多查詢 GEO 問題定義與競爭衝突分析
- [[geo-risk-aware-stability-metrics]] — WCP / DR / WTR 三個風險感知穩定性指標
- [[autogeo-framework]] — AutoGEO（最強基線，被 IF-GEO 超越）
- [[agentgeo-framework]] — AgentGEO（診斷式修復，同樣基於診斷思路）
- [[geo-optimization-methods]] — 9 種基礎 GEO 方法（IF-GEO 的對比基準）
- [[citation-performance-metrics]] — 可見度指標與穩定性指標
- [[geo-dominate-ai-search]] — GEO 學術研究總覽
