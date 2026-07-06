# GPU VRM 架構

**Summary**：GPU 電壓調節模組（VRM）的多相架構原理，包含 PWM 控制器、智慧型功率級（Smart Power Stage）、並聯相位拓撲，以及過電流保護電路設計，以 MSI RTX 5090 Lightning Z 為具體案例。

**Sources**：
- `igorslab - MSI GeForce RTX 5090 Lightning Z review – Lightning-fast and thirsty unicorn in battle against NVIDIA's clock speed barriers.docx`
- `TPU _ MSI GeForce RTX 5090 Lightning Z Review - Up to 1000 W.docx`

**Last updated**：2026-04-24

---

## 基本概念

VRM（Voltage Regulator Module，電壓調節模組）負責將輸入的 12V 電壓轉換為 GPU 各功能域所需的精確工作電壓（通常 0.7–1.1V）。GPU 的 VRM 核心由以下元件構成：

- **PWM 控制器**：產生開關信號，控制相位切換節奏與輸出電壓
- **功率級（Power Stage / DrMOS）**：包含高側 MOSFET、低側 MOSFET 與閘極驅動器，執行實際的開關動作
- **電感（Inductor）**：平滑化開關引起的電流漣波
- **電容（Capacitor）**：提供局部儲能緩衝，應對瞬間負載變化

---

## 供電域分離（以 Lightning Z 為例）

高階 GPU 會將不同功能域的供電分開，避免互相干擾：

| 供電域 | 功能 | 相數（Lightning Z） |
|---|---|---|
| **NVVDD** | GPU 計算核心（Shader、Tensor、RT Core、Cache） | **26 相** |
| **MSVDD** | GPU 內部輔助邏輯（PLL、I/O、控制電路） | 4 相 |
| **FBVDD** | 記憶體介面 PHY 與記憶體控制器 | 4 相 |

- NVVDD 和 MSVDD 各自由一顆 MP29816-A PWM 控制器負責
- FBVDD 與 GDDR7 記憶體供電共用第二顆 MP29816-A（因兩者負載特性相近）

（source: igorslab review docx）

---

## 多相並聯拓撲（Lightning Z 的 26 相設計）

Lightning Z 的 26 個 NVVDD 相位**不是**使用傳統的時移相位倍增器（phase doubler IC），而是採用並聯功率級拓撲：

1. 一顆 MP29816-A 提供若干條真正的 PWM 通道（例如 13 條）
2. 每條 PWM 通道同時驅動 **2 個 MPS2522 功率級**
3. 兩個 MPS2522 接收相同 PWM 信號，各自有獨立電感和電流路徑
4. 由於 MPS2522 內建電流感測，兩者各自回報電流值給 PWM 控制器
5. 控制器偵測不均衡時主動補償，無需外部分流電阻（shunt resistor）

**與相位倍增器的差異**：

| | 時移 Phase Doubler | Lightning Z 並聯拓撲 |
|---|---|---|
| 切換頻率 | 等效加倍 | 維持原頻率 |
| 相位移 | 有（錯開觸發） | 無（同步觸發） |
| 電流分散 | 靠時間交錯 | 靠空間並聯 |
| 電流感測 | 依賴外部電路 | MPS2522 內建，精確 |

**好處**：
- 每個功率級承載電流更低，切換損耗更小
- 熱量分散更均勻，避免局部熱點
- 控制邏輯更直接，無額外倍增器的信號延遲

（source: igorslab review docx）

---

## 關鍵元件

### MP29816-A（Monolithic Power Systems PWM 控制器）

- 多相 PWM 控制器，Lightning Z 使用兩顆
- 一顆專責 NVVDD 核心電壓
- 另一顆負責 GDDR7 記憶體 + FBVDD

### MPS2522（Smart Power Stage）

- 整合高側 MOSFET + 低側 MOSFET + 閘極驅動器 + **電流感測**
- TPU 評測引用型號為 **MP87661 DrMOS 60A**（source: TPU docx）
- 每個功率級獨立回報電流，支援精確負載平衡

### uP1666（電流比較器）

- 硬體層過電流監控模組，完全獨立於 BIOS 與驅動程式
- 持續量測跨 shunt 電阻的電壓降，計算三條供電路徑的實際電流：
  - 外接 12V 接頭 A
  - 外接 12V 接頭 B
  - PCIe slot 的 12V（75W 路徑）
- 任一路徑超過設定上限，立即輸出保護信號（功耗限制或硬關機）
- 佈局在 shunt 電阻附近，縮短量測路徑，最小化誤差與干擾

（source: igorslab review docx）

---

## 輸入濾波

在 [[12v-2x6-connector]] 接頭後方的輸入濾波電路：

- **大型電感**：低通濾波，抑制電源供應器或轉換器開關行為產生的高頻干擾
- **低阻抗分流電阻（shunt）**：精確量測輸入電流，供保護功能與遙測使用
- PCIe slot 端也有較小的電感 + 電容組合，防止 GPU 負載峰值直接耦合回主機板

---

## 相數與效能的關係

更多 VRM 相數的工程意義：

1. **每相電流更低**：降低每個功率級的切換損耗與自發熱
2. **熱量分散更均勻**：避免少數功率級過熱形成局部熱點
3. **快速負載響應**：多相交錯（或並聯）使輸出電流漣波更小，電壓更穩定
4. **高持續電流能力**：對 [[lightning-z]] 這類 800–1000W 持續負載卡片至關重要

---

## Related pages

- [[lightning-z]]
- [[lightning-z-igorslab-review]]
- [[12v-2x6-connector]]
- [[gpu-tgp-power-modes]]
