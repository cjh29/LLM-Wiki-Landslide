# Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability

- **作者**：Wenwen Li, Sizhe Wang, Hyunho Lee, Chenyan Lu, Sujit Roy, Rahul Ramachandran, Chia-Yu Hsu
- **發表年份**：2025
- **發表期刊/會議/預印本**：arXiv:2511.04474v1 [cs.CV]
- **DOI / 連結**：https://arxiv.org/abs/2511.04474
- **關聯模型**：[[Prithvi]], [[SatMAE]], [[TerraMind]]
- **關聯感測器**：[[Sentinel-2]], [[DEM & Slope]]
- **關聯基準**：[[Landslide4Sense]], [[IoU & F1-score]]

---

## 1. 執行摘要 (Executive Summary)
本研究為首篇系統性評估地球觀測基礎模型（Geospatial Foundation Model, GeoFM）在崩塌地空間辨識任務應用的先驅文獻。作者團隊提出了針對感測器（Sensor）、標籤（Label）與領域（Domain）的三維評估架構，以 NASA 與 IBM 聯合研發的 **[[Prithvi]]-EO-2.0** 為核心，全面評估其在波段適應性（Band Adaptability）、標籤稀缺少樣本學習能力（Few-Shot Data Efficiency）與跨地理/跨資料集泛化性（Cross-domain Generalizability）。實驗證明，Prithvi-EO-2.0 在各項指標上均顯著超越傳統任務專屬 CNNs（如 U-Net, U-Net++）、通用 Vision Transformers（如 SegFormer, SwinV2-B）以及其他 GeoFMs（如 SatMAE, TerraMind），奠定了 GeoFM 在崩塌地災害調查與應變中的核心優勢。

---

## 2. 地球基礎模型架構與微調機制 (Foundation Model & Adaptation)
- **基礎模型**：[[Prithvi]]-EO-2.0（分別評估了 300M 與 600M 兩種模型參數量規模）。
- **Backbone 架構**：Vision Transformer (ViT)，具備時空與地理經緯度位置嵌入（Spatio-temporal & Location Embeddings）。
- **預訓練策略**：於 NASA 420 萬筆 Harmonized Landsat-Sentinel-2 (HLS) 30m 多光譜時空影像上進行 3D Masked Autoencoder (MAE) 自監督預訓練。
- **下游微調架構**：
  - Encoder 採用預訓練 Prithvi 權重（評估了 Frozen 與 Full fine-tuning 兩種策略）。
  - Decoder 採用輕量級卷積解碼器（FCN Decoder，由 Conv2D 與 ConvTranspose2D 構成）。
  - **輸入波段適應機制 (Band Adaptation)**：當輸入通道數不等於預訓練 6 波段（B2, B3, B4, B8A, B11, B12）時，設計了 **線性映射 (Linear Projection)** 與 **非線性 U-Net Adapter** 兩種結構將多波段/少波段特徵映射回 6 通道空間。
- **損失函數比較**：採用加權交叉熵 (wCE, 權重比 2:8)、Focal Loss 與 Lovász-Softmax，其中 Lovász-Softmax 在 IoU 直接優化上表現最為優異。

---

## 3. 資料來源與感測器配置 (Data & Sensor Setup)
- **主要評估感測器**：
  - 光學多光譜：[[Sentinel-2]]（包含可見光、紅邊波段 B5-7、近紅外 B8、短波紅外 B11-12、水氣 B9 等）。
  - 地形高程因子：[[DEM & Slope]]（由 ALOS PALSAR 數值高程模型衍生）。
- **任務模式**：單期事後影像語意分割（Post-event Semantic Segmentation, $T=1$）。
- **測試與驗證資料集**：
  1. **[[Landslide4Sense]]**：4,844 幅切片（128×128，重採樣至 10m），涵蓋日本北海道地震、尼泊爾地震、印度暴雨及台灣台東颱風暴雨誘發崩塌。背景與崩塌比為 97.8% : 2.2%（極端不平衡）。
  2. **Landslide Reference Dataset**：具備精確經緯度之全球崩塌資料集，包含未見區域一般化測試子集（44 樣本）。
  3. **GVLM-S2**：獨立自 Global Very-High-Resolution Landslide Mapping (GVLM) 元資料下載之外部 Sentinel-2 驗證集（11 個完全獨立地域，共 56 樣本）。

---

## 4. 實驗設計與評估效能 (Experiments & Performance)

### A. 全資料基準評比 (Landslide4Sense 測試集，100% 資料，6 波段)
| 模型類別 | 具體模型 | 損失函數 | mIoU (%) | F1-score (%) | Precision (%) | Recall (%) | mAcc (%) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **Task-specific CNN** | U-Net (ResNet-50) | wCE | 70.44 | 59.66 | 56.17 | 63.63 | 98.37 |
| **Task-specific CNN** | U-Net++ (ResNet-50) | wCE | 68.96 | 57.89 | 50.19 | 68.38 | 98.03 |
| **Vision Transformer** | SegFormer | wCE | 69.89 | 59.12 | 49.51 | 73.37 | - |
| **Vision Transformer** | SwinV2-B | wCE | 67.77 | 58.28 | 47.09 | **76.43** | 97.88 |
| **VFM (Natural RGB)** | TransLandSeg (SAM) | BCE+Dice | 68.30 | 55.39 | 55.23 | 55.55 | 98.31 |
| **GeoFM** | SatMAE (Multi-Spectral) | wCE | 67.48 | 53.72 | 54.00 | 53.44 | 98.26 |
| **GeoFM** | TerraMind (base) | focal | 69.77 | 58.12 | **65.95** | 51.95 | **98.59** |
| **GeoFM (Ours)** | **Prithvi-EO-2.0-300M** | Lovász | **71.30** | **60.70** | 57.14 | 64.73 | 98.42 |
| **GeoFM (Ours)** | **Prithvi-EO-2.0-600M** | wCE | 70.41 | 58.56 | 51.07 | 68.61 | 98.17 |

### B. 波段適應性實驗 (Band Adaptability on Landslide4Sense)
- **14 波段全融合 (S2 + DEM/Slope)**：Prithvi-300M (U-Net Adapter + Full Fine-tuning) 創下 **73.62% mIoU / 65.42% F1** 的全場最高紀錄；對比傳統 U-Net 輸入 14 波段時發生過擬合崩潰（mIoU 暴跌至 61.20%）。
- **Adapter 設計比較**：U-Net Adapter（非線性局部空間聚合）全面優於 Linear Projection，有效抑制了 Precision/Recall 的病態偏移。
- **波段順序與物理語意**：基於互資訊（Mutual Information, MI）挑選的 6 波段（如結合紅邊/SWIR/DEM）可達 70.64% mIoU；但若將預訓練 6 波段順序隨機打亂（HLS Shuffled），F1 暴跌至 39.17%，證實模型強烈依賴光譜物理語意對齊。

### C. 標籤稀缺少樣本實驗 (Few-Shot Data Efficiency)
- 在僅使用 **1.25% 標籤（約 50 個訓練樣本）** 的極限情境下：
  - **Prithvi-600M 達到 66.96% mIoU**（保留 95.1% 全資料表現，資料效率指標 DE ≈ 0.97）。
  - Prithvi-300M 為 64.45% mIoU。
  - U-Net 降至 59.70%、U-Net++ 降至 55.50%（GeoFM 具備 7–12% mIoU 顯著優勢）。

### D. 跨領域與地理泛化性 (Cross-Domain Generalizability)
- **Landslide Reference 未見測試集 (Generalizability Split)**：
  - Prithvi-600M: **86.03% mIoU**（vs. U-Net 65.73%、U-Net++ 67.59%）。
- **外部獨立資料集 (GVLM-S2 External Set)**：
  - Prithvi-300M: **71.04% mIoU**、Prithvi-600M: **70.75% mIoU**。
  - U-Net 降至 53.09%、U-Net++ 降至 48.61%（呈現災難性跨域衰退）。

---

## 5. 核心洞見、技術挑戰與研究缺口 (Insights & Research Gaps)
- **關鍵貢獻**：
  1. 首度建立 GeoFM 在崩塌地辨識的三維分析架構（感測器、標籤、領域）。
  2. 證實了 EO 原生預訓練（相較於 ImageNet 或 SAM 等自然影像預訓練）在多光譜特徵複用與長尾少樣本場景下的根本優越性。
- **觀察到的限制與未解研究缺口 (Research Gaps)**：
  1. **崩塌地質型態對泛化性的制約甚於單純地理跨度**：在測試集中，愛爾蘭的泥炭地崩塌（Peat Landslides）因地貌與光譜特性特殊，所有模型表現均顯著低落，顯示崩塌地內在型態（Geomorphic Type）是跨域泛化的重大屏障。
  2. **現存公開資料集缺乏 AI-ready 時空元資料**：多數基準資料集（如 Landslide4Sense）缺乏精確拍攝經緯度與時間戳記，使 Prithvi 內建的時空位置嵌入（Location & Temporal Embeddings）無法發揮全部潛能。
  3. **落地計算開銷與運作化缺口**：300M/600M 模型微調計算成本高昂，且單期語意分割仍有 26% 錯判率；未來需結合災前/災後雙期影像（Bi-temporal Change Detection）與參數高效微調（如 Visual Prompt Tuning）方能真正落地即時減災。

---

## 6. 參考文獻引用 (BibTeX)
```bibtex
@article{li2025landslide,
  title={Landslide Hazard Mapping with Geospatial Foundation Models: Geographical Generalizability, Data Scarcity, and Band Adaptability},
  author={Li, Wenwen and Wang, Sizhe and Lee, Hyunho and Lu, Chenyan and Roy, Sujit and Ramachandran, Rahul and Hsu, Chia-Yu},
  journal={arXiv preprint arXiv:2511.04474},
  year={2025}
}
```
