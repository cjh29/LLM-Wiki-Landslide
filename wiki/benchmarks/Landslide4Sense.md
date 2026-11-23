# Landslide4Sense

- **主辦單位/發起人**：IEEE GRSS Data Fusion Contest 衍生競賽 / Ghorbanzadeh et al. (2022)
- **任務類型**：語意分割 (Semantic Segmentation)
- **影像切片尺寸**：128 × 128 像素 (空間解析度統一重採樣至 10m/pixel)
- **總樣本規模**：4,844 幅切片 (官方標準劃分：訓練集 3,799 幅、驗證集 245 幅、測試集 800 幅)
- **輸入通道數 (14 通道)**：
  - [[Sentinel-2]] 12 個光學波段 (B1, B2, B3, B4, B5, B6, B7, B8, B9, B10, B11, B12)
  - [[DEM & Slope]] (ALOS PALSAR 衍生之高程與坡度，各 1 通道)

---

## 1. 資料集特性與挑戰
* **極端類別不平衡 (Severe Class Imbalance)**：全資料集崩塌像素僅佔 **2.2%**，其餘 97.8% 均為非崩塌背景。
* **地理分層策略 (Leave-out-location)**：涵蓋日本膽振 (地震)、尼泊爾 (地震)、印度 (暴雨) 與台灣台東 (颱風暴雨)。

---

## 2. 【核心學術爭鳴】：資料集劃分協定與空間資料洩漏 (Spatial Leakage)
在文獻回顧與橫向比較時，必須嚴格區分以下兩種完全不同的評估標準：
1. **官方標準「留出地點（Leave-out-location）」劃分**：
   * 測試集 800 幅切片來自完全獨立未重疊的地理區塊，嚴格考驗模型的跨域泛化能力，絕無相鄰空間洩漏。
2. **非官方隨機 70/30 劃分**（如 [[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model|Hou et al. (2024)]] 原文設定）：
   * 直接在 3,799 幅切片中隨機抽取 30% 作為測試。由於同一滑坡事件周遭切片彼此相鄰，強烈的空間自相關（Spatial Autocorrelation）導致模型在測試時實質「看過」周遭地貌，造成高達 **84.46% F1** 的虛高假象。
   * **Li et al. (2025) 官方重測結果**：當使用官方標準劃分重新評估 TransLandSeg 時，其真實 F1 暴跌至 **55.39%**！

---

## 3. 官方標準測試集排行榜 (Official Benchmark Leaderboard)

| 排名 | 模型與架構範式 | 模態與通道配置 | mIoU (%) | F1-score (%) | 決策門檻 (t) | 來源文獻 | 關鍵備註 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | **[[Prithvi]]-EO-2.0-300M (GeoFM 主骨幹)** | 14 波段 + U-Net Adapter (Full) | **73.62** | **65.42** | 0.50 | Li et al. (2025) | 14 波段全融合 SOTA；非線性 Adapter 表現卓越 |
| **2** | **Hybrid U-Net + [[Clay]] (混合瓶頸架構)** | 14 波段 (U-Net) + 12 光學波段 (Clay, LoRA 兩階段) | - | **64.5 ± 1.8** (最高 64.93) | **0.78** | Vu (2026) | **混合架構第一名**；Precision 達 62.2%，大幅抑制假警報 |
| 3 | Hybrid U-Net + [[Clay]] (凍結瓶頸) | 同上，Clay 凍結 | - | 61.70 | 0.70 | Vu (2026) | 零微調 GFM 注入瓶頸即超越純 U-Net |
| 4 | **[[Prithvi]]-EO-2.0-300M** | 6 波段 (HLS Baseline, Lovász) | **71.30** | **60.70** | 0.50 | Li et al. (2025) | 6 波段純光學最高分 |
| 5 | **U-Net (競賽 Baseline)** | 14 波段 (直接拼接, CE 5000 epochs) | 70.44 | 59.90 (59.66) | 0.50 | 競賽官方 / Vu (2026) | 傳統卷積基準 (過度分割，Recall > Prec) |
| 6 | SegFormer | 6 波段 (wCE) | 69.89 | 59.12 | 0.50 | Li et al. (2025) | 廣域注意力機制 |
| 7 | **[[Prithvi]]-EO-2.0-600M** | 6 波段 (wCE) | 70.41 | 58.56 | 0.50 | Li et al. (2025) | 1.25% 極限少樣本下最強 |
| 8 | SwinV2-B | 6 波段 (wCE) | 67.77 | 58.28 | 0.50 | Li et al. (2025) | Recall 全場最高 (76.4%) 但 Precision 僅 47.1% |
| 9 | TerraMind (base) | 6 波段 (focal) | 69.77 | 58.12 | 0.50 | Li et al. (2025) | Precision 達 65.95% |
| 10 | U-Net++ | 6 波段 (wCE) | 68.96 | 57.89 | 0.50 | Li et al. (2025) | - |
| 11 | **TransLandSeg (SAM + ATL)** | **RGB-only (BCE+Dice)** | **68.30** | **55.39** *(自述隨機切分 84.46)* | 0.50 | Li et al. (2025) 重測 | 缺少多光譜與高程線索，官方測試集表現受限 |
| 12 | **[[Clay]] v1.5 (獨立主骨幹 + 地形殘差)** | 12 波段 (LoRA) + 2 地形 (多尺度殘差) | - | 55.2 ± 3.6 | 0.60 | Vu (2026) | 純 ViT 缺少 Skip Connections，落後 U-Net 4.7 pp |
| 13 | SatMAE (Multi-Spectral) | 6 波段 (wCE) | 67.48 | 53.72 | 0.50 | Li et al. (2025) | 表徵適應力較弱 |

---

## 4. 關聯論文文獻 (Referenced Papers)
* [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]]
* [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]]
* [[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model|Hou et al. (2024)]]
