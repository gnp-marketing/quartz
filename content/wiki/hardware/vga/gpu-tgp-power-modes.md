# GPU TGP 與功耗模式

**Summary**：TGP（Total Graphics Power）是 GPU 在正常運作下允許的最大功耗上限，AIB 廠商通常透過 vBIOS 提供多種功耗模式供使用者切換，以平衡效能、溫度與噪音。

**Sources**：
- `benchlife - 360mm 一體式水冷、2x 12V-2×6 接頭與 800W 功耗，MSI GeForce RTX 5090 Lightning Z 實測.docx`
- `TPU _ MSI GeForce RTX 5090 Lightning Z Review - Up to 1000 W.docx`

**Last updated**：2026-04-24

---

## TGP 定義

TGP（Total Graphics Power）代表顯示卡在滿載時允許消耗的總電功率上限，包含 GPU 晶片、記憶體、供電電路等所有元件的耗電總和。

- TGP 越高，GPU 時脈牆越高，理論效能上限越高
- TGP 越高，散熱需求、電源供應器需求也越高
- AIB 版 TGP 通常高於 NVIDIA 公版參考設計

---

## vBIOS 功耗模式

高階 AIB 版本常提供雙 vBIOS 開關，讓使用者切換不同的功耗設定：

### MSI RTX 5090 Lightning Z 實例

| 模式 | TGP | GPU 時脈（中位數） | GPU 溫度（中位數） | 風扇轉速（中位數） |
|---|---|---|---|---|
| OC（預設） | 800W | 2,722 MHz | 66.8°C | 1,446 RPM |
| EXTREME | 1,000W | 2,895 MHz | 72.0°C | 1,866 RPM |

（source: benchlife docx）

### 2500W BIOS（極限超頻專用）

TPU 透露 [[lightning-z]] 尚有第三個 2500W BIOS 可取得，實際效果是**移除功耗上限**，供極限超頻玩家使用。

- **使用即喪失保固**
- 可能需要改裝散熱系統
- 非一般使用者使用情境（source: TPU docx）

---

## 關鍵發現：高 TGP 不等於高效能提升（雙來源確認）

**從 800W 提升至 1,000W（+200W / +25%），實際遊戲效能提升幾乎為零。** 此結論獲兩份獨立評測印證：

- benchlife：3DMark 與 9 款 4K 遊戲全部落在 ±3% 範圍內（source: benchlife docx）
- TechPowerUp：「只要切換開關，在原廠狀態下快 1%」；補充說明大多數遊戲不會長時間跑到 1000W 持續功耗，但瞬間爆發負載可受益（source: TPU docx）

**代價（800W → 1000W）**：
- GPU 溫度上升 5.2°C
- GDDR7 溫度上升 2.0°C
- 風扇轉速提高 420 RPM（更吵）
- 多耗電 200W

**結論**：對 [[rtx-5090]] 這一級別的 GPU，TGP 800W 已足夠發揮完整效能，EXTREME 模式只是「數字遊戲」。

---

## 為什麼高 TGP 效能不明顯？

GPU 效能受多重因素限制（時脈牆、記憶體頻寬、溫度限制等），當 TGP 已超過 GPU 的實際需求，多餘的功耗預算主要只換來極小幅度的時脈提升，邊際效益遞減。[[rtx-5090]] 在 800W 條件下已接近架構本身的性能極限。

TPU 的補充觀點：雖然大多數遊戲不跑到 1000W，但某些瞬間爆發場景（不到一秒的高負載）確實可以從較高的功耗上限中受益，這是 EXTREME 模式的唯一實際意義。（source: TPU docx）

---

## 電源供應器建議（針對 Lightning Z）

| 使用模式 | 最低 PSU 建議 |
|---|---|
| 預設 OC（800W） | 1,000W |
| EXTREME（1,000W） | 1,200W |
| 搭配高功耗 CPU | 視 CPU TDP 再加 |

（source: TPU docx）

---

## Related pages

- [[lightning-z]]
- [[lightning-z-benchlife]]
- [[lightning-z-tpu]]
- [[rtx-5090]]
- [[12v-2x6-connector]]
- [[gpu-aio-cooler]]
