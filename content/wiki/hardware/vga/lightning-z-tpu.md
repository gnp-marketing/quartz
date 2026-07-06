# MSI GeForce RTX 5090 Lightning Z 評測 — TechPowerUp

**Summary**：TechPowerUp 英文深度評測，涵蓋 PCB 分析、VRM 規格、噪音標準化散熱測試、超頻，以及與所有主要 RTX 5090 變體的完整比較。

**Sources**：`TPU _ MSI GeForce RTX 5090 Lightning Z Review - Up to 1000 W.docx`

**Last updated**：2026-04-24

---

## 基本資訊

- **評測媒體**：TechPowerUp（英文）
- **原文網址**：https://www.techpowerup.com/review/msi-geforce-rtx-5090-lightning-z/
- **售價**：$5,090 USD（MSRP，數字刻意對應型號）
- **測試系統**：AMD Ryzen 7 9800X3D、MSI X870E Carbon Wi-Fi、DDR5-6200、Seasonic Prime TX 1600W、驅動 581.15 WHQL

---

## 效能總結

**無 RT、無 DLSS 條件下，比 RTX 5090 FE 快 10%。** TPU 稱這相當於假想中的「RTX 5090 Ti」。（source: TPU docx）

| 對比卡 | Lightning Z 相對優勢 |
|---|---|
| RTX 5090 Founders Edition | +10% |
| ASUS RTX 5090 Matrix（含 BTF 800W） | 稍快 |
| ASUS RTX 5090 Astral Liquid | +5%（Astral）vs +10%（Lightning） |
| RTX 4090 | +約 50% |
| AMD RX 9070 XT | 超過 2 倍 |

（source: TPU docx）

---

## 工廠超頻

| 卡型 | 原廠 Boost 時脈 | 預設 TGP |
|---|---|---|
| NVIDIA RTX 5090 FE | 2,407 MHz | 575W |
| MSI RTX 5090 Suprim Liquid SOC | 2,527 MHz | 575W |
| **MSI RTX 5090 Lightning Z** | **2,730 MHz** | **800W** |

- 比 FE 原廠時脈快 **+323 MHz / +13%**
- 同時提升時脈 + 功耗上限 + AIO 散熱，三者協同作用使效能最大化（source: TPU docx）

---

## 超頻（手動 OC）

| 卡型 | OC 平均 GPU 時脈 | OC 幀率 | 最大功耗 |
|---|---|---|---|
| NVIDIA RTX 5090 FE | 2,938 MHz | 166.7 FPS | 575/600W |
| MSI RTX 5090 Suprim Liquid SOC | 3,069 MHz | 175.8 FPS | 600/600W |
| **MSI RTX 5090 Lightning Z** | **3,218 MHz** | **177.8 FPS** | **800/1000W** |
| Lightning Z（最大電壓） | — | **180.2 FPS** | 1000W |

- 超頻後全 RTX 5090 系列最快（source: TPU docx）
- MSI Afterburner 提供完整電壓控制：GPU (NVVDD)、Memory (FBVDD)、AUX (MSVDD)、AUX2 (PEXVDD)
- 注意：NVIDIA 限制 GDDR7 記憶體 OC 上限為 +375 MHz（超過此值驅動自動截斷）

---

## 遊戲中實測時脈（1000W EXTREME BIOS）

| 場景 | GPU 時脈 | 記憶體時脈 | GPU 電壓 |
|---|---|---|---|
| Cyberpunk 2077（遊戲） | 3,037 MHz | 1,750 MHz | 1.090V |
| Stalker 2（遊戲） | 2,992 MHz | 1,750 MHz | 1.090V |
| Spider-Man 2（遊戲） | 3,015 MHz | 1,750 MHz | 1.090V |
| 25 款遊戲平均 | 3,021 MHz | 1,750 MHz | 1.089V |
| Furmark（壓力測試） | 2,953 MHz | 1,750 MHz | 1.026V |

（source: TPU docx）

---

## 散熱與噪音

### 實測溫度與噪音

| 模式 | GPU 溫度 | GDDR7 溫度 | 噪音 | 風扇轉速 |
|---|---|---|---|---|
| 800W (OC) | 64°C | 66°C | **29.4 dBA** | 1,281 RPM |
| 1000W (Extreme) | 66°C | 66°C | 36.2 dBA | 1,562 RPM |

### RTX 5090 各版本噪音比較（TPU 實測，選摘）

| 卡型 | 模式 | GPU 溫度 | 噪音 |
|---|---|---|---|
| NVIDIA RTX 5090 FE | 預設 | 77°C | 40.1 dBA |
| ASUS RTX 5090 TUF | 預設 | 66°C | 41.1 dBA |
| ASUS RTX 5090 Astral LC | 預設 | 58°C | 35.9 dBA |
| MSI RTX 5090 Suprim Liquid SOC | 預設 | 61°C | 31.2 dBA |
| **MSI RTX 5090 Lightning Z** | **800W** | **64°C** | **29.4 dBA** |
| MSI RTX 5090 Lightning Z | 1000W | 66°C | 36.2 dBA |

（source: TPU docx）

### 噪音標準化散熱測試（35 dBA 等噪音條件）

在相同 35 dBA 噪音條件下，Lightning Z 散熱器：

- 比 RTX 5090 FE 涼 **31°C**
- 比一般優秀空冷 RTX 5090 涼 **15°C**
- 比頂級水冷版本（如 LYNK+ 自定義水冷）涼 **5°C**

**結論：目前所有測試過的 RTX 5090 中散熱能力第一。**（source: TPU docx）

---

## 幫浦設計（重要）

[[lightning-z]] 的幫浦**不會完全停轉**，原因：

> 若幫浦停轉，1000W 負載可在不到一秒內啟動，而幫浦重啟需要時間，期間溫度會瞬間飆高。

- 怠速：幫浦低速運轉（幾乎無聲）
- 風扇轉速超過 1,000 RPM 時：幫浦切換高速模式，增加冷卻液流量

（source: TPU docx）

---

## PCB 與 VRM 分析

- **GPU VRM**：33 相
  - 控制器：2x Monolithic Power Systems MP29816
  - 功率級：Monolithic MP87661 DrMOS，每相額定 60A
- **記憶體 VRM**：7 相（同樣由第二顆 MP29816 管理）
  - 功率級：同樣為 MP87661 DrMOS
- **GDDR7 記憶體晶片**：Samsung K4VAF325ZC-SC28，額定 28 Gbps

（source: TPU docx）

---

## 功耗模式詳見 [[gpu-tgp-power-modes]]

- 預設（OC）：800W
- Extreme BIOS：1,000W
- **2500W BIOS**（需自行取得）：實際移除功耗上限，**使用即喪失保固**，適合極限超頻且需改裝散熱
- 雙接頭負載分攤：800W 模式各 400W；1000W 模式各 500W
- **禁止只插一條接頭**（原廠不支援）

---

## 電源供應器建議

| 使用模式 | 最低 PSU 建議 |
|---|---|
| 預設 OC（800W） | 1,000W |
| Extreme（1,000W） | 1,200W |
| 含高功耗 CPU（如 Ryzen 9 / i9） | 更高 |

（source: TPU docx）

---

## 物理規格

| 項目 | 數值 |
|---|---|
| 尺寸 | 26.0 × 15.0 cm |
| 重量（卡本體） | 2,955g |
| 重量（含散熱器） | 4,094g |
| 寬度（插槽） | 60mm（3 槽） |
| 顯示輸出 | 3x DisplayPort 2.1b + 2x HDMI 2.1b |
| USB-C 埠 | 1x（僅供 8 吋面板連接主機板，不可接外部裝置） |

（source: TPU docx）

---

## 優缺點總結（TPU）

**優點**：雙 16-pin 供電、最高 1000W、大幅工廠超頻、比 FE 快 10%、出色電壓控制、安靜涼快（尤其 800W）、DLSS 4、8 吋 LCD、良好 OC 空間、32GB VRAM、能效高、風扇停轉、雙 BIOS、PCIe 5.0

**缺點**：售價極高（$5,090）、散熱器需要機殼額外空間、幫浦持續運轉、怠速/多螢幕/影片播放耗電偏高、驅動人為限制記憶體 OC 上限

（source: TPU docx）

---

## Related pages

- [[lightning-z]]
- [[lightning-z-benchlife]]
- [[rtx-5090]]
- [[gpu-aio-cooler]]
- [[12v-2x6-connector]]
- [[gpu-tgp-power-modes]]
