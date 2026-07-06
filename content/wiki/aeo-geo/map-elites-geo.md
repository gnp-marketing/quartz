# MAP-Elites 策略 Archive（GEO 應用）

**Summary**: MAP-Elites 是一種「質量-多樣性」進化算法，在 AgenticGEO 中用於維護一個多樣且高效的策略倉庫。相比傳統只保留最佳策略的進化方法，MAP-Elites 同時保留不同「行為特性」的策略，使系統能針對不同文件找到適合的改寫策略。

**Sources**: AgenticGEO - A Self-Evolving Agentic System for Generative Engine Optimization.pdf（Yuan et al., Beihang University, 2026）

**Last updated**: 2026-04-16

---

## 背景：為何需要「質量-多樣性」進化？

傳統進化算法（如遺傳算法）只保留整體最優的個體——對 GEO 而言，這相當於「只保留在平均文件上效果最好的改寫策略」。問題在於，[[agenticgeo-beihang]] 的策略敏感度分析表明：

- 對文件 A 最好的策略可能對文件 B 完全無效
- 需要一個**多樣化的策略庫**，覆蓋不同的結構、風格、推理方式

MAP-Elites（Multi-dimensional Archive of Phenotypic Elites）的設計哲學：
**在每個「行為特性格」中保留最佳策略，而非只保留全局最優。**

---

## MAP-Elites 在 AgenticGEO 中的設計

### 描述符（Behavior Descriptors）

策略的「行為特性」由 12 個離散維度刻畫，分三類：

**Core Types（核心類型，2 維）**
| 維度 | 語意 |
|---|---|
| strategy_type | 策略的整體改寫方式類型 |
| output_schema | 輸出格式的架構型別 |

**Switches（開關，6 維）**
| 維度 | 語意 |
|---|---|
| has_self_check | 是否包含自我驗證步驟 |
| has_reasoning | 是否包含顯式推理鏈 |
| has_conflict_res | 是否包含衝突解決機制 |
| use_code_block | 是否使用程式碼塊格式 |
| has_prelude | 是否有引言段落設計 |
| has_post_check | 是否有後處理驗證 |

**Buckets（分桶，4 維）**
| 維度 | 語意 |
|---|---|
| tone_bucket | 語調類別（如 Assertive / Technical / Simple） |
| constraint_strength | 約束強度等級 |
| length_policy | 長度控制策略 |
| reasoning_steps_bucket | 推理步數分桶 |

### Archive 結構

每個唯一的 12 維描述符組合對應一個**cell**（格）。每個 cell 最多保留 K_c = 3 個策略。

```
Archive M = {
  cell(ψ(g1)): [策略 s1, s2, s3],  // 3 個該格最優策略
  cell(ψ(g2)): [策略 s4, s5],      // 此格只有 2 個
  ...
}
```

---

## 策略入 Archive 的雙重門檻（Value-Novelty Gate）

新候選策略 s 要進入 Archive，必須通過兩個條件之一：

### 1. Value Gate（性能門檻）

```python
if 目標 cell 未滿（< K_c 個策略）:
    直接接納
elif 新策略分數 > cell 內最差策略的分數:
    替換最差者
else:
    拒絕
```

### 2. Novelty Gate（去重門檻）

計算候選策略 s 與 cell 內現有所有策略 e 的結構相似度：

```
Sim(s, e) = |n-grams(s) ∩ n-grams(e)| / |n-grams(s) ∪ n-grams(e)|
```

若 max Sim(s, e) > 0.9，說明候選策略與現有策略過於相似，**直接拒絕**（即使分數更高）。

**設計理念**：性能最優的策略如果結構重複，不僅佔用 Archive 容量，也會讓 Evolver 在下一輪生成高度相似的後代，陷入局部最優。多樣性是長期進化的前提。

---

## PND Score：Archive 的維護評分

Archive 中保留的策略按 PND Score（Pareto-Novelty-Diversity）排序：

```
S_PND(s) = r(s) + λ_pnd × (Nov(s) + Div(s))
```

- **r(s)**：策略 s 的可見度增益（由 Critic 或真實引擎提供）
- **Nov(s)**：相對於 Archive 中所有策略的結構新穎度（基於 n-gram Jaccard）
- **Div(s)**：相對於同 cell 內其他策略的多樣度
- **λ_pnd = 0.3**：多樣性權重

**推理時的用途**：從進化後的 Archive 選 Top-25 策略時，也按 PND Score 排序——兼顧效果與多樣性，確保 Critic 有足夠多樣的候選策略可評估。

---

## 進化機制：如何產生新策略

每輪迭代中，新策略通過兩種方式從 Archive 中的父代生成：

### 神經突變（Neural Mutation）—— Evolver

Evolver（Qwen2.5-7B-Instruct）讀取父代策略的基因型 JSON，生成語意相關但結構不同的後代：

```
prompt:
  "你是 GEO 的策略進化代理。選擇一個 operator_id，
   產生一個結構化基因型 JSON（I/C/R/F/T）作為後代。"

輸入：
  - 查詢 q
  - 文件摘要 content_summary
  - 父代基因型 A（JSON）
  - [可選] 父代基因型 B（用於交叉操作 cx_*）
  - 操作符目錄 operator_catalog

約束：
  - 如選交叉操作（cx_*），必須同時提供父代 A 和 B
  - 優先選擇多樣化的操作符，避免重複
```

Evolver 以 Sibling-aware AWR（Advantage-Weighted Regression）訓練，確保進化方向是「相對好的突變」而非隨機。

### 符號擾動（Symbolic Perturbation）

對父代基因型的各維度做規則式修改（直接改欄位值），產生確定性的變體，不依賴 Evolver 推論。

---

## 與推理時選策略的關係

Archive 在推理階段同樣發揮核心作用：

1. 用進化後的 Archive（而非固定策略池）提供多樣候選
2. Critic 對文件 x 評估每個候選策略的預期增益
3. 按 Critic 分數排序後，Rewriter 執行 Top-1 策略
4. 若改寫後分數不理想，從剩餘策略繼續選，直到 3 步上限

這是 Archive 帶來的核心優勢：**策略庫覆蓋不同文件類型和改寫風格**，推理時比固定策略池有更高概率找到適合當前文件的策略。

---

## 相關頁面

- [[agenticgeo-beihang]] — AgenticGEO 完整框架概述
- [[geo-strategy-genotype]] — 策略基因型的 5 維表示（Archive 中每個策略的結構）
- [[co-evolving-critic]] — 與 Archive 共同進化的 Critic 代理評估器
