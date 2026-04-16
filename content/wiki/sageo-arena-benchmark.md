# SAGEO Arena Benchmark

**Summary**: 延世大學提出的首個支援全流程（檢索→重排→生成）階段級可見度評估的 SAGEO 基準測試，語料庫含 171K 真實網頁文件，保留完整結構資訊。

**Sources**:
- SAGEO Arena - A Realistic Environment for Evaluating Search-Augmented Generative Engine Optimization.pdf（Kim et al., 延世大學 + 建國大學，arXiv:2602.12187，2026-02-12）

**Last updated**: 2026-04-16

---

## 背景：既有 benchmark 的兩大缺陷

既有 GEO 評估環境（GEO-Bench、AutoGEO、C-SEO Bench、CC-GSEO-Bench）均存在根本性問題：

### 缺陷一：缺乏端到端評估

所有現有 benchmark 都預設目標文件已在候選集內，直接評估生成階段。這跳過了現實中的檢索和重排兩關。

**後果**：無法得知優化策略是否讓文件**連被檢索到**都成問題——文件若在檢索階段就掉出候選集，生成階段的優化毫無意義。

### 缺陷二：丟失結構資訊

所有現有 benchmark 只保留 body text，丟棄真實網頁中的 title、meta description、headings、Schema/JSON-LD。

**後果**：無法研究這些結構信號（Google/Bing 指南明確強調的優化要素）對 AI 搜尋引擎的影響。

---

## Benchmark 比較表

| Benchmark | 語料庫 | 檢索 | 重排 | 生成 | 結構資訊 | 可見度指標 |
|-----------|--------|------|------|------|----------|-----------|
| GEO-Bench | — | ✗ | ✗ | ✓ | ✗ | Word Count |
| AutoGEO | — | ✗ | ✗ | ✓ | ✗ | Word Count, Utility |
| C-SEO Bench | — | ✗ | ✗ | ✓ | ✗ | Citation Rank |
| CC-GSEO-Bench | — | ✗ | ✗ | ✓ | ✗ | Influence |
| **SAGEO Arena** | **170K** | **✓** | **✓** | **✓** | **✓** | **Hit Rate, Rank Change** |

---

## 語料庫構建

### 規模與來源

- **171,003** 個唯一網頁文件
- **2,700** 個測試查詢（9 個領域，每領域 300 個）
- 每個查詢平均對應 **63** 個候選文件
- 使用 Google Custom Search API 每個查詢取回最多 100 個搜尋結果，再爬取完整頁面

### 九個領域

| 來源資料集 | 領域 | 查詢數 | 文件數 |
|-----------|------|--------|--------|
| MS MARCO | Web Search | 300 | 21,880 |
| Natural Questions | General QA | 300 | 21,921 |
| HotpotQA | Multi-hop QA | 300 | 13,409 |
| NFCorpus | Biomedical | 300 | 21,079 |
| Quora | Community QA | 300 | 21,734 |
| FiQA | Finance | 300 | 16,771 |
| DebateQA | Debate | 300 | 17,443 |
| E-commerce | Shopping | 300 | 18,885 |
| Researchy | Academic | 300 | 17,881 |
| **合計** | | **2,700** | **171,003** |

---

## 結構資訊擷取

SAGEO Arena 依照 Google/Bing Webmaster Guidelines 保留五類結構欄位（詳見 [[sageo-structural-information]]）：

- **Title**：頁面主題的簡潔摘要，初始相關性評估的主要信號
- **Meta Description**：搜尋結果摘要，補充 title 的簡潔描述
- **Headings（H1–H6）**：章節標題層級，搜尋引擎視為比 body text 更強的主題信號
- **Schema/JSON-LD**：結構化標記，機器可讀的實體、屬性和關係定義
- **Body Text**：主要文字內容，分成 256 token 段落（64 token 重疊）

**重要設計原則**：各欄位以獨立索引（BM25 多欄位）而非合併成單一文字，保留每個元件的獨立信號，允許細粒度分析。

---

## 生成式搜尋 Pipeline

### 三階段架構

```
用戶查詢 q
  ↓
[檢索] BM25 多欄位索引 + Reciprocal Rank Fusion
  → 取回 top-100 段落
  ↓
[重排] Qwen3-Reranker-4B（cross-encoder）
  → 保留 top-10
  ↓
[生成] GPT-5-mini（帶 inline citation）
  → 生成含引用的回答
```

### 語義單元（Semantic Unit）

每個 body text 段落 $p_i$ 與其所屬文件的完整結構資訊 $S(d)$ 配對，形成語義單元：

$$\mathcal{U}(p_i) = S(d) \cup \{p_i\}$$

這解決了「結構資訊屬於文件層級、但檢索是段落層級」的落差。

### 文件排名追蹤

同一文件的多個段落可能同時出現在候選列表，以最高排名的段落代表該文件：

$$\text{rank}(d^{\text{tgt}}_q) = \min_{p \in \mathcal{P}(d^{\text{tgt}}_q)} \text{rank}(p)$$

---

## 評估方法

### 評估流程

1. 執行基線 pipeline，對每個測試查詢選取一個**在重排後進入 top-10 的文件**作為目標文件
2. 對目標文件套用 SAGEO 優化策略，重新索引入語料庫
3. 用相同查詢重新執行 pipeline，比較優化前後在各階段的可見度

### 指標

**Hit Rate（H@k）**：目標文件出現在 top-k 候選集的查詢比例

- 檢索：H@20（top-100 中評估 top-20）
- 重排：H@10（基線固定為 1.00，因為目標文件本身從 top-10 選出）
- 生成：Citation Rate（目標文件被引用的查詢比例）

**Rank Change（ΔRank）**：優化前後排名位移的平均值（正值 = 排名提升）

$$\Delta\text{Rank} = \frac{1}{|Q|} \sum_{q \in Q} \left(\text{rank}_{\text{base}}(d^{\text{tgt}}_q) - \text{rank}_{\text{SAGEO}}(d^{\text{tgt}}_q)\right)$$

未進入 top-k 的文件賦予預設排名 k+1（檢索 k=100，重排 k=10）。

---

## 與 RAG Benchmark 的根本差異

標準 RAG benchmark 評估**系統層級的答案品質**（忠實度、相關性），不設計文件層級評估協議。SAGEO Arena 的差異：

- 每個查詢明確指定目標文件
- 追蹤目標文件在三個階段的可見度（非系統整體品質）
- 控制文件優化效果與生成品質的混淆
- 支援「哪個 pipeline 階段是瓶頸」的分析

---

## 相關頁面

- [[sageo-structural-information]] — 五類結構欄位的定義、角色與優化方向
- [[stage-aware-sageo]] — 主要實驗發現 + 階段感知優化方法
- [[generative-engine-architecture]] — SAGE pipeline 架構分類
- [[geo-optimization-methods]] — 各優化策略的效果數據
- [[autogeo-framework]] — AutoGEO 在 SAGEO Arena 中的表現（最差降幅）
- [[geo-domain-specific-strategies]] — 購物領域的特殊現象
