# GEO 策略基因型（Strategy Genotype）

**Summary**: AgenticGEO 將每個 GEO 改寫策略表示為結構化的 5 維基因型（Instruction / Constraints / Reasoning / Format / Tone），可被進化、突變、交叉組合。這個表示法讓策略具備可比較性、可進化性，並支援 MAP-Elites Archive 的行為分類。

**Sources**: AgenticGEO - A Self-Evolving Agentic System for Generative Engine Optimization.pdf（Yuan et al., Beihang University, 2026）

**Last updated**: 2026-04-16

---

## 設計動機

現有 GEO 方法的策略形態是**自然語言 prompt**（如「添加權威引用」、「增加統計數字」）。這類表示：
- 不可比較：無法量化兩個策略的差異
- 不可進化：無法系統性地生成變體
- 難以歸類：不知道兩個策略是否在同一「行為空間」

AgenticGEO 將策略形式化為**基因型（genotype）**：一個有明確語意維度的結構化 JSON，同時保留可讀性（可以渲染為自然語言 prompt）。

---

## 5 維基因型結構

```python
g = ⟨g_I, g_C, g_R, g_F, g_T⟩
```

| 維度 | 代號 | 語意 | 範例值 |
|---|---|---|---|
| Instruction | g_I | 目標與範圍定義：受衆、核心事實、強調重點、專家角色 | "目標：使內容對一般讀者更具說服力，強調數量化事實" |
| Constraints | g_C | 嚴格邊界：字數限制、引用查核、防幻覺規則、事實一致性 | "嚴格限制：不得新增任何在原文中未出現的數字或聲明" |
| Reasoning | g_R | 邏輯步驟：衝突解決、自我校正、分步規劃、邏輯驗證 | "步驟一：識別核心論點；步驟二：尋找可量化補充；步驟三：驗證事實一致性" |
| Format | g_F | 輸出格式：bullet lists、程式碼塊、輸出 schema、段落引言 | "使用 bullet point 列舉；每要點不超過 2 句" |
| Tone | g_T | 寫作風格：語氣主張強度、技術性、簡潔程度、正式度 | "語調：肯定且具備領域專業感（Assertive + Technical）" |

---

## 兩種渲染函數

同一基因型根據使用場景渲染為不同格式：

### R_crit：給 Critic 的緊湊摘要

目的：最小化 token 消耗，保留行為特性標籤。

```python
R_crit(g) = ⊕_{k ∈ K_active} (Name(k) + ":" + Val(k))
```

只保留非空的離散欄位（如 tone 標籤、format 類型）：

```
"Tone:Assertive | Format:List | Constraint:Anti-Hallucination"
```

Critic（1.5B 模型）用這個緊湊表示評估策略對文件的預期增益，不需要看完整改寫 prompt。

### R_eng：給 Rewriter 的完整改寫 prompt

目的：生成可執行的指令，供 Rewriter（32B 模型）真正執行改寫。

```python
R_eng(g) = 模板填充函數，包含：
  - 完整 Instruction 指令
  - 所有 Constraints 條件
  - 完整 Reasoning 步驟
  - Format 格式規範
  - Tone 風格要求
  + 文件摘要佔位符 {summary}
  + 輸出格式要求
```

---

## 基因型的可進化性

### 突變操作（Mutation）

Evolver（7B 模型）可以對任一維度做語意上的修改：

**Instruction 突變**：改變受衆定義或強調重點
- 原始：「針對一般讀者」→ 突變：「針對領域專家」
- 原始：「強調實用性」→ 突變：「強調學術嚴謹性」

**Tone 突變**：改變語氣風格
- Assertive → Simple（降低正式度）
- Technical → Conversational（改變受衆層次）

**Format 突變**：改變輸出格式
- 段落文字 → Bullet List
- 一般文字 → 引用格式（加 blockquote）

**Reasoning 突變**：增加或刪除推理步驟
- 移除 self-check 步驟（更快但可能不一致）
- 新增 conflict resolution 步驟（更穩健）

### 交叉操作（Crossover，cx_ 開頭的 operator）

兩個父代策略可以結合：

```
父代 A：Tone:Assertive + Format:List + Constraint:StrictFact
父代 B：Instruction:ExpertRole + Reasoning:MultiStep + Tone:Technical
      ↓ 交叉操作 cx_combine
後代：Tone:Assertive + Format:List + Instruction:ExpertRole + Reasoning:MultiStep
```

Evolver 的 prompt 要求：選 cx_* 操作符時必須同時提供父代 A 和 B；若只有父代 A，禁止選擇交叉操作符。

---

## 策略的 MAP-Elites 分類

基因型通過描述符函數 ψ 映射到 MAP-Elites cell：

```
ψ(g) → 12 維離散向量 → cell index
```

12 維來源：

| 類別 | 維度 | 與基因型的關係 |
|---|---|---|
| Core Types | strategy_type, output_schema | 由 g_I 和 g_F 決定 |
| Switches | has_self_check, has_reasoning, has_conflict_res, use_code_block, has_prelude, has_post_check | 各對應 g_R, g_F 的具體欄位 |
| Buckets | tone_bucket, constraint_strength, length_policy, reasoning_steps_bucket | 由 g_T, g_C, g_F, g_R 的強度/數量決定 |

**範例**：
- g_T = "Assertive" → tone_bucket = "assertive"
- g_R 包含 3 個步驟 → reasoning_steps_bucket = "3+"
- g_F = Bullet List → output_schema = "list"
- g_R 有 has_self_check → has_self_check = True

---

## 與 9 種種子策略的關係

AgenticGEO 的初始 Archive 由 9 種種子策略（GEO-Bench 原始策略）表示為基因型初始化：

| 種子策略 | 主要基因型特徵 |
|---|---|
| Keyword Stuffing | g_I: 關鍵詞嵌入，Tone: neutral |
| Unique Words | g_I: 稀有詞彙，Tone: formal |
| Easy-To-Understand | g_T: simple, g_F: plain paragraph |
| Authoritative | g_T: assertive, g_I: expert role |
| Technical Words | g_T: technical, g_I: domain jargon |
| Fluency Optimization | g_C: no new info, g_R: grammar check |
| Cite Sources | g_I: credibility, g_R: citation check |
| Quotation Addition | g_I: authority quotes, g_C: no invented quotes |
| Statistics Addition | g_I: numerical facts, g_C: verifiable only |

這 9 種策略在進化後，會生成出涵蓋更多 cell 的多樣後代策略。

---

## 相關頁面

- [[agenticgeo-beihang]] — AgenticGEO 完整框架
- [[map-elites-geo]] — MAP-Elites Archive（基因型決定 cell 分類）
- [[co-evolving-critic]] — Critic 接收 R_crit 渲染的緊湊摘要作為輸入
- [[geo-optimization-methods]] — 9 種種子策略（基因型的初始化來源）
