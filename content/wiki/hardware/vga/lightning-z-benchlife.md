# MSI GeForce RTX 5090 Lightning Z 評測 — benchlife

**Summary**：benchlife 繁體中文評測，聚焦 360mm AIO 散熱設計與 1000W vs 800W 功耗模式的實測比較，最終給予 Silver 推薦。

**Sources**：`benchlife - 360mm 一體式水冷、2x 12V-2×6 接頭與 800W 功耗，MSI GeForce RTX 5090 Lightning Z 實測.docx`

**Last updated**：2026-04-24

---

## 基本資訊

- **評測媒體**：benchlife（台灣繁體中文）
- **原文網址**：https://benchlife.info/msi-geforce-rtx-5090-lightning-z-review/
- **評測結論**：Silver 推薦
- **評測單位卡號**：#12（全球限量 1,300 張）

---

## 散熱系統

[[lightning-z]] 採用 [[gpu-aio-cooler]] 設計，是本評測的核心主題。

- 360mm 一體式水冷，由 Cooler Master 代工
- 銅底水冷塊全覆蓋 GPU、GDDR7 記憶體、MOSFET
- 新一代馬達幫浦
- 混合式高低密度鰭片水冷排
- 3 顆 120mm Lightning 風扇
- 水管「東出」設計，所有外部線材包覆整齊，視覺更簡潔

（source: benchlife docx）

---

## 供電設計

- 採用 2x [[12v-2x6-connector]] 接頭
- 預設 OC 模式 TGP：800W（[[gpu-tgp-power-modes]]）
- EXTREME 模式：TGP 可達 1000W（透過雙 vBIOS 切換）

對比 ROG Matrix Platinum GeForce RTX 5090 採用的 BTF GC-HPWR 方案，Lightning Z 的 2x 12V-2×6 方案在預設條件下同樣達到 800W（source: benchlife docx）

---

## 1000W vs 800W 效能比較

**結論：兩者效能幾乎無差異，建議使用預設 OC（800W）模式。**（source: benchlife docx）

### 3DMark 成績（EXTREME 1000W vs OC 800W）

| 測試項目 | 1000W | 800W | 差異 |
|---|---|---|---|
| Time Spy（總分） | 39,004 | 39,130 | 0% |
| Time Spy Graphics Score | 51,386 | 51,736 | +1% |
| Time Spy Extreme（總分） | 19,716 | 19,165 | -3% |
| Time Spy Extreme Graphics Score | 27,633 | 27,437 | -1% |
| Fire Strike（總分） | 64,905 | 64,736 | 0% |
| Fire Strike Graphics Score | 102,276 | 102,954 | +1% |
| Fire Strike Extreme（總分） | 50,243 | 50,029 | 0% |
| Fire Strike Ultra（總分） | 34,052 | 34,096 | 0% |
| Port Royal | 41,361 | 41,512 | 0% |
| Speed Way | 15,618 | 15,605 | 0% |
| DXR | 196.57 | 196.83 | 0% |

### 4K 遊戲幀率（EXTREME 1000W vs OC 800W）

| 遊戲 | 設定 | 1000W | 800W | 差異 |
|---|---|---|---|---|
| Black Myth: Wukong | CINEMATIC | 67 fps | 68 fps | +1.5% |
| CS 2 | VERY HIGH | 383.5 fps | 379.6 fps | -1.0% |
| Cyberpunk 2077 | ULTRA | 121.64 fps | 120.64 fps | -0.8% |
| F1 25 | ULTRA MAX | 71 fps | 71 fps | 0% |
| Final Fantasy XIV: Dawntrail | — | 309.32 | 305.48 | -1.3% |
| Metro Exodus | ULTRA | 182.42 fps | 183.05 fps | +0.3% |
| Shadow of the Tomb Raider | HIGHEST | 256 fps | 257 fps | +0.4% |
| Total War: Warhammer 3 | — | 160.2 fps | 160.2 fps | 0% |
| Watch Dogs: Legion | ULTRA | 156 fps | 154 fps | -1.3% |

所有差異皆在 ±3% 以內，屬於量測誤差範圍（source: benchlife docx）

---

## EXTREME vs OC 熱力學比較

| 指標 | EXTREME（1000W） | OC（800W） | 差異 |
|---|---|---|---|
| TGP | 1,000W | 800W | +200W |
| GPU 時脈（中位數） | 2,895 MHz | 2,722 MHz | +173 MHz |
| GPU 溫度（中位數） | 72.0°C | 66.8°C | +5.2°C |
| GDDR7 溫度（中位數） | 72.0°C | 70.0°C | +2.0°C |
| 風扇轉速（中位數） | 1,866 RPM | 1,446 RPM | +420 RPM |

多耗 200W 換來 173 MHz 時脈提升，但效能增益接近 0%，同時溫度更高、風扇更吵（source: benchlife docx）

---

## 8 吋顯示面板

- 整合於顯示卡上蓋，功能類似第二顆外接小螢幕
- 透過 **MSI Lightning Hub** 的 **FlashDash** 功能控制
- 可顯示 GPU/CPU 即時資訊、影片等自訂內容
- 控制需安裝完整驅動，支援 Web 介面管理
- 附贈 **PCIe 5.0 Riser Cable + 直立支架**，方便面板朝向使用者

（source: benchlife docx）

---

## 與 ROG Matrix Platinum 比較

| 項目 | MSI RTX 5090 Lightning Z | ROG Matrix Platinum RTX 5090 |
|---|---|---|
| 散熱 | 360mm AIO 水冷 | 3+1 風扇空冷 |
| 供電接頭 | 2x 12V-2×6 | BTF GC-HPWR |
| 預設 TGP | 800W | 800W |
| 特殊定位 | 最強 RTX 5090 | ASUS 顯示卡 30 週年紀念版 |
| 整體感受 | 簡潔、實在 | 承先啟後、有儀式感 |

（source: benchlife docx）

---

## 評測結語

> 360mm 的一體式水冷散熱，相較於空冷散熱器能提供更佳的散熱表現，只是機殼的選擇就…

benchlife 給予 Silver 推薦，認可其散熱設計與整體規格，但暗示 AIO 方案對機殼相容性有要求（source: benchlife docx）

Lightning 系列上次出現是 RTX 2080 Ti 時代，且當時仍為空冷。RTX 5090 Lightning Z 是系列重啟後的全新起點（source: benchlife docx）

---

## Related pages

- [[lightning-z]]
- [[rtx-5090]]
- [[gpu-aio-cooler]]
- [[12v-2x6-connector]]
- [[gpu-tgp-power-modes]]
