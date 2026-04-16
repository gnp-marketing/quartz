# 多模態 GEO：圖片與媒介對 VLM 排名可見度的影響

**Summary**: VLM（Vision-Language Model）已成為電商等現代搜尋系統的核心排名引擎。本頁探討圖片與多媒體元素如何影響 VLM 排名可見度，與純文字 GEO 的本質差異，以及對 GEO 從業者的實踐意涵。

**Sources**:
- Multimodal Generative Engine Optimization - Rank Manipulation for Vision-Language Model Rankers.pdf（Du et al., Georgetown/USC/Maryland/Arizona State, arXiv:2601.12263v1, 2026-01-18）

**Last updated**: 2026-04-16

---

## 搜尋引擎的多模態轉向

現代電商搜尋（如 Amazon 商品排名）已大量採用 VLM 作為排名引擎。VLM 將圖片與文字同時投影到統一語義空間，實現跨模態的相關性判斷。

這代表一個根本性的 GEO 範式轉變：

| 面向 | LLM-based 搜尋（舊） | VLM-based 搜尋（新） |
|---|---|---|
| 排名訊號 | 純文字 | 圖片 + 文字聯合 |
| 優化對象 | 文字描述、結構、關鍵字 | 圖片語義、文字、兩者的跨模態一致性 |
| 現有文字 GEO 效果 | 完全適用 | 效果被稀釋（圖片訊號分走影響力） |

---

## 圖片對排名可見度的獨立貢獻

MGEO 論文在 Qwen2.5-VL-7B 上的實驗（10 個商品類別，每類 10–15 商品）提供最直接的量化證據（source: Multimodal Generative Engine Optimization - Rank Manipulation for Vision-Language Model Rankers.pdf）：

| 優化模式 | 平均排名變化 | 相對效能 |
|---|---|---|
| 純文字優化 | −0.73 | 1.0x（基準） |
| 純圖片優化 | −1.30 | **1.78x** |
| 圖片 + 文字聯合（MGEO） | −2.25 | **3.08x** |
| 商業 AI 改寫（HSCM） | −0.30 | 0.41x |

（負值代表排名上升。10 個商品列表中，隨機提升的期望值為 −4.5。）

**關鍵發現**：在 VLM-based 排名中，**圖片訊號（1.78x）的影響力高於文字訊號（1.0x）**——純圖片優化的效果比純文字優化高出約 78%。

---

## 跨模態耦合的放大效應

圖片與文字聯合優化的效果（−2.25）**超過兩者單獨效果之和**（−0.73 − 1.30 = −2.03），存在協同增益（synergy）。

VLM 的跨模態注意力機制使圖片與文字相互強化：
- 文字優化能更好地利用圖片特徵
- 圖片優化能強化文字的語義對齊
- 交替優化能找到單模態優化無法觸達的聯合最優解

**對 GEO 的意涵**：圖片與文字之間的**語義一致性（cross-modal coherence）**，可能比兩者各自的品質更重要。

---

## 「看起來更好」不等於「排名更高」：HSCM 的教訓

一個關鍵的負面結果：使用商業 AI 工具（GPT-4o-mini 改寫文字 + GPT-image-1-mini 修圖）的 HSCM 基線效果最差（−0.30），甚至低於純文字優化。

HSCM 的輸出具有：
- ✅ 更流暢的文字（AI 改寫品質高）
- ✅ 更美觀的圖片（AI 圖片編輯，如背景替換）
- ❌ 幾乎沒有排名提升效果

原因：**VLM 的排名判斷準則與人類的感知美感不一致**。商業 AI 工具優化「人類認為的好內容」，而非對齊「VLM 的內部排名目標函數所偏好的訊號」。

這個洞見同樣適用於合作性 GEO：
- 提升圖片美感 ≠ 提升 VLM 可見度
- 關鍵在於圖片與查詢語義的**對齊程度**，而非圖片的絕對視覺品質

---

## 按商品類別的差異

不同類別對圖片 vs 文字訊號的依賴程度不同（source: Multimodal Generative Engine Optimization - Rank Manipulation for Vision-Language Model Rankers.pdf）：

| 類別 | 純文字 | 純圖片 | 聯合 | 主導訊號 |
|---|---|---|---|---|
| Baby Stroller | −0.4 | −1.3 | **−3.9** | 圖片 |
| Juice | −0.4 | −2.1 | **−3.4** | 圖片 |
| Lipstick | −0.4 | −2.1 | **−2.2** | 圖片 |
| Basketball | −1.7 | −0.4 | **−2.1** | 文字 |
| Desk Lamp | −2.2 | −1.3 | **−1.4** | 文字 |
| Keyboard | +0.4 | −0.6 | **−1.7** | 圖片（文字有反效果） |
| Non-stick Pan | −0.6 | −1.2 | **−2.6** | 圖片 |
| Yoga Mat | −0.4 | −1.4 | **−1.8** | 圖片 |

**規律**：
- **視覺主導類別**（嬰兒車、飲料、美妝、廚具、健身器材）：圖片影響力遠高於文字
- **規格主導類別**（桌燈、籃球）：文字規格描述更重要，圖片效果有限
- **特殊案例**（鍵盤）：純文字優化甚至有反效果（+0.4），說明文字端的單模態攻擊可能干擾 VLM 的多模態判斷

---

## 對 GEO 從業者的意涵

### 1. VLM-based 搜尋需要圖片優化策略

在以 VLM 為核心的搜尋環境中，忽視圖片訊號將錯失可觀的可見度機會。視覺主導類別中，圖片優化的 ROI 可能高於文字優化。

### 2. 圖文跨模態一致性優先於單一模態品質

- 圖片應傳達與文字描述一致的語義（例如：強調輕便的嬰兒車，圖片要突出其輕巧感）
- 圖片與文字「各說各話」會降低 VLM 的相關性判斷

### 3. 美感 ≠ 可見度

AI 圖片生成工具（背景替換、風格美化）能提升人類感知品質，但不能自動對齊 VLM 的排名偏好。有效的圖片優化需要理解 VLM 如何解讀圖片語義。

### 4. 合作性多模態 GEO 的第一個方法：Caption Injection（2026）

2026 年，Caption Injection（Xiaoluchen et al.）提出了第一個針對多模態 GSE 的**合作性** G-SEO 方法，為上述「未來方向」提供了具體實現：

**核心思路**：將圖片提取出的 Object–Action–Scene 描述精煉後注入文字，讓 GSE 的 LLM 在生成回應時能「讀到」視覺語意。

**在多模態 GSE 中的效果**（MRAMG benchmark，4,800 查詢）：
- Caption Injection：+1.12% 主觀可見度（所有方法中最佳）
- 純文字最佳方法（流暢度優化）：+0.71%

這印證了「圖片與查詢語義的主動對齊」的實踐價值：Caption Injection 透過文字化的圖片語意，顯著提升了內容在多模態 GSE 中的差異化感知（uniqueness +3.50%）。

**與 MGEO（對抗性）的對比**：

| 面向 | MGEO（Du et al., 對抗性） | Caption Injection（合作性） |
|---|---|---|
| 目標 | 最大化自身排名，不惜犧牲系統品質 | 提升 GSE 回應中的主觀可見度 |
| 手段 | PGD 圖片擾動 + Soft Prompt（不可察覺） | 提取圖片描述、對齊文字、自然注入 |
| 道德立場 | 對抗性攻擊 | 合作性內容優化 |
| 可持續性 | 低（隨模型更新被修補） | 高 |

詳見 [[caption-injection-gseo]]。

---

## 研究局限（MGEO 論文，2026-01-18）

- 僅在 Qwen2.5-VL-7B 上測試，其他 VLM 的結果可能不同
- 假設靜態商品列表；真實電商平台有動態內容更新和平台預處理步驟
- 攻擊成功率因商品差異較大（有些商品需要引入明顯視覺瑕疵才能大幅提升排名）
- 防禦機制尚未探索，白盒假設在現實中難以完全成立

---

## 相關頁面

- [[vlm-ranking-attack]] — MGEO 框架技術詳解：PGD 圖片擾動 + Soft Prompt 文字優化
- [[caption-injection-gseo]] — 合作性多模態 G-SEO：Caption Injection 三階段流程與實驗結果
- [[mrag-gseo]] — 多模態 RAG 的 G-SEO 挑戰與 MRAMG benchmark
- [[geo-cooperative-vs-adversarial]] — 合作性 vs 對抗性 GEO 的整體框架（含 MGEO 數據）
- [[generative-engine-architecture]] — 生成式引擎架構分類（VLM-based 排名的架構背景）
- [[sageo-structural-information]] — 結構資訊在 SAGE pipeline 各階段的角色（文字側對比）
- [[big-brand-bias]] — 品牌偏見：VLM 排名中的非內容因素
