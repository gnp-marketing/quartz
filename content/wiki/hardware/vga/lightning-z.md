# MSI GeForce RTX 5090 Lightning Z

**Summary**：MSI GeForce RTX 5090 Lightning Z 主條目，整合各評測來源的資訊，為 Lightning 系列自 RTX 2080 Ti 後的回歸之作，採用 360mm AIO 水冷，全球限量 1,300 張，售價 $5,090 USD。

**Sources**：
- `benchlife - 360mm 一體式水冷、2x 12V-2×6 接頭與 800W 功耗，MSI GeForce RTX 5090 Lightning Z 實測.docx`
- `TPU _ MSI GeForce RTX 5090 Lightning Z Review - Up to 1000 W.docx`
- `igorslab - Eye for solder eyes, tooth for chattering teeth_ What's really going on with the "solder joint gate" of the MSI RTX 5090 Lightning Z_.docx`
- `igorslab - MSI GeForce RTX 5090 Lightning Z review – Lightning-fast and thirsty unicorn in battle against NVIDIA's clock speed barriers.docx`

**Last updated**：2026-04-24



---

## 產品定位

- **GPU 核心**：NVIDIA [[rtx-5090]]（Blackwell GB202）
- **系列**：MSI Lightning（上一代為 GeForce RTX 2080 Ti Lightning Z，時隔多代回歸）
- **發售日期**：2026-02-12
- **全球限量**：1,300 張
- **售價**：$5,090 USD（數字刻意對應型號；RTX 5090 MSRP 的 2.5 倍）（source: TPU docx）
- **定位**：MSI 旗艦中的旗艦，高於 Suprim Liquid SOC 與 Gaming Trio；競品為 ASUS ROG Matrix Platinum 與 GIGABYTE AORUS Infinity

---

## 完整規格

| 項目 | 規格 |
|---|---|
| GPU | GeForce RTX 5090（Blackwell GB202） |
| 記憶體 | 32GB Samsung GDDR7（K4VAF325ZC-SC28，28 Gbps） |
| 記憶體頻寬 | 1,792 GB/s |
| 散熱 | 360mm [[gpu-aio-cooler]]（Cooler Master 代工） |
| 供電接頭 | 2x [[12v-2x6-connector]] |
| VRM | 33 相（GPU）+ 7 相（記憶體）；MP29816 控制器 + MP87661 DrMOS 60A |
| 預設 Boost 時脈 | 2,730 MHz（原廠超頻，vs 參考版 2,407 MHz） |
| 預設 TGP（OC 模式） | 800W |
| 最大 TGP（EXTREME 模式） | 1,000W |
| 顯示面板 | 8 吋整合式 LCD |
| vBIOS | 雙 vBIOS：OC（800W）/ EXTREME（1,000W）；另有 2500W BIOS（喪失保固） |
| 風扇 | 3x 120mm Lightning 風扇（水冷排） |
| 顯示輸出 | 3x DisplayPort 2.1b + 2x HDMI 2.1b |
| 尺寸 | 26.0 × 15.0 cm，60mm 寬（3 槽） |
| 重量 | 2,955g（卡）/ 4,094g（含散熱器） |
| 附件 | PCIe 5.0 Riser Cable + 直立支架 |

（source: TPU docx, benchlife docx）

---

## 效能表現

TPU 實測（無 RT、無 DLSS）：

| 對比卡 | Lightning Z 相對優勢 |
|---|---|
| RTX 5090 Founders Edition | **+10%**（相當於「RTX 5090 Ti」水準） |
| ASUS RTX 5090 Matrix（BTF 800W） | 稍快 |
| RTX 4090 | +約 50% |
| AMD RX 9070 XT | 超過 2 倍 |

（source: TPU docx）

效能增益來自三者協同：原廠大幅超頻（+323 MHz）+ 預設 800W 高功耗上限 + AIO 散熱提供更大 Boost 空間。

---

## 散熱設計

採用 [[gpu-aio-cooler]] 設計，為 Lightning 系列首度捨棄空冷。

- 水冷塊由 Cooler Master 代工，銅底全覆蓋 GPU、GDDR7 記憶體、MOSFET
- 新一代馬達幫浦，**不會完全停轉**（設計如此，防止 1000W 瞬間啟動導致溫度飆升）
- 360mm 水冷排採混合式高低密度鰭片設計
- 水管「東出」設計，外部線材全部包覆，視覺整潔

### 實測散熱數據

| 來源 | 模式 | GPU 溫度 | GDDR7 溫度 | 噪音 | 風扇轉速 |
|---|---|---|---|---|---|
| TPU | 800W (OC) | 64°C | 66°C | **29.4 dBA** | 1,281 RPM |
| TPU | 1000W (Extreme) | 66°C | 66°C | 36.2 dBA | 1,562 RPM |
| igorslab | OC（Cyberpunk 全載） | **~64°C** | — | **35.6 dB(A)** | — |
| igorslab | Extreme（Cyberpunk 全載） | **~61°C** | — | 最高 **38 dB(A)** | — |

TPU 噪音標準化測試（35 dBA 等噪條件）：比 RTX 5090 FE 涼 **31°C**，目前所有測試過的 RTX 5090 中**散熱能力第一**。（source: TPU docx）

igorslab 發現：**AIO 水冷比同廠風冷 SUPRIM（~78°C）涼 14–17K**，即使功耗更高。（source: igorslab review docx）

**噪音特殊問題**：負載下降時風扇比幫浦轉速降得更快，幫浦的 **~3 kHz 音調**在過渡期間浮現——此頻率剛好是人耳最敏感頻段，主觀感受比 dB(A) 數字更刺耳。（source: igorslab review docx）

---

## PCB 與 VRM 架構

詳見 [[gpu-vrm-architecture]]。igorslab 拆解重點（source: igorslab review docx）：

- **40 相** VRM，8 層 PCB，3 oz 銅
- NVVDD（GPU 核心）：**26 相有效相數**，2x MP29816-A PWM 控制器 + MPS2522 並聯功率級拓撲
- uP1666 電流比較器：硬體層獨立監控 2 條外接 12V + PCIe slot 的過電流保護
- 散熱材料：GPU die 使用無矽相變 PTM pad（>6.3 W/mK）；詳見 [[thermal-interface-materials]]
- **背板碳纖維紋路為貼膜**，實際為 Al-Si 壓鑄合金（83% Al + 16-17% Si）

---

## 功耗模式

詳見 [[gpu-tgp-power-modes]]。

| 模式 | TGP | GPU 時脈（中位數） | GPU 溫度 | 風扇轉速 |
|---|---|---|---|---|
| OC（預設） | 800W | 2,722 MHz | 66.8°C | 1,446 RPM |
| EXTREME | 1,000W | 2,895 MHz | 72.0°C | 1,866 RPM |

**跨來源一致結論**：兩種模式效能差異在 ±3% 以內（benchlife），TPU 稱「只快 1%」。建議使用預設 OC 模式。

---

## 超頻潛力

- 手動超頻可達平均 **3,218 MHz**，幀率 177.8 FPS
- 搭配 MSI Afterburner 電壓控制（NVVDD / FBVDD / MSVDD / PEXVDD）可達 180.2 FPS
- 全 RTX 5090 系列超頻後最快（source: TPU docx）
- 注意：NVIDIA 驅動限制 GDDR7 OC 上限 +375 MHz

### OC 效率遞減（igorslab 量化分析）

igorslab 在 Cyberpunk 2077 4K Overdrive DLAA 全載下量測各 OC 配置（source: igorslab review docx）：

| 設定 | 平均時脈 | 相對 SUPRIM 時脈增幅 | 相對 SUPRIM FPS 增幅 |
|---|---|---|---|
| SUPRIM（參考） | ~2.65 GHz | — | — |
| Lightning Z OC | ~3.06 GHz | +15% | ~+7% |
| Lightning Z Extreme | 略低於 OC | — | 相近 |
| Lightning Z Extreme +200 | **~3.28 GHz** | +24% | **~+11%** |

**核心結論**：時脈增加 24% 只帶來 11% FPS 增益；每額外一步 OC 的 FPS/GHz 效率持續下降，而功耗峰值突破 1,040W 持續。DLSS 的效益（30fps → 70fps+）遠超所有 OC 方案。

---

## 8 吋顯示面板（FlashDash）

- 整合於顯示卡上蓋，出現在 OS 中作為第二螢幕
- 透過 **MSI Lightning Hub**（網頁介面）的 FlashDash 功能管理
- 可顯示 GPU/CPU 即時監控資訊、自訂影片或任何桌面內容
- 需安裝後台軟體連接 MSI 雲端服務，方可透過 Web 控制
- 隨附 PCIe 5.0 Riser Cable 與直立支架

（source: benchlife docx, TPU docx）

---

## 製造品質爭議：焊點門

上市後社群流傳 [[12v-2x6-connector]] 供電區域部分針腳焊點外觀異常，引發「焊點門」（Solder Joint Gate）討論。

igorslab 作者以親持樣品進行電氣實測與製程分析（source: igorslab solder docx）：

| 項目 | 結果 |
|---|---|
| 可疑 12V 針腳接觸電阻 | 與其他針腳相同，正常 |
| 電流分配 | 對稱，無異常 |
| 高負載局部溫度 | 無過熱點 |
| 整卡穩定性 | 正常 |

**製程解釋**：Lightning Z 與 Suprim 共用 MS-V530 平台及 Palwonn Electronics PCB 廠，但 Lightning Z 屬小批量生產，可能使用選擇性焊接製程，導致焊點外觀較不均勻，電氣品質仍在規格內。

**結論**：功能正常，外觀不符 $5,090 USD 旗艦定位預期。詳見 [[lightning-z-igorslab-solder]]。

---

## 評測評分彙整

| 媒體 | 語言 | 評分/結論 |
|---|---|---|
| benchlife | 繁體中文 | Silver 推薦 |
| TechPowerUp | 英文 | 強力推薦（效能 +10% vs FE，散熱第一，但售價極高） |
| igorslab（完整評測） | 英文 | 「工程奇蹟級獨角獸」——技術無可挑剔，但售價超出實際附加價值，定性為收藏品 |
| igorslab（焊點分析） | 英文 | 功能正常，但製造外觀有待改善 |

---

## 與同級競品比較

| 項目 | MSI RTX 5090 Lightning Z | ASUS ROG Matrix Platinum | GIGABYTE AORUS Infinity |
|---|---|---|---|
| 散熱方案 | 360mm AIO | 3+1 風扇空冷 | 待補充 |
| 供電接頭 | 2x [[12v-2x6-connector]] | BTF GC-HPWR | 待補充 |
| 預設 TGP | 800W | 800W | 待補充 |
| 原廠 Boost 時脈 | 2,730 MHz | 待補充 | 待補充 |
| 特殊功能 | 8 吋 LCD | 30 週年紀念版設計 | 待補充 |
| 售價 | $5,090 USD | 待補充 | 待補充 |

（source: benchlife docx, TPU docx）

---

## Related pages

- [[lightning-z-benchlife]]
- [[lightning-z-tpu]]
- [[lightning-z-igorslab-review]]
- [[lightning-z-igorslab-solder]]
- [[rtx-5090]]
- [[gpu-aio-cooler]]
- [[12v-2x6-connector]]
- [[gpu-vrm-architecture]]
- [[thermal-interface-materials]]
- [[tht-solder-quality]]
- [[gpu-tgp-power-modes]]
