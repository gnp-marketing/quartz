# AgenticGEO：自我進化代理框架

**Summary**: Beihang University（北京航空航天大學）提出的自我進化 GEO 框架。核心主張：GEO 是 instance-dependent 的，靜態策略對近半數文件無效。AgenticGEO 以 MAP-Elites 進化策略 Archive 搭配 Co-Evolving Critic 代理評估器，在跨域測試中平均比最強基準（AutoGEO）提升 46.4%，且高度保留語義一致性。

**Sources**: AgenticGEO - A Self-Evolving Agentic System for Generative Engine Optimization.pdf（Yuan et al., Beihang University, 2026）

**Last updated**: 2026-04-16

---

## 核心問題：靜態策略的根本限制

AgenticGEO 的出發點是一個關鍵觀察（論文 Figure 1）：**GEO 是 instance-dependent 的**。

對 GEO-Bench 的 9 種既有策略（Keyword Stuffing、Citations、Quotations…）做策略敏感度分析：

- **x 軸**：同一文件在 9 種策略間的可見度**方差**（策略敏感度）
- **y 軸**：9 種策略中能達到的**最大增益**

結果分成四個象限：

| 類型 | 意義 |
|---|---|
| Robustly Optimizable（左上） | 高增益、低敏感度：任何策略都有效——這類最少 |
| Strategy-Dependent（右上） | 高增益、高敏感度：必須選對策略才有效 |
| Optimization-Resistant（左下） | 低增益、低敏感度：策略效果差但穩定——無從改善 |
| Low-Yield & Volatile（右下） | 低增益、高敏感度：效果差且不穩定——最難處理 |

**核心發現**：近一半的樣本落在「無論選哪種靜態策略效果都差」的區域。這說明靜態策略池本身就是問題，需要**進化**而非只是選擇。

---

## 框架概覽：三階段架構

AgenticGEO 由三個元件協作：**Evolver**（策略生成）、**Critic**（代理評估）、**Rewriter**（內容改寫）。

```
┌─────────────────────────────────────────────────────────────────┐
│  Stage 1: 離線 Critic 對齊（Offline Critic Alignment）           │
│  用 9 種種子策略 × 訓練集文件的真實引擎反饋，warm-start Critic      │
└──────────────────────────────┬──────────────────────────────────┘
                               ↓
┌─────────────────────────────────────────────────────────────────┐
│  Stage 2: 線上策略-Critic 共同進化（Online Co-Evolution）         │
│                                                                  │
│  for t = 1..T (100 輪):                                         │
│    1. Evolver 生成候選策略（神經突變 + 符號擾動）                   │
│    2. Critic 預篩選（低成本評分）→ 選 Top-4 + Random-4            │
│    3. 真實引擎評估（高成本，只對 8 個候選）                         │
│    4. Value-Novelty Gate → 更新 MAP-Elites Archive               │
│    5. 更新 Evolver（用 Btrue + Bpred）                           │
│    6. 重新校準 Critic（只用 Btrue）                               │
└──────────────────────────────┬──────────────────────────────────┘
                               ↓
┌─────────────────────────────────────────────────────────────────┐
│  Stage 3: 推理（Inference）                                      │
│  從進化 Archive 選 Top-25 策略 → Critic 引導多輪改寫（最多 3 步）   │
│  Tabu List 防止重複策略                                           │
└─────────────────────────────────────────────────────────────────┘
```

### 模型組成

| 元件 | 模型 | 職責 |
|---|---|---|
| Evolver | Qwen2.5-7B-Instruct | 從父代策略生成新候選策略 |
| Critic | Qwen2.5-1.5B | 估算策略對特定文件的增益 |
| Rewriter | Qwen2.5-32B-Instruct | 執行實際的內容改寫 |
| GE（評估引擎） | Qwen2.5-32B-Instruct / Llama-3.3-70B-Instruct | 真實引擎反饋 |

---

## Stage 1：離線 Critic 對齊

### 目的

在進入昂貴的線上互動之前，先讓 Critic 學會「哪種策略對哪種文件比較好」。

### 流程

1. 對訓練集的每份文件 x，分別用 9 種種子策略改寫，送入真實引擎取得分數
2. 計算每個策略的**監督增益**：
   ```
   r_sup(x, s) = Score(A_s^train) - Score(A_0^train)
   ```
   即：使用策略 s 的引擎輸出分數 − 不改寫的基準分數
3. 以 Hybrid Objective 訓練 Critic：
   - **Regression loss**：學習絕對分數值（捕捉策略的絕對優劣）
   - **Pairwise ranking loss**：學習相對排序（捕捉策略間的相對順序）
4. 訓練分兩相：先凍結骨幹網路，warmup value head；再解凍全部參數聯合微調

離線 Critic 的排名品質（NDCG）：
- GEO-Bench：NDCG@1 = 84.01，NDCG@5 = 94.98
- MS-MARCO：NDCG@5 = 82.82
- E-Commerce：NDCG@5 = 78.46

---

## Stage 2：線上共同進化

線上階段是 AgenticGEO 的核心：策略 Archive 和 Critic **同時更新**，彼此強化。

### 每輪迭代（Algorithm 1）

**Phase 1：混合候選生成**
- 從 Archive 抽樣父代策略集 P
- Evolver 進行**神經突變**：基於父代，生成語意相關但結構不同的新策略
- 同時做**符號擾動**：規則式修改父代的各維度欄位
- 合併得候選池 S_cand

**Phase 2：Critic 預篩**
- Critic 對所有候選評分：R_critic(s) = C(x, s)
- 選 Top-K_top（=4）+ Random K_rand（=4）送真實引擎
- 其餘候選只有 Critic 分數，不消耗真實引擎配額

**Phase 3：真實引擎評估**
- 只有被選中的 8 個候選送真實引擎（GE evaluation）
- 計算混合獎勵：
  ```
  R_mix(s) = R_true(s)   若 s 被真實引擎評估過
             R_critic(s)  其餘
  ```

**Phase 4：Archive 更新（Value-Novelty Gate）**
- 新策略進 Archive 需通過雙重門檻：
  1. **Value**：在其 MAP-Elites cell 中，分數高於現有最差者
  2. **Novelty**：n-gram Jaccard 相似度 < 0.9（結構足夠不同）
- Archive 維護使用 **PND Score**（Pareto-Novelty-Diversity）：
  ```
  S_PND(s) = r(s) + λ_pnd × (Nov(s) + Div(s))
  ```
  λ_pnd = 0.3，平衡效果與探索多樣性

**Phase 4：Evolver + Critic 更新**
- Evolver 用 Btrue（真實反饋）+ Bpred（Critic 預測）一起訓練
- Critic 只用 Btrue 重新校準，保持與真實引擎對齊

### 收斂保證

- Critic 泛化誤差：O(1/√T)——隨資料量增加，Critic 預測越準確
- 整體遺憾界（Regret Bound）：O(√T)——搜尋效率的次線性遺憾

---

## Stage 3：推理時的多輪改寫

推理時 AgenticGEO 扮演規劃代理（planner agent）：

1. 從進化後的 Archive 選 Top-25 策略（按 PND Score）
2. Critic 分析文件 x 的內容弱點，選出最適合的策略
3. Rewriter 執行改寫（第 1 步）
4. 重新評估：若分數提升則繼續，否則選下一策略
5. 最多 3 步改寫，**Tabu List** 記錄已用策略，防止重複

這個設計讓 AgenticGEO 在推理時仍能「適應性地選策略」，而非盲目套用。

---

## 實驗結果

### 實作細節

- LoRA 微調（rank=16，α=32，dropout=0.05），2 epochs
- 4 × NVIDIA RTX Pro 6000 GPU
- 線上迭代 T=100，每輪 K_top=4 + K_rand=4

### In-Domain 結果（GEO-Bench）

| 方法 | Qwen 引擎 Overall | Llama 引擎 Overall |
|---|---|---|
| 無優化 | 20.21 | 19.20 |
| Keyword Stuffing | 20.69 | 20.02 |
| Quotation Addition（靜態最強） | 23.76 | 21.57 |
| AutoGEO | 23.71 | 22.78 |
| **AgenticGEO** | **25.48** | **24.52** |
| **提升幅度** | **+26.08%（vs Vanilla）** | **+27.71%（vs Vanilla）** |

### Cross-Domain 結果

| 數據集 | AgenticGEO（Qwen） | AutoGEO（Qwen） | 提升 |
|---|---|---|---|
| MS-MARCO | 34.10 | 30.67 | **+11.2%** |
| E-Commerce | 26.58 | 21.18 | **+25.5%** |

跨域遷移能力是 AgenticGEO 的顯著優勢：Archive 中的多樣策略在未見過的域上仍能找到有效策略。

### 消融研究

移除哪個元件損失最大：

1. **移除進化 Archive**（固定初始 9 種策略）：損失最大
   - 說明「長期策略積累」是最關鍵的驅動力
2. **離線 Critic 沒有線上繼續更新**：次大損失
3. **不用 Critic 引導改寫**（隨機選策略）：中等損失
4. **Archive 不保留多樣性**（只按分數保留）：較小損失，但仍顯著

### 語義一致性

使用 BERTScore-F1（roberta-large）測量改寫前後的語義相似度：

- AgenticGEO 達到最高整體分數，且維持高語義相似度
- **不依賴激進改寫**：改寫是「精準的、針對弱點的」，而非整頁重寫

---

## 與其他方法的定位比較

| 特性 | 靜態 GEO | AutoGEO | AgentGEO（Virginia Tech） | **AgenticGEO（Beihang）** |
|---|---|---|---|---|
| 策略形態 | 固定 9 種 prompt | 蒸餾固定規則 | 固定 9 種診斷工具 | **進化 Archive** |
| 適應文件 | 否 | 域/引擎特化 | 否（工具固定） | **內容感知** |
| 引擎適應 | 否 | 有限 | 否 | **動態更新** |
| 真實引擎查詢次數 | 高 | 高 | 高 | **大幅減少（靠 Critic 代理）** |
| 策略是否可進化 | 否 | 否 | 否 | **是（MAP-Elites）** |

---

## 關鍵限制

1. **訓練成本**：線上共同進化需要 T=100 輪，每輪仍需真實引擎評估 8 個候選
2. **Critic 泛化邊界**：E-Commerce 域的 NDCG@1 只有 68.47，低於 GEO-Bench（84.01），說明 Critic 在陌生域的精準度下降
3. **Archive 維護容量**：每 cell K_c=3，過小可能限制策略多樣性
4. **最大改寫步數**：推理時限制 3 步，對非常困難的文件可能不足

---

## 相關頁面

- [[map-elites-geo]] — MAP-Elites Archive 機制詳解（質量多樣性進化）
- [[co-evolving-critic]] — Co-Evolving Critic 設計、訓練目標與泛化界
- [[geo-strategy-genotype]] — 5 維基因型策略表示與 12 行為描述符
- [[agentgeo-framework]] — Virginia Tech AgentGEO：診斷修復框架（完全不同方法）
- [[autogeo-framework]] — CMU AutoGEO：規則蒸餾框架（AgenticGEO 的最強基準）
- [[geo-optimization-methods]] — 9 種種子策略（AgenticGEO 的離線初始化來源）
- [[geo-cooperative-vs-adversarial]] — 合作性 GEO 的整體框架
- [[geo-visibility-metrics]] — 優化目標指標（Word / Pos / Overall）
