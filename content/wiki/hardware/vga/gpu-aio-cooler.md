# GPU 一體式水冷（AIO Cooler）

**Summary**：GPU AIO（All-In-One）是將幫浦、水冷排、風扇整合為一體的封閉式液冷方案，近年開始應用於高 TGP 旗艦顯示卡，提供優於空冷的散熱能力，但對機殼空間有額外需求。

**Sources**：
- `benchlife - 360mm 一體式水冷、2x 12V-2×6 接頭與 800W 功耗，MSI GeForce RTX 5090 Lightning Z 實測.docx`
- `TPU _ MSI GeForce RTX 5090 Lightning Z Review - Up to 1000 W.docx`
- `igorslab - MSI GeForce RTX 5090 Lightning Z review – Lightning-fast and thirsty unicorn in battle against NVIDIA's clock speed barriers.docx`

**Last updated**：2026-04-24

---

## 基本原理

AIO 液冷由以下元件組成：

1. **水冷頭（Water Block）**：直接接觸 GPU 晶片（及記憶體、MOSFET）導熱
2. **幫浦（Pump）**：驅動冷卻液循環，通常整合於水冷頭內
3. **水冷排（Radiator）**：將熱量散逸至空氣，尺寸常見 240mm / 360mm
4. **風扇**：裝於水冷排，強制氣流通過鰭片

相較於空冷，AIO 的優勢在於可將熱量導至機殼遠端（水冷排安裝位），適合高 TGP 場景。

---

## GPU AIO 的應用現況

傳統上 AIO 主要用於 CPU 散熱。近年高功耗 GPU（TGP 超過 400W）促使 AIB 廠商為旗艦顯示卡採用 AIO：

- **[[lightning-z]]**：360mm AIO，由 Cooler Master 代工，銅底全覆蓋 GPU + GDDR7 + MOSFET（source: benchlife docx）
- 新一代幫浦馬達、混合式高低密度鰭片設計（source: benchlife docx）

---

## 幫浦行為設計（重要）

[[lightning-z]] 的幫浦**不會完全停轉**（no pump stop），這與常見的 CPU AIO 不同。

原因（MSI 說明）：1000W 負載可在不到一秒內啟動，若幫浦從停轉到重啟需要時間，這段延遲會導致溫度瞬間飆高。（source: TPU docx）

**幫浦運作模式**：
- 怠速：低速持續運轉（幾乎無聲）
- 風扇轉速超過 1,000 RPM 時：切換高速模式，增加冷卻液流量

---

## 散熱性能數據（Lightning Z 實測）

### 溫度與噪音

| 模式 | GPU 溫度 | GDDR7 溫度 | 噪音 | 風扇轉速 |
|---|---|---|---|---|
| 800W (OC) | 64°C | 66°C | **29.4 dBA** | 1,281 RPM |
| 1000W (Extreme) | 66°C | 66°C | 36.2 dBA | 1,562 RPM |

（source: TPU docx）

### 噪音標準化測試（35 dBA 等噪條件，TPU 特有測試方法）

在完全相同的 35 dBA 噪音條件下，Lightning Z 散熱器：

| 對比 | 溫差 |
|---|---|
| vs RTX 5090 FE | 涼 **31°C** |
| vs 一般優秀空冷 RTX 5090 | 涼 **15°C** |
| vs 頂級客製水冷（LYNK+） | 涼 **5°C** |

**目前所有 TPU 測試過的 RTX 5090 中，散熱能力第一。**（source: TPU docx）

---

## RTX 5090 各版本散熱比較

| 卡型 | 散熱方案 | 遊戲 GPU 溫度 | 噪音 |
|---|---|---|---|
| NVIDIA RTX 5090 FE | 雙風扇空冷 | 77°C | 40.1 dBA |
| ASUS RTX 5090 TUF | 三風扇空冷 | 66°C | 41.1 dBA |
| MSI RTX 5090 Suprim SOC | 三風扇空冷 | 75°C | 28.4 dBA |
| ASUS RTX 5090 Astral LC | AIO 液冷 | 58°C | 35.9 dBA |
| MSI RTX 5090 Suprim Liquid SOC | AIO 液冷 | 61°C | 31.2 dBA |
| LYNK+ RTX 5090 | 客製水冷 | 56°C | 32.5 dBA |
| **MSI RTX 5090 Lightning Z（800W）** | **360mm AIO** | **64°C** | **29.4 dBA** |

（source: TPU docx）

---

## 機殼相容性考量

採用 AIO 顯示卡的機殼需要：

- 足夠的 360mm 水冷排安裝位（通常為機殼頂部或前方）
- 足夠的走線空間（水管長度與彎曲半徑）
- 若 CPU 已使用 AIO，需有空間安裝第二個水冷排

benchlife 評測中隱晦指出這是選購時的注意事項：「只是機殼的選擇就…」（source: benchlife docx）
TPU 亦指出：「若 CPU 也用水冷，需要一個能安裝兩組水冷排的機殼。」（source: TPU docx）

---

## 水冷排與冷頭材質（igorslab LIBS 分析）

igorslab 對 Lightning Z 的 AIO 元件進行了 LIBS 元素分析（source: igorslab review docx）：

| 元件 | 材質 | 說明 |
|---|---|---|
| 水冷頭（冷板） | **純銅，無鍍鎳** | 最高導熱係數（~400 W/mK）；無額外鍍層熱阻；封閉水路中氧化問題可忽略 |
| 360mm 水冷排散熱片 | **Al-Si 鋁合金** | 並非銅；輕量、成本優，靠大面積和細鰭片補償導熱劣勢；需要內部防腐塗層 |

**鋁製水冷排的工程邏輯**：鋁的導熱係數低於銅，但在 360mm 大面積散熱器中，熱阻主要由對流決定（風扇風量），材料本身的傳導路徑很短，鋁的劣勢可被大面積抵消。鋁重量更輕、壁厚可做更薄（增加散熱面積）。

---

## 幫浦噪音問題（igorslab 補充發現）

igorslab 發現一個 TPU 未特別指出的噪音現象（source: igorslab review docx）：

**問題**：負載下降時，風扇轉速比幫浦轉速降得更快，導致幫浦聲音在過渡期間相對突出。

| 噪音特性 | 說明 |
|---|---|
| 頻率 | **~3 kHz**（幫浦機械噪音）|
| 主觀感受 | 遠比 dB(A) 數字更刺耳——3 kHz 剛好是人耳靈敏度最高的頻段 |
| 觸發時機 | 遊戲場景切換、幀率驟降、幕後任務突然中止等造成負載快速下降時 |
| 持續時間 | 短暫，風扇轉速與幫浦轉速趨近後消失 |

這解釋了為何 Lightning Z 的 dB(A) 數字（35.6–38 dB(A)）在合理範圍，主觀噪音感受卻比同 dB(A) 等級的空冷方案更容易引起注意。

---

## 水管設計

[[lightning-z]] 採用「東出」（右出）水管設計，且將所有額外線材包覆於水管束內，使顯示卡外觀更為簡潔。風扇由顯示卡直接供電控制，**不需要額外連接主機板排針**。（source: benchlife docx, TPU docx）

---

## Related pages

- [[lightning-z]]
- [[lightning-z-benchlife]]
- [[lightning-z-tpu]]
- [[lightning-z-igorslab-review]]
- [[thermal-interface-materials]]
- [[rtx-5090]]
- [[gpu-tgp-power-modes]]
