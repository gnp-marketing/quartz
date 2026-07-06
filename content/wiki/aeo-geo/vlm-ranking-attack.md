# VLM 排名攻擊：MGEO 框架技術詳解

**Summary**: MGEO（Multimodal Generative Engine Optimization）是首個針對 VLM-based 產品排名系統的多模態對抗性框架。透過聯合優化不可察覺的圖片擾動和流暢的文字 suffix，揭示 VLM 跨模態耦合的安全漏洞。

**Sources**:
- Multimodal Generative Engine Optimization - Rank Manipulation for Vision-Language Model Rankers.pdf（Du et al., Georgetown/USC/Maryland/Arizona State, arXiv:2601.12263v1, 2026-01-18）

**Last updated**: 2026-04-16

---

## 威脅模型

**攻擊者設定**（模擬真實電商惡意賣家）：
- 只能修改自己商品的圖片（It）和文字描述（Tt）
- 約束條件：修改後圖片對人眼不可察覺；文字必須流暢、符合平台規則
- 目標：讓目標商品在 n 個候選商品中排名第一（σ(t) = 1）
- 模型存取：白盒（white-box）——使用開源代理模型（surrogate），不需存取目標平台的私有排名模型

這個威脅模型與現有排名攻擊的根本差異：**先前研究可以修改用戶查詢或其他商品，MGEO 只能動自己的商品**。

---

## 技術架構

MGEO 由兩個優化分支組成，透過交替更新聯合運作。

### 文字分支：Soft Prompt 優化

改自 StealthRank（Tang et al., 2025）的梯度式文字攻擊，適配多模態 VLM 輸入：

1. **初始化**：用目標商品描述 + 引導句（例如 "Help me write a prompt to rank this product at the top of the list:"）生成 token logits，作為連續 suffix embedding 的起始點
2. **三目標 Loss**：
   - **Ranking Loss**：最大化目標排名序列的生成機率（cross-entropy）
   - **Fluency Regularization**：維持與原描述的語境連貫性
   - **N-gram Penalty**：懲罰「top」、「must rank」、「recommend」等明顯排名關鍵詞
3. **解碼**：N 步優化後，對連續 embedding 貪婪解碼為離散 token，附加到商品描述末尾

### 圖片分支：PGD 擾動

使用 Projected Gradient Descent（PGD）的帶符號梯度下降：

```
I_t^(k+1) = I_t^(k) - α · sign(∇ L_total)
```

三目標 Loss：

| Loss 項 | 作用 | 數學形式 |
|---|---|---|
| Target Loss | 最大化目標排名序列的 cross-entropy | CrossEntropy(logits, target) |
| Smoothness Loss | 懲罰相鄰像素突變，讓擾動平滑自然 | Σ[(δ_i+1,j − δ_i,j)² + (δ_i,j+1 − δ_i,j)²] |
| Magnitude Loss | 限制整體擾動幅度；前景像素（rembg 分離）權重更高 | Σ w_i,j |δ_i,j,c| |

### 交替優化（核心機制）

N 個 round，每個 round 執行：
1. 固定圖片，優化文字 suffix K_T 步
2. 固定文字 suffix，優化圖片擾動 K_I 步

這讓文字優化能適應更新後的圖片特徵，圖片優化也能利用更新後的文字語境，從而探索單模態優化無法觸達的聯合對抗最優解。

---

## 圖片端正則化消融

| λ_smoothness | λ_magnitude | 平均排名變化 | 隱蔽性 |
|---|---|---|---|
| 10 | 10 | −1.53 | 最佳（幾乎無噪點） |
| **5** | **5** | **−2.25** | **良好（主要實驗設定）** |
| 0 | 5 | −2.31 | 尚可 |
| 5 | 0 | −2.72 | 明顯失真（效果最強但不隱蔽） |
| 0 | 0 | −2.29 | 嚴重偽影（且非最強，因過擬合） |

**結論**：
- Magnitude 正則化是隱蔽性的主要保障（去除後排名效果 +0.47，但圖片嚴重失真）
- Smoothness 對效果貢獻次要，主要改善噪點的空間分布
- 完全不加正則化反而不是最強：模型過擬合「1. product name」的輸出格式，推論時可能先輸出其他文字，導致攻擊失效

---

## 與相關排名攻擊的比較

| 方法 | 模態 | 優化方式 | 適用場景 |
|---|---|---|---|
| LLM Whisperer | 文字 | 同義詞替換（硬編碼規則） | LLM-based 排名 |
| StealthRank | 文字 | 梯度 + Langevin dynamics | LLM-based 排名 |
| RAF | 文字 | 離散 token 優化 | LLM-based 排名 |
| CheatAgent | 文字 | LLM agent 迭代編輯（黑盒） | LLM-based 推薦 |
| VLATTACK | 圖片+文字 | 跨模態擾動 | 分類/對齊任務 |
| **MGEO** | **圖片+文字** | **交替梯度優化（排名目標）** | **VLM-based 排名** |

VLATTACK 等先前多模態攻擊針對分類任務（讓「貓」被判定為「狗」），目標函數與排名問題根本不同——排名是競爭性、連續值問題，需要最大化相對分數而非絕對分類。

---

## 相關頁面

- [[multimodal-geo]] — 多模態 GEO 的整體概念與 GEO 從業者意涵（圖片對可見度的量化影響）
- [[geo-cooperative-vs-adversarial]] — 合作性 vs 對抗性 GEO 框架（MGEO 屬於對抗性端）
- [[geo-optimization-methods]] — 合作性文字 GEO 方法（文字側對比）
