# Caption Injection：第一個多模態 G-SEO 方法

**Summary**: Caption Injection 從圖片提取描述性文字並注入原文，是首個針對多模態生成式搜尋引擎的合作性優化方法。在多模態 GSE 場景下，其主觀可見度提升（+1.12%）顯著超越所有純文字基線。

**Sources**:
- Caption Injection for Optimization in Generative Search Engine.pdf（Xiaoluchen, Jie Bao, Haojie Wu, Zhen Chen, Yong Liao；arXiv:2511.04080v2，2026-03-18）

**Last updated**: 2026-04-16

---

## 問題背景

現代生成式搜尋引擎（GSE）已逐步採用多模態 RAG（MRAG）架構，能同時處理文字與圖片並生成更豐富的回應。然而，現有的 G-SEO 方法（如流暢度優化、引語添加、統計添加）都只針對文字進行改寫，無法利用圖片中蘊含的視覺語意，在多模態環境中的優化潛力受到限制。

Caption Injection 提出一個簡單直接的解法：**把圖片的語意「翻譯」成自然語言，注入文字內容**，讓 GSE 的 LLM 在生成回應時能讀到原本只存在於圖片中的資訊（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

---

## 核心設計原則

與內容改寫型 G-SEO 方法不同，Caption Injection 視圖片語意為**補充信號**，而非改寫主體：

> 圖片語意通常是局部且稀疏的，無法獨立驅動優化，因此我們只用它來增強與視覺內容相關的文字片段，而非改寫整段文字。

這意味著：
- 原文結構完整保留
- Caption 只在語義相關的位置注入
- 不因為加入視覺描述而破壞文字的可讀性

---

## 三階段流程（Generate–Refine–Inject）

整個流程通過 prompt engineering 實作，分三步完成（source: Caption Injection for Optimization in Generative Search Engine.pdf）：

### 階段一：Structural Generation（結構性描述生成）

用視覺語言模型（VLM，實作上使用 Qwen-2.5-VL-7B）從圖片提取結構化語意：

$$C_s = S(I) = \{o, a, s\}$$

- **o**（Object）：圖片中的主要物體
- **a**（Action）：物體正在執行的動作
- **s**（Scene）：場景環境

這個 **O-A-S 三元組**構成視覺理解的基礎語意單位，生成客觀、無主觀情感的結構性描述（structural caption）。

**Prompt 關鍵指令**：
```
Generate a concise and objective caption for this image,
describing the main objects, actions, and scene present.
Do not include any subjective emotions, opinions, or speculation.
```

### 階段二：Alignment Refinement（對齊精煉）

以文字內容 T 為語境，對結構性描述 $C_s$ 進行精煉：

$$C_r = R(T, C_s)$$

精煉規則：
1. 比對 caption 與文字中的相關細節
2. 補全 Who / What / When / Where / How 等要素——文字中有的取文字版本（文字優先），缺的保留 caption 原版
3. 展開與 caption 最相關的關鍵資訊，保持邏輯連貫
4. 精煉後字數為原始 caption 的 50%–150%

精煉後的 $C_r$ 自然對齊文字語境，語意更完整，避免語意漂移（semantic drift）。

### 階段三：Semantic Injection（語義注入）

LLM 自動判斷 $C_r$ 的最佳插入位置，無縫嵌入文字：

$$T' = J(T, C_r)$$

- J 為上下文感知的語義注入操作
- LLM 依據語義依賴關係自動選點
- 規定：不刪除或修改其他任何文字；只在選定位置插入

---

## 實驗結果

### 主要成果（MRAMG benchmark，4,800 個查詢-內容對，6 個領域）

以下為各方法在主觀可見度上的平均相對改善百分比（source: Caption Injection for Optimization in Generative Search Engine.pdf）：

| 方法 | 單模態 GSE | 多模態 GSE |
|---|---|---|
| 傳統 SEO（關鍵詞） | -2.30% | -0.66% |
| 流暢度優化 | **-0.37%**（最佳） | +0.71% |
| 引語添加 | -1.12% | -0.08% |
| 統計添加 | -4.13% | -2.12% |
| **Caption Injection** | -1.01%（第二） | **+1.12%（最佳）** |

**關鍵詮釋**：
- 單模態場景中，所有方法的提升都很有限（甚至負值）；流暢度優化在純文字場景略優
- 多模態場景才是 Caption Injection 的主場——比次優方法（流暢度優化 +0.71%）高出 **+0.41%**

### 適應性分析

Caption Injection 在多模態場景的有效優化率（effective optimization rate）比單模態場景高出 **5.75%**，是所有方法中提升幅度最大的，體現其跨域適應性（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

### 最大改善維度：唯一性（Uniqueness）

Caption Injection 在「唯一性」維度的增益遠超其他維度：

| 指標維度 | 流暢度優化（多模態增量） | Caption Injection（多模態增量） |
|---|---|---|
| 相關性 | +0.89% | +1.30% |
| 影響力 | +0.93% | +2.17% |
| 多樣性 | +1.34% | +2.53% |
| **唯一性** | **+1.58%** | **+3.50%（最高）** |
| 點擊追蹤概率 | +0.96% | +1.80% |
| 主觀位置 | +0.77% | +1.74% |
| 主觀量 | +1.03% | +2.61% |
| **整體平均** | **+1.07%** | **+2.13%** |

視覺語意透過提供其他純文字來源無法提供的差異化資訊，顯著提升內容的「唯一性」感知（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

---

## 消融研究

### Refined Caption vs. Structural Caption

精煉後的 caption（Refined）優於原始結構性 caption（Structural）：

| Caption 類型 | 單模態（平均） | 多模態（平均） |
|---|---|---|
| Structural Caption（single-shot） | -1.15% | +0.71% |
| **Refined Caption（single-shot）** | **-1.01%** | **+1.12%** |

精煉步驟確實有效：細粒度改寫讓 caption 更聚焦於關鍵片段，幫助 LLM 捕捉視覺語意。

### Single-shot vs. Multi-shot 注入

單次注入（single-shot）vs. 多次注入（multi-shot）的效果差異揭示一個重要洞察：

| 注入方式 | 單模態 | 多模態 |
|---|---|---|
| Single-shot | -1.01% | +1.12% |
| Multi-shot | -0.63% | +0.01% |

- **單模態**：多次注入有益，重複強化讓 LLM 更好理解淺層概念
- **多模態**：多次注入幾乎無效——LLM 開始過度關注視覺語意，反而降低對文字細節的注意力

**實踐指引**：在多模態 GSE 中，**只做一次 caption 注入**（source: Caption Injection for Optimization in Generative Search Engine.pdf）。

---

## 局限性

（source: Caption Injection for Optimization in Generative Search Engine.pdf）

1. **語意融合仍淺層**：目前的視覺-文字整合停留在 caption 嵌入層，未做深層跨模態特徵融合
2. **長文弱化問題**：長文（如技術手冊，平均 6,365 字元）中，所有方法表現均差——Caption 的語意被稀釋，難以定位優化核心
3. **GSE 偏好黑盒**：不同 GSE 對文字 vs. 圖片 caption 的內部偏好不同，優化效果難以精確預測
4. **跨模型泛化待驗**：實驗使用 GLM-4-9B 和 Qwen-2.5-VL-7B，其他模型組合的效果尚未系統探討

---

## 對 GEO 實踐者的意涵

1. **在網頁圖片添加語意對齊的 alt text / caption**：不是通用的「圖片描述」，而是明確連結圖片語意與文字主旨的描述
2. **caption 要與文字語境對齊**：先閱讀鄰近段落，再寫圖片描述，讓兩者語意呼應
3. **避免在多模態場景重複注入**：一張圖片的語意只在最相關的位置注入一次
4. **長文需考慮信息密度**：長篇技術文章的圖片語意效益最低，應優先提升流暢度

---

## 相關頁面

- [[mrag-gseo]] — 多模態 RAG 的 G-SEO 挑戰與 MRAMG benchmark 介紹
- [[multimodal-geo]] — VLM-based 排名中圖片的可見度影響（排名攻擊視角）
- [[geo-optimization-methods]] — 純文字 G-SEO 方法全覽（Caption Injection 的對比基線）
- [[geo-cooperative-vs-adversarial]] — 合作性 vs 對抗性 GEO 策略框架
- [[geo-visibility-metrics]] — G-EVAL 主觀可見度七維度評估體系
