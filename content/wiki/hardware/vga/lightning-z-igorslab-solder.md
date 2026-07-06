# MSI RTX 5090 Lightning Z 焊點門分析（igorslab）

**Summary**：igorslab 作者親自持有 Lightning Z 樣品，針對外界討論的「焊點門」進行電氣實測與製程分析，結論為功能正常但外觀不符旗艦定位。

**Sources**：
- `igorslab - Eye for solder eyes, tooth for chattering teeth_ What's really going on with the "solder joint gate" of the MSI RTX 5090 Lightning Z_.docx`

**Last updated**：2026-04-24

---

## 背景

MSI GeForce RTX 5090 Lightning Z 上市後，論壇與社群開始流傳其 [[12v-2x6-connector]] 供電區域部分焊點外觀異常的圖片，引發廣泛討論。最初的圖片主要來自 TechPowerUp 分析與 Alva Jonathan 的影片記錄。

---

## 三種異常層次

### 1. GND 針腳（回路電流接地腳）

多張媒體圖片顯示接地針腳填錫量偏少或不均勻。技術上影響有限：

- 回路電流並非僅走 12V-2×6 的 GND 腳，也同時流經 PCIe 插槽的接地接點（並聯路徑）
- 12V-2×6 規格本身有多條平行 GND 針腳，提供額外的電流分散能力
- 個別焊點外觀欠佳通常不造成直接功能限制

### 2. 作者自身樣品的 12V 供電針腳

作者自己的 Lightning Z 有一支 12V 供電針腳錫量明顯偏少，此情況更需關注：

- 供電腳承載的是直接從電源供應器至 GPU 的主電流
- 供電線路**不具備**與接地路徑相同的外部並聯冗餘
- 雖有多條平行供電腳，但不均勻的焊接可能導致電流分配不對稱
- 理論上可能造成長期可靠性問題、局部熱應力與老化加速

---

## 製程背景分析

igorslab 比對 Lightning Z 與 Suprim 兩塊板的標記，得出以下觀察：

| 項目 | Suprim | Lightning Z |
|---|---|---|
| 平台代號 | MS-V530 | MS-V530（相同） |
| PCB 製造商 | Palwonn Electronics（深圳）| Palwonn Electronics（深圳）|
| 批次追蹤標記 | 標準化、一致 | 較不一致，部分標記結構不同 |
| 合規認證標章 | 完整（FCC、UKCA、KC、RoHS 等） | 完整（相同） |

**推論**：Lightning Z 屬於小批量生產，可能使用獨立製造線（選擇性焊接或較少自動化），這合理解釋了焊點外觀較不均勻的現象，但並非來自不同 OEM 廠商。

---

## IPC 工業標準

本文引用的驗收標準：

| 標準 | 說明 |
|---|---|
| IPC-A-610 | THT 焊點視覺驗收準則 |
| IPC-J-STD-001 | 焊接製程品質要求 |
| Class 2 barrel fill 要求 | 通孔填充深度 ≥ **75%**，理想情況兩面均可見焊料 |

**限制**：無 X 光或截面顯微分析，僅憑外觀無法確認通孔內部填充情況。

---

## 作者實測結果

| 量測項目 | 結果 |
|---|---|
| 可疑 12V 針腳接觸電阻 | 與其他針腳**平均值相同**，無異常 |
| 各針腳電流分配 | 對稱，無不均現象 |
| 高負載局部溫度 | 無異常過熱點 |
| 整卡穩定性 | 正常 |

---

## 結論

- **電氣功能**：正常，目前量測未發現任何異常
- **長期可靠性**：理論上有輕微疑慮（12V 腳焊點），但無實測佐證
- **外觀品質**：不符合 $5,090 USD 旗艦定位的預期標準
- 作者表示考慮補焊，「不是出於必要，而是良心過不去」

> 「技術上或許無關緊要，但心理上卻出奇地持久。」—— igorslab

---

## Related pages

- [[lightning-z]]
- [[12v-2x6-connector]]
- [[tht-solder-quality]]
- [[lightning-z-tpu]]
