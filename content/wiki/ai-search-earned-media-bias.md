# AI 搜尋的 Earned Media 偏見

**Summary**: AI 搜尋引擎對第三方媒體（Earned Media）有系統性壓倒性偏好，幾乎排除社交媒體內容，與 Google 的均衡分佈形成強烈對比。這是 GEO 實踐中最重要的單一發現。

**Sources**: Generative Engine Optimization - How to Dominate AI Search.pdf

**Last updated**: 2026-04-15

---

## 三種媒體類型定義

| 類型 | 說明 | 範例 |
|------|------|------|
| **Brand** | 品牌官網，直接提供產品或服務 | apple.com、toyota.com |
| **Earned** | 獨立媒體、評測網站、比較網站 | techradar.com、forbes.com、consumerreports.org |
| **Social** | 社交平台、社群論壇、用戶生成內容 | reddit.com、youtube.com、quora.com |

## 核心數據：AI vs Google 的媒體分佈

### 汽車垂直領域（美國）

| 系統 | Brand | Earned | Social |
|------|-------|--------|--------|
| Google | 39.5% | 45.1% | 15.4% |
| AI 搜尋 (GPT) | 18.1% | 81.9% | 0% |

### 消費電子（美國）

| 系統 | Brand | Earned | Social |
|------|-------|--------|--------|
| Google | 32.9% | 51.7% | 15.4% |
| AI 搜尋 (GPT) | 7.9% | 92.1% | <1% |

### 軟體產品（美國）

| 系統 | Brand | Earned | Social |
|------|-------|--------|--------|
| Google | 43.7% | 45.4% | 10.9% |
| AI 搜尋 (GPT) | 26.7% | 72.7% | <1% |

**一致性結論**：無論垂直領域或地區，AI 搜尋的 Earned 佔比都在 70–93%，Social 幾乎為零。

## 各 AI 引擎的 Earned Media 程度

不同 AI 引擎的偏向程度有所不同，但都符合 Earned ≫ Brand ≫ Social 的模式：

| 引擎 | 知名品牌查詢（Earned） | 利基品牌查詢（Earned） | Social 容忍度 |
|------|---------------------|---------------------|-------------|
| **ChatGPT** | 93.5% | 95.1% | 0% |
| **Claude** | 87.3% | 86.3% | ~3% |
| **Perplexity** | 67.4% | 73.4% | ~18–24% |
| **Gemini** | 63.4% | 66.4% | ~11–13% |

（來源：Generative Engine Optimization - How to Dominate AI Search.pdf）

ChatGPT 和 Claude 是最保守的 Earned-heavy 引擎；Perplexity 最包容多元來源；Gemini 最傾向 Brand 官網。

## 查詢意圖對媒體分佈的影響

查詢類型會調整分佈，但不改變 Earned-heavy 的基本傾向：

| 查詢類型 | 說明 | AI 的變化 |
|---------|------|---------|
| **Informational**（資訊類） | 「OLED TV 如何運作？」 | Earned 為主，幾乎無 Social |
| **Consideration**（考慮類） | 「學生最佳筆電 2025」 | Earned 幾乎獨佔（GPT 幾乎 100%） |
| **Transactional**（交易類） | 「線上購買 iPhone 15」 | Brand 佔比上升，但 Earned 仍居多 |

對比之下，Google 在三種查詢類型中都保持 Brand / Earned / Social 均衡。

## 跨語言的一致性

即使查詢語言改變，AI 的 Earned-heavy 模式依然維持。無論中文、日文、德文、法文或西班牙文查詢，各引擎都維持 Earned ≫ Brand ≫ Social 的分佈，只是在哪些具體 domain 被引用上有所不同。

詳見 [[geo-language-strategy]]

## 為什麼 Social 內容被排除

（論文觀察，非引擎官方解釋）

- AI 引擎優先可驗證的第三方事實，社交媒體的非結構化、主觀性內容難以被用作佐證
- 社交平台的意見難以歸因或引用
- AI 設計上傾向提供「機構認可」的來源，提升回答可信度

## 對 GEO 實踐者的涵義

**最高優先級行動**：

1. **放棄「社交媒體流量→AI 可見度」的假設**：Reddit 熱門、YouTube 高觀看數，不代表 AI 會引用你的品牌
2. **將 Earned Media 建設列為核心策略**：PR 活動、媒體合作、產品評測邀約，不是附加行銷，是 GEO 的主幹
3. **反向連結品質比數量更重要**：被 TechRadar、Tom's Guide、Consumer Reports 等 Earned 頂級網站引用，比數百個低品質連結更有效
4. **品牌官網仍有價值，但有上限**：Brand 內容在 AI 中佔 7–30%，意味著你的官網不會被完全忽略，但絕對不夠

## 相關頁面

- [[geo-dominate-ai-search]] — 來源論文摘要
- [[ai-search-engine-comparison]] — 各引擎 Earned Media 傾向的差異
- [[big-brand-bias]] — 品牌知名度如何影響 AI 推薦
- [[geo-optimization-methods]] — 內容層面的優化方法
- [[geo-language-strategy]] — 語言對 Earned Media 引用的影響
