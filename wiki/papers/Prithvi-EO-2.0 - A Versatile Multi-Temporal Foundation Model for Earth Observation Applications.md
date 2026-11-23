# Prithvi-EO-2.0 - A Versatile Multi-Temporal Foundation Model for Earth Observation Applications

- **作者**：Daniela Szwarcman, Sujit Roy, Paolo Fraccaro, Þorsteinn Elí Gíslason, Benedikt Blumenstiel, Rinki Ghosal, Pedro Henrique de Oliveira, Joao Lucas de Sousa Almeida, Rocco Sedona, Yanghui Kang, Srija Chakraborty, Sizhe Wang, Ankur Kumar, Myscon Truong, Denys Godwin, Hyunho Lee, Chia-Yu Hsu, Ata Akbari Asanjan, Besart Mujeci, Trevor Keenan, Paulo Arevalo, Wenwen Li, Hamed Alemohammad, Pontus Olofsson, Christopher Hain, Robert Kennedy, Bianca Zadrozny, Gabriele Cavallaro, Campbell Watson, Manil Maskey, Rahul Ramachandran, Juan Bernabe Moreno
- **發表年份**：2024
- **發表期刊/會議/預印本**：arXiv:2412.02732v1 [cs.CV]
- **DOI / 連結**：https://arxiv.org/abs/2412.02732
- **關聯模型**：[[Prithvi]]
- **關聯感測器**：[[Sentinel-2]], Landsat-8/9 (HLS)
- **關聯基準**：GEO-Bench, [[Landslide4Sense]], Sen1Floods11, BioMassters, [[IoU & F1-score]]

---

## 1. 執行摘要 (Executive Summary)
本技術報告為 NASA 與 IBM 聯合研發的第二代地球觀測基礎模型 **[[Prithvi]]-EO-2.0** 的官方奠基性報告（Foundational Technical Report）。模型預訓練於 NASA 420 萬筆全球 10 年（2014–2023）Harmonized Landsat-Sentinel-2 (HLS) 30m 多時相切片。Prithvi-EO-2.0 首次在 ViT 架構中原生融入 3D 時空卷積補丁嵌入（3D Patch Embedding）與顯式的地理經緯度/時間日期正餘弦嵌入（Location & Temporal Embeddings），推出了 300M 與 600M 兩種模型量級。報告透過權威基準 GEO-Bench 以及橫跨災害應變（崩塌地偵測、洪水、野火疤痕）、土地覆蓋作物分類與生態系碳匯（AGBM, GPP）等多個專業領域下游任務進行了廣泛驗證，證實了地球觀測原生基礎模型在多光譜特徵複用與極限少樣本適應上的領先地位，並全面開源微調工具鏈 **TerraTorch**。

---

## 2. 基礎模型架構與預訓練規格 (Architecture & Pretraining Blueprint)
- **預訓練資料庫採樣策略 (Representative Global Sampling)**：
  * 基於 Copernicus Land Cover 100m 與 RESOLVE 846 個生態區，對全球 HLS 切片進行去偏抽樣（降採樣海洋與純沙漠，升採樣城鎮建築區與高熵異質景觀），最終形成 3,156 個訓練瓦片（共 420 萬個 $4 \times 256 \times 256$ 時空樣本，時相跨度 1–6 個月）。
  * 輸入通道包含 Landsat/Sentinel 共有的 6 個核心光學波段（B2 Blue, B3 Green, B4 Red, B8A Narrow NIR, B11 SWIR1, B12 SWIR2）。
- **架構核心創新**：
  * **3D 時空 MAE**：採用 3D 卷積進行立方體 Token 化（$t=1, h=16, w=16$ 或 $14 \times 14$），搭配 3D 正餘弦位置編碼。
  * **時空元資料偏置嵌入 (Metadata Bias Embeddings)**：將中心經緯度與採樣年份/一年中的第幾天（DOY）獨立轉為 2D sin/cos 編碼，以可學習純量權重加權注入 Patch Tokens，並引入 0.1 隨機丟棄（Dropout）機制，使模型具備應對無元資料輸入的自適應能力。
  * **計算規模**：在德國 Jülich 超算中心 JUWELS Booster（240 張 A100 GPU）訓練 400 Epochs（300M 耗費約 2.1 萬 GPU 小時，600M 耗費約 5.8 萬 GPU 小時）。
- **微調生態系統**：推出專為地球觀測基礎模型打造的開源工具庫 **TerraTorch**（整合 PyTorch Lightning 與 TorchGeo），提供即插即用的語意分割解碼器與超參數優化工具。

---

## 3. 崩塌地偵測實證評估 (Landslide Detection on Landslide4Sense)
在報告第 4.2.1 節中，官方評估了 Prithvi-EO-2.0 在 [[Landslide4Sense]] 官方劃分（3,799 訓練 / 245 驗證 / 800 測試切片）上的基準表現：
- **微調配置**：輸入影像雙線性插值至 $224 \times 224$ 以匹配 Backbone；解碼器採用 4 層反卷積（Deconvolution）+ FCN；評估了加權交叉熵 (wCE 2:8) 與 Lovász Loss。

### A. 全量訓練資料表現 (100% Data / 3,799 樣本)
| 模型 | 損失函數 | mIoU (%) | F1-score (%) | Precision (%) | Recall (%) | 像素準確率 aAcc (%) | 實證特性 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| U-Net (ResNet-50) | wCE | 70.4 | 59.7 | 56.2 | 63.6 | 98.4 | 傳統 CNN 基準，假警報偏高 |
| U-Net++ (ResNet-50)| wCE | 69.0 | 57.0 | 50.2 | 68.4 | 98.0 | 過度分割顯著 |
| **Prithvi-EO-2.0-300M** | **Lovász** | **71.3** | **60.7** | **57.1** | **64.7** | **98.4** | **整體最佳平衡**；有效消除假警報 |
| **Prithvi-EO-2.0-600M** | wCE | 70.4 | 58.6 | 51.1 | **68.6** | 98.2 | 召回率最高，大容量表徵強 |

### B. 極限少樣本學習能力 (2.5% Data / 僅 100 樣本)
| 模型 | 損失函數 | mIoU (%) | F1-score (%) | Precision (%) | Recall (%) | aAcc (%) | mIoU 衰減幅度 (越低越抗跌) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| U-Net (ResNet-50) | wCE | 65.1 | 49.1 | 41.7 | 60.0 | 97.7 | 暴跌 5.3 pp |
| U-Net++ (ResNet-50)| wCE | 65.5 | 50.2 | 40.9 | 65.2 | 97.5 | 暴跌 3.5 pp |
| Prithvi-EO-2.0-300M | Lovász | 66.6 | 49.8 | **56.8** | 44.4 | 98.3 | 暴跌 4.7 pp，Precision 保持最高 |
| **Prithvi-EO-2.0-600M** | **wCE** | **67.1** | **49.1** | 54.3 | 44.8 | **98.3** | **僅微跌 3.3 pp (最抗跌)** |

---

## 4. 核心洞見與對本知識庫的貢獻
- **確立權威 Source of Truth**：作為第一篇（Li et al., 2025）論文評測的母體模型，本報告提供了精確的預訓練架構參數、波段規格與 2.5% 少樣本基準。
- **參數量規模帶來的抗衰減韌性**：實驗證明將模型擴展至 600M 在全樣本下精度提升有限（與 300M 相當），但在極端缺乏標註（僅 100 幅影像）的緊急災害場景下，**600M 大模型憑藉深層先驗展現出最高的穩定性與抗跌能力**。

---

## 5. 參考文獻引用 (BibTeX)
```bibtex
@article{szwarcman2024prithvi,
  title={Prithvi-EO-2.0: A versatile multi-temporal foundation model for earth observation applications},
  author={Szwarcman, Daniela and Roy, Sujit and Fraccaro, Paolo and G{\'\i}slason, {\TH}orsteinn El{\'\i} and Blumenstiel, Benedikt and Ghosal, Rinki and de Oliveira, Pedro Henrique and de Sousa Almeida, Joao Lucas and Sedona, Rocco and Kang, Yanghui and others},
  journal={arXiv preprint arXiv:2412.02732},
  year={2024}
}
```
