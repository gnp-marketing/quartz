# MSI RTX 5090 Lightning Z 完整評測（igorslab）

**Summary**：igorslab 對 MSI RTX 5090 Lightning Z 的深度全評測，涵蓋 PCB 拆解、LIBS 材料分析、多遊戲基準測試、功耗峰值量測、散熱與噪音分析，結論定性為「工程奇蹟級獨角獸」。

**Sources**：
- `igorslab - MSI GeForce RTX 5090 Lightning Z review – Lightning-fast and thirsty unicorn in battle against NVIDIA's clock speed barriers.docx`

**Last updated**：2026-04-24

---

## 測試平台

| 元件 | 規格 |
|---|---|
| CPU | AMD Ryzen 9850X3D（輕微降壓超頻） |
| 主機板 | ASRock OC Formula |
| 記憶體 | 2x16GB T-Force Delta RGB DDR5-6000 CL30 |
| PSU | MSI MPG Ai1600TS 1600W |
| 熱像儀 | Optris Pi640（640×480 測量像素，最高 30 FPS）|
| 顯微鏡 | Keyence VHX-7000 + AE-300（最高 x2000，3D 輪廓儀）|
| TIM 測試儀 | TIMA5（工業標準）|
| 功耗量測 | 示波器 + 自製 MCU 量測裝置；NVIDIA PCAT |

---

## PCB 與 VRM 架構

詳細架構見 [[gpu-vrm-architecture]]。重點摘要：

- **40 相**總 VRM，8 層 PCB，3 oz 銅
- **NVVDD（GPU 核心）**：26 相有效相數，2x Monolithic Power Systems **MP29816-A** PWM 控制器
  - 每個 PWM 通道並聯 2x **MPS2522** 智慧型功率級（非時移 phase doubler）
  - MPS2522 各自有電流感測，不均衡時 PWM 控制器主動補償
- **MSVDD**（輔助邏輯）：4 相
- **FBVDD**（記憶體介面）：4 相
- **uP1666** 電流比較器：硬體層過電流保護，獨立監控 2 條外接 12V + PCIe slot

---

## 材料分析（LIBS 元素分析）

igorslab 使用 Keyence VHX-7000 顯微鏡進行雷射誘導崩潰光譜（LIBS）深度鑽孔分析。詳見 [[thermal-interface-materials]]。

| 部件 | 材料結論 | 備注 |
|---|---|---|
| 水冷頭 | **純銅，無鍍鎳** | 最高導熱；封閉水路氧化問題可忽略 |
| 360mm 水冷排散熱片 | **Al-Si 鋁合金** | 並非銅；輕量成本導向，靠大面積補償導熱 |
| 背板 | Al-Si 合金（83% Al，16-17% Si）+ 聚合物塗層 | **碳纖外觀僅為貼膜**，非真正碳纖維 |
| GPU die TIM | 無矽 PTM pad（Al/Zn 氧化物填充）| **>6.3 W/mK**；相變材料，優於大多數散熱膏 |
| VRM / GDDR7 一般導熱墊 | 矽膠基，**3.3 W/mK** | 中低端熱阻，功能足夠 |
| GDDR7 高性能導熱墊 | 無矽聚合物，**9.12 W/mK** | 乾燥油土質感；穩定不滲油，適應不均表面 |

---

## 風扇

型號：**Power Logic PLA12025S12H-4**

| 規格 | 數值 |
|---|---|
| 尺寸 | 120mm × 25mm |
| 電壓 | 12V DC |
| 電流 | 0.36A（≈ 4.3W） |
| 軸承 | H.Y. Bearing（廠商自有進階滑動軸承，優於一般 sleeve bearing） |

---

## BIOS 模式比較

| 模式 | TGP | 平均閒置功耗 | 平均 GPU Boost 時脈 |
|---|---|---|---|
| OC BIOS | 800W | ~45–50W | ~2,730 MHz |
| Extreme BIOS | 1,000W | **<15W**（更積極閒置省電） | ~2,730 MHz（基準相同，電氣空間更大） |

Extreme BIOS 並非傳統超頻 profile，而是解鎖更大電氣空間供手動或工具進一步調整。

---

## 基準測試結論

測試遊戲：Cyberpunk 2077（Overdrive DLAA / DLSS Quality）、Black Myth: Wukong（Cinema DLAA / DLSS Quality）、Borderlands 4（Badass DLAA）

測試卡：SUPRIM、Lightning Z OC、Lightning Z Extreme、Lightning Z Extreme +200MHz、RTX 5090 FE、RTX 4090 各版本

### 核心結論（igorslab 反覆強調）

| 比較 | 效果 |
|---|---|
| DLAA → DLSS Quality | **根本性改變**：幀率 ~30fps → 70fps+，遊戲體驗本質不同 |
| 標準超頻 → 激進超頻 | **邊際效益**：幀率多 1–5%，功耗多 15–25%，效率明顯惡化 |
| RTX 4090 → RTX 5090 | **清晰世代差距**：帧時間穩定性與絕對性能均有顯著提升 |

### Cyberpunk 2077 4K Overdrive DLAA（最苛刻工況）

- 所有 RTX 5090 勉強維持 30fps 以上
- Lightning Z 各版本比 SUPRIM 高 4–11%
- 功耗卻高出 15–40%
- OC 不改變幀時間基本特性，只有常數偏移

### Borderlands 4（縮放最乾淨的測試）

- RTX 5090 家族間差距最清晰
- Lightning Z 系列最快，但 SUPRIM 在效率上表現更平衡

---

## 功耗峰值分析（>5ms 持續峰值，Cyberpunk DLAA 基準）

| 設定 | 平均功耗 | 持續峰值 |
|---|---|---|
| SUPRIM | ~600W | <700W |
| Lightning Z OC | ~755W | **>1,000W** |
| Lightning Z Extreme | 動態更大，均值相近 | >1,000W |
| Lightning Z Extreme +200MHz | 最高 | **>1,040W 持續** |

這些峰值並非毫秒級瞬變，而是功耗管理主動允許的真實持續工作點，對 PSU 和電源基礎設施有直接需求。

---

## 時脈 vs 效率分析

| 設定 | 平均 GPU 時脈 | 平均 FPS（Cyberpunk DLAA） | FPS / GHz 效率 |
|---|---|---|---|
| SUPRIM | ~2.65 GHz | 參考基準 | 最高 |
| Lightning Z OC | ~3.06 GHz | +~7% | 下降 |
| Lightning Z Extreme | 略低於 OC | 相近 | 略低 |
| Lightning Z Extreme +200 | **~3.28 GHz** | +~11% | 最低 |

**時脈增加 +24% → 幀率僅增加 +11%**：架構與工作負載限制（RT、記憶體頻寬瓶頸）使純頻率增益遞減明顯。

---

## 散熱比較（Cyberpunk DLAA 全負載）

| 設定 | 最高 GPU 溫度 |
|---|---|
| SUPRIM（風冷） | **~78°C** |
| Lightning Z OC（AIO） | **~64°C**（功耗更高，溫度卻低 14K）|
| Lightning Z Extreme | **~61°C** |
| Lightning Z Extreme +200 | **~61°C**（溫度不再是限制因素）|

AIO 水冷的熱緩衝提供充裕的 Boost 空間，這正是 Lightning Z 維持高時脈的基礎。

---

## 噪音測量

| 設定 | 噪音 |
|---|---|
| SUPRIM（風冷） | **36.4 dB(A)**，乾淨寬頻，無突出音調 |
| SUPRIM Liquid | **33.2 dB(A)**，最安靜 |
| Lightning Z OC | **35.6 dB(A)** |
| Lightning Z Extreme | 最高 **38 dB(A)** |

**特殊問題**：負載下降時風扇轉速比幫浦降得更快，幫浦的 ~3 kHz 音調浮現。3 kHz 剛好是人耳最敏感的頻段，主觀感受比 dB(A) 數字更明顯。

---

## 整合式顯示器（LCD 面板）

- 獨立 USB-C 供電，與 GPU PCIe bus 分離
- 透過 MSI Center 控制，可顯示溫度、時脈、使用率、功耗等即時資料
- 測試時 **Web 端設定介面不穩定**（連線中斷、設定遺失）
- Windows 使用技巧：在顯示設定中稍微錯位兩個螢幕，縮小游標跨螢幕觸發區域

---

## 拆解評價

igorslab 給予罕見的正面評價：

> 「不需要特殊工具，拆解順序幾乎不言自明。MSI 顯然在設計時考慮到了分析、維修和技術溯源性。」

---

## 結論引言

> "Lightning Z 是工程奇蹟與近乎完美的可行性研究……它是真正意義上的獨角獸：極稀少、極昂貴、技術上令人著迷，但遠離任何理性需求或效率。售價遠超實際附加價值，但使其成為收藏品與不妥協工程的宣言。"

---

## Related pages

- [[lightning-z]]
- [[gpu-vrm-architecture]]
- [[thermal-interface-materials]]
- [[gpu-aio-cooler]]
- [[12v-2x6-connector]]
- [[gpu-tgp-power-modes]]
- [[rtx-5090]]
