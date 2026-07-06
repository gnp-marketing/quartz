# Co-Evolving Critic（共同進化代理評估器）

**Summary**: AgenticGEO 的輕量代理模型（Qwen2.5-1.5B），模擬真實生成引擎對策略效果的評分，大幅降低昂貴的真實引擎查詢次數。Critic 與 MAP-Elites Archive 共同進化——Archive 提供真實反饋給 Critic，Critic 的評分引導 Archive 更新方向。

**Sources**: AgenticGEO - A Self-Evolving Agentic System for Generative Engine Optimization.pdf（Yuan et al., Beihang University, 2026）

**Last updated**: 2026-04-16

---

## 核心問題：真實引擎反饋的成本

生成式引擎（GE）評估一個改寫策略的效果，需要：
1. 對文件套用策略（Rewriter 推論）
2. 將改寫後的文件送入真實 GE（Qwen2.5-32B / Llama-3.3-70B）
3. 從 GE 的引用輸出中計算可見度分數

每輪迭代如果對所有候選策略都做真實引擎評估，成本極高。AgenticGEO 每輪只對 8 個候選（Top-4 + Random-4）做真實評估，其餘靠 Critic 代理。

---

## Critic 的雙重職責

### 1. 策略篩選器（Evaluator）

在候選策略送真實引擎前，Critic 先估算每個策略的預期增益：

```
R_critic(s) = C(x, s)
```

- x：文件（查詢 + 文件摘要組合的 context）
- s：策略的 compact summary（由 R_crit 函數渲染）
- 輸出：實數分數，估算套用策略 s 後的可見度增益

Top-K_top（=4）候選送真實引擎，Random K_rand（=4）也送真實引擎（用於探索）。其餘候選只有 Critic 分數，進入 Bpred replay buffer 作為訓練素材。

### 2. 推理規劃器（Planner）

推理時，Critic 分析文件 x 的具體弱點，從 Archive Top-25 策略中選出適合當前文件的改寫策略。這不只是排序，而是**內容感知的策略選擇**：

```
識別文件弱點 → 選擇能針對弱點的策略 → 引導 Rewriter 多步執行
```

---

## Critic 訓練目標：Hybrid Objective

Critic 需要同時學習兩件事：

1. **絕對值**：策略 s 對文件 x 的增益大約是多少分？
2. **相對順序**：策略 s1 和 s2 哪個更好？

單純的 regression（只學絕對值）會忽視相對排序；單純的 pairwise（只學相對排序）無法估算絕對增益。AgenticGEO 使用 Hybrid Objective：

### Regression Loss（絕對值）

```
L_reg = E_(x,s) [(C(x,s) - r_target(x,s))^2]
```

r_target 是從真實引擎或 Btrue/Bpred 取得的目標增益。

### Pairwise Ranking Loss（相對順序）

```
L_pair = E_(x,s+,s-) [w(s+,s-) · log(1 + exp(-(C(x,s+) - C(x,s-))))]
```

其中 w(s+, s-) 是加權係數，對分差大的策略對給更高權重（強調「明顯差距」的學習）。

分兩相採樣：
- **Top-5 密集對**（分差小的鄰近策略）：精煉細粒度局部排序
- **全局對比對**（分差大的遠端策略）：確保粗粒度分離

### 分階段訓練策略

```
Phase 1（凍結骨幹）:
  只更新 value head，防止主幹表示崩潰
  → warmup 收斂後

Phase 2（全參數微調）:
  解凍骨幹，聯合優化全部參數
```

---

## 兩個 Replay Buffer

| Buffer | 來源 | 內容 | 用途 |
|---|---|---|---|
| **Btrue** | 真實引擎評估的結果 | (x, s, R_true(s)) | Critic 重新校準、Evolver 訓練 |
| **Bpred** | Critic 預測的候選 | (x, s, R_mix(s)) | 只用於 Evolver 訓練 |

**設計理念**：
- Critic 只從真實反饋（Btrue）學習，確保校準方向正確，不從自己的預測學習（避免自我強化偏差）
- Evolver 同時從 Btrue 和 Bpred 學習，有更多訓練素材加速進化

---

## Critic 的進化：線上重新校準

每 T 輪迭代後，Btrue 積累更多真實反饋，Critic 用新數據重新校準：

```python
# 每輪結束後：
Btrue ← Btrue ∪ {(x, s, R_true(s)) | s ∈ S_eval}
Critic_{t+1} ← TrainCritic(Critic_t, Btrue)
```

這就是「共同進化」的含義：
- Archive 提供新策略 → 真實引擎評估 → 新反饋進 Btrue
- Btrue 累積 → Critic 校準更準確
- 更準確的 Critic → 更好的候選篩選 → Archive 品質提升

### 泛化界（Lemma A.2）

Critic 的泛化誤差有理論保證：

```
|C_T(s) - R(s)| ≤ O(1/√T)
```

隨 Btrue 數據量增加（T 增大），Critic 預測的誤差以次線性速率收斂至 0。這保證線上共同進化不會讓 Critic 越來越偏離真實。

---

## Critic 的策略表示輸入

Critic 接收的是策略的 **Compact Summary**（R_crit 函數渲染），而非完整改寫 prompt：

```python
R_crit(g) = ⊕_{k ∈ K_active} (Name(k) + ":" + Val(k))

# 範例輸出：
# "Tone:Assertive | Format:List | Constraint:Anti-Hallucination"
```

設計考量：
- 完整改寫 prompt 可能有數百個 token，Critic（1.5B）處理長序列成本高
- Compact Summary 保留所有「離散行為特性」，足以讓 Critic 區分策略優劣
- 減少 token 消耗，讓 Critic 可以快速評估大量候選

---

## Critic 排名品質（NDCG）

在各數據集的離線校準後，Critic 排名品質：

| 數據集 | NDCG@1 | NDCG@3 | NDCG@5 |
|---|---|---|---|
| GEO-Bench | 84.01 | 93.89 | 94.98 |
| MS-MARCO | 77.73 | 81.39 | 82.82 |
| E-Commerce | 68.47 | 73.77 | 78.46 |

**解讀**：
- GEO-Bench 是訓練域，NDCG@1=84.01 表示 84% 的情況下 Critic 能正確選出最優策略
- E-Commerce 是跨域，NDCG@1=68.47，Critic 在陌生域的精準度下降
- NDCG@5 在各域都維持 78+，說明 Critic 對「大致最優」的識別能力是可靠的

---

## 相關頁面

- [[agenticgeo-beihang]] — AgenticGEO 完整框架（Critic 的系統位置）
- [[map-elites-geo]] — MAP-Elites Archive（與 Critic 共同進化的策略庫）
- [[geo-strategy-genotype]] — 策略基因型與 R_crit compact summary 渲染
