# 12V-2×6 PCIe 供電接頭

**Summary**：12V-2×6 是 PCIe 5.0 時代的新一代 GPU 供電接頭標準，取代舊版 12VHPWR（16-pin），改善了因插入不完全導致的過熱與燒毀問題，設計上支援高達 600W 以上的持續功耗。

**Sources**：
- `benchlife - 360mm 一體式水冷、2x 12V-2×6 接頭與 800W 功耗，MSI GeForce RTX 5090 Lightning Z 實測.docx`
- `TPU _ MSI GeForce RTX 5090 Lightning Z Review - Up to 1000 W.docx`
- `igorslab - Eye for solder eyes, tooth for chattering teeth_ What's really going on with the "solder joint gate" of the MSI RTX 5090 Lightning Z_.docx`

**Last updated**：2026-04-24

---

## 規格

| 項目 | 12V-2×6 |
|---|---|
| 接腳數 | 12+2（共 14 pin） |
| 最大功率 | 單接頭約 600W（官方） |
| 對應標準 | PCIe 5.0 CEM 5.1 |
| 感測腳位 | 2 個感測針腳，驗證插入是否完全 |

---

## 與舊版 12VHPWR 的差異

12VHPWR（12+4 pin）出現在 RTX 4090 時代，因插入不完全導致過熱燒毀的事故引發廣泛關注。12V-2×6 是改良版：

- 感測針腳縮短，確保完全插入才能正常供電
- 連接器公差更嚴格
- 俗稱「12VHPWR 改版」或「16-pin v2」

---

## 在 RTX 5090 上的應用

[[rtx-5090]] 的高階 AIB 版本普遍採用單顆或雙顆 12V-2×6：

- **[[lightning-z]]**：2x 12V-2×6，預設 TGP 800W，最高可達 1,000W

### 雙接頭的負載分攤優勢

使用兩條 12V-2×6 接頭的設計，讓每條接頭承受的電流更低，接頭針腳溫度更低，可靠性更高：

| 功耗模式 | 每條接頭負載 | 相比單接頭標準（600W）|
|---|---|---|
| 800W (OC) | **400W** | 減少 33% |
| 1000W (Extreme) | **500W** | 減少 17% |

TPU 說明：「這讓每條接頭只需承擔 400W（1000W 模式下為 500W），比標準 RTX 5090 的單接頭少 100W。」（source: TPU docx）

### 使用限制

- **禁止只插一條接頭**：TPU 明確指出原廠不支援單接頭運作（source: TPU docx）
- 兩條皆需具備 600W 供電能力

---

## BTF GC-HPWR 說明

BTF（Back To Front）是 ASUS 的背插式供電設計，GC-HPWR 為其 GPU 供電模組，可搭配相容主機板實現無外露線材的供電。與 12V-2×6 相比屬於不同生態，需要特定的電源供應器與機殼配合。

*BTF 詳細資料待後續 ingest 補充*

---

---

## 焊點品質觀察（Lightning Z 案例）

igorslab 針對 [[lightning-z]] 的「焊點門」進行分析，揭示 12V-2×6 針腳類型對焊點缺陷的不同風險程度（source: igorslab solder docx）：

| 針腳類型 | 冗餘路徑 | 焊點不良影響 |
|---|---|---|
| GND（接地腳） | 高（PCIe 插槽 GND 並聯 + 多條平行 GND） | 通常不影響功能 |
| 12V（供電腳） | 中（多條平行供電腳，但無外部並聯） | 可能導致電流不對稱，長期有可靠性疑慮 |

實測結論：Lightning Z 可疑針腳的接觸電阻與其他針腳相同，電流分配對稱，功能正常。

詳見 [[tht-solder-quality]] 及 [[lightning-z-igorslab-solder]]。

---

## Related pages

- [[lightning-z]]
- [[lightning-z-igorslab-solder]]
- [[tht-solder-quality]]
- [[rtx-5090]]
- [[gpu-tgp-power-modes]]
