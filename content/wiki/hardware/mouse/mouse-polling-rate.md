# 滑鼠輪詢率（Polling Rate）

**Summary**: 輪詢率定義滑鼠每秒向電腦回報位置的頻率，8,000Hz 為現階段高階遊戲滑鼠的最高規格，帶來更低延遲但也伴隨電池與 CPU 消耗的代價。

**Sources**: `Club386 (UK) - MSI Versa 300 Wireless 8K review_ a high-value, high-polling mouse.docx`

**Last updated**: 2026-04-26

---

## 基本概念

輪詢率（Polling Rate）以 Hz 為單位，表示滑鼠每秒向系統傳送位置資料的次數：

| 輪詢率 | 回報間隔 |
|--------|----------|
| 125Hz | 8ms |
| 500Hz | 2ms |
| 1,000Hz | 1ms |
| 2,000Hz | 0.5ms |
| 8,000Hz | 0.125ms |

輪詢率越高，理論上輸入延遲越低、游標軌跡越順暢。

## 8,000Hz 的實際影響

### 優點
- 輸入延遲降至 0.125ms，對反應時間要求極高的競技遊戲（如 CS2、Valorant）具理論優勢
- 游標軌跡資料點密度更高，微小移動更精準捕捉

### 代價
- **電池消耗加速**：高輪詢率需要更頻繁的無線傳輸。以 [[versa-300-wireless-8k]] 為例，1,000Hz 無 RGB 可達 196 小時，8,000Hz 有 RGB 僅約 80 小時（source: Club386 review docx）
- **CPU 使用率上升**：相較於 1,000Hz 或 2,000Hz，8,000Hz 在部分系統下會明顯提高 CPU 佔用（source: Club386 review docx）

## 是否值得？

Club386 評測者指出，高 DPI 與高輪詢率的效益在真實對局中「難以切身感受」，但在技術規格上仍是優勢，且未來有更多選擇空間（source: Club386 review docx）。目前 8,000Hz 多見於高階或旗艦產品，[[versa-300-wireless-8k]] 在 £59.99 的主流價位提供此規格，屬市場少見。

## 相關建議

若對 CPU 或電池使用較為在意，可將輪詢率降至 1,000Hz 或 2,000Hz，效能差異在多數情境下不易察覺。開啟嚴格閒置與睡眠計時器亦可延長電池壽命。

## Related pages

- [[versa-300-wireless-8k]]
- [[versa-300-wireless-8k-club386]]
- [[pixart-paw3395]]
