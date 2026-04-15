# 引用失敗分類法（Citation Failure Taxonomy）

**Summary**: 首個系統性的引用失敗模式分類法，橫跨生成式引擎的 Fetching、Parsing、Generation 三個流水線階段，共四大類別十二種失敗模式。

**Sources**: Diagnosing and Repairing Citation Failures in Generative Engine Optimization.pdf（Tian et al., Virginia Tech + Zhejiang University, 2026-03-11）

**Last updated**: 2026-04-15

---

## 研究背景

本分類法基於 949 組「失敗 vs 成功」對比案例建立。每組案例來自相同查詢——一個被引用的競爭者頁面（Winner）和一個未被引用的目標頁面（Loser）。對比策略的目的在於隔離邊際因素：兩頁面都被檢索到，差異只在於引擎為何偏好其中一個。

資料來源：GEO-Bench，涵蓋 10 個領域（健康、旅遊、科技等），查詢涵蓋多種意圖。

---

## 四大類別分布

| 類別 | 比例 | 發生階段 |
|---|---|---|
| **Technical Integrity**（技術完整性） | 10.1% | Fetching / Parsing |
| **Semantic Alignment**（語義對齊） | 62.2% | Generation |
| **Content Quality**（內容品質） | 27.1% | Generation |
| **Systemic Exclusion**（系統性排除） | 0.6% | 跨階段結構性問題 |

**最重要的發現**：超過 62% 的失敗屬於語義對齊問題——頁面技術上正常，也被成功解析，但引擎認為它「不夠回答查詢意圖」。

---

## 類別一：Technical Integrity（10.1%）

頁面在 Fetching 或 Parsing 階段就已失敗，內容從未抵達生成模型。

### 1.1 Access Blocking（3.9%）
**定義**：爬蟲被防火牆、403 拒絕、登入牆攔截。

**案例**：
> 查詢：「the federal communications commission (fcc) controls and regulates」
> 失敗原因：FederalRegister.gov 封鎖了自動爬蟲，回應要求完成 CAPTCHA 才能訪問。

**修復工具**：無法靠內容優化解決，需調整 robots.txt 和爬蟲策略。

---

### 1.2 JS/Dynamic Failure（0.4%）
**定義**：頁面依賴 JavaScript 動態渲染，爬蟲只拿到空殼 HTML。

**案例**：
> 查詢：「Where can I order a birthday cake online?」
> 失敗原因：頁面提示「Sorry, this webpage requires JavaScript to function correctly.」——內容全部在 JS 中，爬蟲什麼都拿不到。

**修復工具**：AgentGEO 中的 `static_renderer_simulator`——將 JS 邏輯轉換為靜態 HTML。

---

### 1.3 Unparseable Content（0.6%）
**定義**：HTML 結構損毀、字元編碼錯誤，導致提取出亂碼。

**案例**：
> 查詢：「Animal Castration – Why doesn't it seem to hurt very much?」
> 失敗原因：提取出的是二進位亂碼流（`jcs9m??j+?"??3?I?S?...`），完全無法閱讀。

---

### 1.4 Low Signal-to-Noise（5.2%）
**定義**：有效內容被廣告、導航、頁尾、Cookie 警告等雜訊淹沒，解析器無法定位核心段落。

**案例**：
> 查詢：「who plays jekyll in once upon a time」
> 失敗原因：頁面只有 IMDb 的元資料標題（「Release Dates—Official Sites—Company Credits—Filming...」），沒有任何實質性段落。

**修復工具**：`noise_isolation`——用語義 HTML 標籤（`<aside>`, `<footer class="geo-noise">`）標記雜訊，讓解析器能過濾。

---

## 類別二：Semantic Alignment（62.2%）

頁面被成功解析並進入生成模型，但內容被判斷為「不符合查詢意圖」。這是最常見的失敗類別。

### 2.1 Intent Divergence（29.1%）
**定義**：頁面回應的用戶目標和查詢目標不一致（如查詢是 Transactional，但頁面是 Informational）。

**案例**：
> 查詢：「when does the turn of the screw take place」（查詢原著設定）
> 失敗原因：頁面全部是關於 1974 年電影改編版的資訊，根本沒提到原著發生的時間地點。

**修復工具**：`intent_realignment`——改寫開頭段落，直接回應查詢意圖；非相關細節移至段落末尾而非刪除。

---

### 2.2 Contextual Gap（32.3%）
**定義**：主題相關，但缺少查詢要求的具體實體、術語或細節。

**案例**：
> 查詢：「mention the names of any 3 famous folklore sports in karnataka state」
> 失敗原因：頁面詳細討論了 Karnataka 的板球、足球、羽毛球等現代運動，但幾乎沒提傳統民俗運動（Kambala、Korikatta），即使有提也只是隱藏在頁面深處，沒有明確突顯。

**修復工具**：`entity_injection`——針對性地在語義最相關的位置插入缺失實體，用 `<strong>` 標籤語義高亮。

---

### 2.3 Outdated Information（0.1%）
**定義**：內容在事實上已過期或時間不匹配。

**案例**：
> 查詢：「the most recent technological change to the u.s. economy was」
> 失敗原因：頁面是 1987 年的出版物，討論的是三十年前的技術變革，完全沒提 AI、機器人、第四次工業革命。

**修復工具**：`historical_redteam`——用時間線框架重構，把舊資訊定位為「理解當前的必要歷史脈絡」。

---

### 2.4 Localization Mismatch（0.4%）
**定義**：地區、語言或法規不符合查詢的隱含地域要求。

**案例**：
> 查詢：「Book a spa appointment for a facial treatment」（未指定地點）
> 失敗原因：頁面全是紐約市的水療推薦，對一個不在紐約的使用者完全沒用。

---

## 類別三：Content Quality（27.1%）

頁面語義對齊，但呈現方式太差，無法被模型有效引用。

### 3.1 Information Scarcity（26.1%）
**定義**：內容太淺薄、「fluffy」，缺乏可直接引用的事實和細節。

**案例**：
> 查詢：「when is an articulated lorry most likely to jackknife」
> 失敗原因：頁面只有「點擊以下問題查看可能的答案」，完全沒有實質性解釋。

**修復工具**：`entity_injection` 或 `persuasive_rewriting`（增加深度和可信度）。

---

### 3.2 Content Fragmentation（0.3%）
**定義**：內容片段化，各部分之間缺乏連貫性，難以被 LLM 合成引用。

---

### 3.3 Excessive Verbosity（0.2%）
**定義**：關鍵事實被大量填充內容稀釋，模型很難定位核心答案。

**修復工具**：`bluf_optimization`——在頁面頂部加「Bottom Line Up Front」摘要框，直接呈現最重要的 1-2 句事實。

---

### 3.4 Unstructured Layout（0.5%）
**定義**：內容格式化不當——密集純文字塊，應該用表格呈現的資料沒有表格化。

**案例**：
> 查詢：「what position did doug peterson play in the nfl」
> 失敗原因：頁面全是密集統計表格，沒有一句直接說明「Doug Pederson 在 NFL 打四分衛」，模型無法提取直接答案。

**修復工具**：`structure_optimization`（插入語義標題和列表）；`data_serialization`（轉換叙事型數據為 HTML 表格）。

---

## 類別四：Systemic Exclusion（0.6%）

即使頁面技術正常、語義對齊、內容品質也不錯，仍然因結構性劣勢而失敗。這是**無法靠內容優化解決**的失敗類型。

### 4.1 Competitive Redundancy（0.5%）
**定義**：高權威來源（Wikipedia、Coursera 等）涵蓋了完全相同的事實，引擎選擇了更有公信力的來源。

**案例**：
> 查詢：「What is the theory of gravity wave detection?」
> 失敗原因：目標頁面的內容詳盡且準確，但 Wikipedia 涵蓋了同樣的事實，引擎選擇了更具權威的 Wikipedia。

**實驗驗證**：一個機器學習大學課程頁面對「最佳線上機器學習課程」查詢，即使優化後增加了詳細課程描述，引擎仍持續偏向 Coursera、edX 等知名平台。這說明 GE 可能存在**領域層面的內部偏見**，超出了頁面內容本身。

詳見 [[big-brand-bias]] 和 [[geo-cooperative-vs-adversarial]] 中的不可修復失敗討論。

---

### 4.2 Window Truncation（0.1%）
**定義**：查詢的答案存在於頁面中，但位置太深，超出了 context window 的 token 上限，從未被模型讀到。

**案例**：
> 查詢：「When is the snowboard big air competition?」
> 失敗原因：頁面的可見部分只有一段運動員背景介紹，實際賽程時間表深埋在頁面後段，超出了 context window。

**修復工具**：`content_relocation`——在頁面頂部建立摘要區塊，將深層內容的關鍵資訊上移至可見範圍。

---

## 對 GEO 實踐的核心啟示

### 「診斷優先」原則

本分類法最重要的應用是說明為什麼**通用優化策略會失效**：

- 一個 Access Blocking 失敗的頁面，靠「加統計數據」毫無幫助
- 一個 Intent Divergence 失敗的頁面，靠「改善結構」也無效
- 每個失敗有其根因，需要針對性修復

詳見 [[agentgeo-framework]]——一個基於此分類法的診斷-修復系統。

### 通用規則的「長尾陷阱」

實驗發現，AutoGEO 等通用規則在部分主題（健康類）反而降低引用率——因為通用規則基於聚合模式，而在特定領域，這些模式可能不適用甚至反效果。診斷式優化透過針對每頁的具體失敗原因介入，能更公平地提升各類主題的可見度。

---

## 相關頁面

- [[agentgeo-framework]] — 基於此分類法的診斷-修復系統
- [[geo-citation-vs-contribution]] — 為什麼要聚焦「引用率」而非「貢獻度」
- [[big-brand-bias]] — Systemic Exclusion 的 Competitive Redundancy 深入分析
- [[geo-cooperative-vs-adversarial]] — 不可修復失敗的意義
- [[geo-optimization-methods]] — AgentGEO 9 種修復工具詳解
- [[generative-engine-architecture]] — 三種引用機制如何影響失敗模式
