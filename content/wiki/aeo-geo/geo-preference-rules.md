# GEO 偏好規則集

**Summary**: 生成式引擎（Gemini、GPT、Claude）在使用文件生成回答時的內容偏好規則，由 AutoGEO 框架從數萬筆行為觀察中系統性提煉，涵蓋跨引擎共同規則與領域/引擎獨特規則。

**Sources**: What Generative Search Engines Like and How to Optimize Web Content Cooperatively.pdf（Wu et al., CMU, arXiv:2510.11438v1, 2025-10-13）

**Last updated**: 2026-04-15

---

## 規則的意義

這份規則集不是人工猜測，而是 [[autogeo-framework]] 從以下三個資料集、三種 LLM 引擎的行為觀察中**自動提煉**：

- **GEO-Bench**：8,000 筆開放域查詢（Princeton/IIT Delhi, KDD '24）
- **Researchy-GEO**：10,000 筆研究型多角度問題（CMU, 2025）
- **E-commerce**：1,667 筆電商查詢（CMU, 2025，從 LMSYS-Chat-1M 過濾）

每條規則均有實驗驗證：用該規則改寫文件後，GE 引用率顯著上升。

---

## 跨引擎共同規則（適用所有 GE）

以下規則在 Gemini、GPT、Claude 三種引擎上均出現，重疊率 79–84%，是最優先實作的核心清單。

### 1. 來源引用（Source Citation）
> 為所有事實性主張標注可信、具權威性的來源。

- Gemini 版本：「Attribute all factual claims to credible, authoritative sources with clear citations.」
- 實作：每 200-300 字至少一個明確引用，格式如「根據 [來源機構]...」

### 2. 全面覆蓋（Comprehensive）
> 涵蓋主題的所有關鍵面向與子主題，不遺漏重要角度。

- 避免只寫一個視角；複雜主題需主動梳理所有相關子問題

### 3. 事實準確（Factual Accuracy）
> 資訊準確、可查證，最好同時是最新的。

- Claude 版本特別強調「internally consistent」（內部一致性）

### 4. 主題聚焦（Topic Focus）
> 只保留與核心主題直接相關的內容，移除廣告、導覽連結、不相關段落。

- 「乾淨」的文件比包含大量雜訊的文件更易被引用

### 5. 中立語氣（Neutral Tone）
> 維持客觀、中立的論述語氣，避免促銷語言、個人偏見。

- 特別注意：不要讓文件讀起來像廣告或宣傳文

### 6. 平衡觀點（Balanced View）
> 對複雜或有爭議的主題，呈現多個重要觀點或反論。

### 7. 自足性（Self-Contained）
> 文件本身提供理解所需的全部脈絡，不依賴外部連結。

- GE 在處理文件時無法跟隨外部連結；自足的文件可被完整理解和引用

### 8. 可操作性（Actionable）
> 對於程序性主題，提供清晰、具體、可執行的步驟。

### 9. 深度解釋（In-depth）
> 澄清底層原因、機制和脈絡（解釋「如何」和「為什麼」，不只陳述事實）。

### 10. 結論優先（Conclusion First）
> 在文件開頭直接陳述關鍵結論。

- GE 在處理文件時有位置偏好（前段更重要），結論放前面有助被引用

### 11. 邏輯結構（Logical Structure）
> 用清晰的標題、列表、段落組織內容，確保邏輯流暢。

- Claude 版本特別強調「facilitate machine parsing」（方便機器解析）

### 12. 具體例證（Specific Evidence）
> 用具體的數據、統計或命名範例支持主張，而非抽象概括。

### 13. 清晰語言（Clear Language）
> 用清晰、簡潔、無歧義的語言，避免行話堆砌和冗詞。

### 14. 資訊即時性（Up-to-date）
> 使用最新的資訊，尤其是時效性強的主題。

### 15. 凝鍊（Conciseness）
> 刪除多餘語言、重複填充詞和不必要的冗餘。

---

## 引擎獨特規則

跨引擎重疊之外，每個 LLM 仍有獨特偏好：

| 規則 | 適用引擎 | 說明 |
|---|---|---|
| 寫作品質（Writing Quality） | Gemini 獨有 | 無語法錯誤、排版正確 |
| 資訊性目的（Informational Purpose） | GPT 獨有 | 純粹資訊性，無促銷或互動內容 |
| 單一焦點（Single Idea） | Claude 獨有 | 每個段落只表達一個明確想法 |
| 無障礙性（Accessibility） | GPT + Claude | 不需登入、訂閱或付費即可取得全文 |

---

## 領域差異

**開放域（Researchy-GEO、GEO-Bench）vs 電商（E-commerce）的規則重疊率只有 34–40%**，差異顯著。

### 研究型查詢的獨特規則

| 規則 | 說明 |
|---|---|
| In-Depth | 解釋底層機制（how/why） |
| Conclusion First | 結論放文件開頭 |
| Balanced View | 承認多個重要觀點 |
| Specific Evidence | 用數據/統計/命名例子支持 |
| Self-Contained | 不依賴外部連結 |
| Actionable | 提供可執行步驟 |
| Neutral Tone | 無促銷偏見 |
| Writing Quality | 無語法錯誤 |
| Cohesive Flow | 邏輯連貫的段落過渡 |

### 電商查詢的獨特規則

| 規則 | 說明 |
|---|---|
| Step-by-Step Guide | 提供步驟式指引或明確建議 |
| Production Details | 列出型號、技術規格、可量化數據 |
| Pros & Cons Rec | 用優缺點分析支持建議 |
| Non-Exaggerated | 客觀呈現，不過度誇張 |
| Modular | 將內容分成模組化、自足的段落或列表項目 |
| Term Definition | 定義技術術語或行話 |

---

## 規則集對比：研究型 vs 電商

以下為核心共同規則在兩個領域的表述差異（來源：AutoGEO 附錄 Table 7）：

| 規則關鍵字 | 研究型版本 | 電商版本 |
|---|---|---|
| Source Citation | 為所有事實主張附可信來源與明確引用 | 引用權威來源、提供證據或展示明確專業知識 |
| Comprehensive | 全面涵蓋主題所有關鍵面向與子主題 | 提供充足深度與廣度的完整答案 |
| Logical Structure | 用標題、列表、段落確保凝聚流暢 | 用標題、列表、表格組織，方便掃描 |
| Clear Language | 清晰簡潔，避免行話和模糊 | 清晰、簡單、明確，並定義技術術語 |

---

## 規則重疊率數據

### 跨引擎重疊（Researchy-GEO 資料集）

| 引擎對 | Jaccard 重疊率 |
|---|---|
| Gemini ↔ GPT | 78.95% |
| Gemini ↔ Claude | 84.21% |
| GPT ↔ Claude | 84.21% |

### 跨域重疊（Gemini 引擎）

| 資料集對 | 重疊率 |
|---|---|
| Researchy-GEO ↔ GEO-Bench | 88.24%（同為開放域） |
| Researchy-GEO ↔ E-commerce | 34.78% |
| GEO-Bench ↔ E-commerce | 40.00% |

**涵義**：跨引擎差異小，跨領域差異大。若只能做一套規則，優先用引擎特定規則；若資源有限，通用規則仍有顯著效果（規則轉移後仍比 Vanilla 有改善）。

---

## 各規則的個別貢獻

AutoGEO_API 消融研究顯示（Fig. 3）：
- **每條規則都有正向貢獻**，沒有雜訊規則
- 完整規則集的效果**優於任何單一規則**，規則之間互補
- 最有影響力的規則**因領域而異**：
  - E-commerce：Factual Accuracy、Pros & Cons、Step-by-Step Guide 影響最大
  - GEO-Bench：Comprehensive、Balanced View、In-depth 影響最大
  - Researchy-GEO：In-depth、Comprehensive、Balanced View 影響最大

---

## 實踐建議

依優先序：

1. **先實作共同規則**（15 條）：無論什麼引擎都有效，投資報酬率最高
2. **識別你的主要領域**：如果是電商，補充 Step-by-Step、Production Details；如果是研究型內容，補充 In-depth、Balanced View
3. **若目標特定引擎**：
   - Claude 偏好 Single Idea（每段一個焦點）和確保全文機器可讀
   - GPT 偏好 Informational Purpose（純資訊，無互動）和全文 Accessible
   - Gemini 偏好高寫作品質（無排版錯誤）
4. **Conclusion First 是最容易實作的高槓桿改動**：把結論從文末移到文首

---

## 相關頁面

- [[autogeo-framework]] — 規則如何被自動提煉（技術細節）
- [[geo-cooperative-vs-adversarial]] — 合作性 GEO vs 對抗性攻擊的對比
- [[geo-optimization-methods]] — 既有 9 種手工方法
- [[geo-domain-specific-strategies]] — 按領域的更細緻優化策略
- [[geo-visibility-metrics]] — GEO 指標體系
