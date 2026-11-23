# TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model

- **作者**：Changhong Hou, Junchuan Yu, Daqing Ge, Liu Yang, Laidian Xi, Yunxuan Pang, Yi Wen
- **發表年份**：2024
- **發表期刊/會議/預印本**：arXiv:2403.10127v1 [cs.CV]
- **DOI / 連結**：https://arxiv.org/abs/2403.10127
- **關聯模型**：[[SAM & VFM Adaptation]], [[Prithvi]], [[Clay]]
- **關聯感測器**：[[Sentinel-2]]
- **關聯基準**：[[Bijie Dataset]], [[Landslide4Sense]], [[IoU & F1-score]]

---

## 1. 執行摘要 (Executive Summary)
本研究代表了將電腦視覺領域大型視覺基礎模型（Vision Foundation Model, VFM，以 Meta 的 **Segment Anything Model, SAM** 為核心）遷移至遙測崩塌地偵測的代表性探索。作者發現，未經適配的原始 SAM 在崩塌地影像上的 Zero-shot 與提示點（Point Prompts）分割表現低落，主因在於 SA-1B 預訓練資料庫完全缺乏遙測與地質影像。為此，本篇提出了 **自適應遷移學習架構（Adaptive Transfer Learning, ATL）**，在完全凍結 SAM ViT-L 主骨幹的前提下，**僅微調 1.3% 的參數量（4.18M / 312.45M）**。實驗顯示，在 0.8m 超高解析度光學影像的 [[Bijie Dataset]] 上，TransLandSeg 取得高達 **93.41% F1 / 88.10% MIoU** 的卓越表現。但在中解析度（10m）的 [[Landslide4Sense]] 上，因其僅能接收 RGB 三波段，後續被文獻證實受限於光譜特徵貧瘠，且引發了遙測資料集「隨機切片 vs 地理留出」評估協定的重大爭鳴。

---

## 2. 視覺基礎模型架構與微調機制 (VFM Architecture & ATL Adaptation)
- **基礎模型**：Meta SAM (ViT-L/16)，包含 24 層 Transformer Blocks（14×14 視窗注意力 + 4 個全域注意力區塊），總參數量約 312.45M。
- **微調策略：參數高效微調 (PEFT / Adapter-Tuning)**：
  - **完全凍結 (Frozen)**：SAM 的圖像 Patch Embedding 與所有 24 層 ViT Transformer 編碼器。
  - **可訓練參數 (Trainable, 僅佔 1.3%)**：輕量級 Mask Decoder 以及各層之間插入的 **ATL (Adaptive Transfer Learning)** 適配層。
- **ATL 結構設計與消融結論**：
  - 採用瓶頸式結構：降維層 $W_{\text{down}} \in \mathbb{R}^{M \times d}$ $\to$ 非線性中間層 MidLay $\to$ 升維層 $W_{\text{up}} \in \mathbb{R}^{d \times M}$（$d \ll M$）。
  - **殘差連接的核心重要性**：消融實驗（Table IV）證實，去除 ATL 的殘差連接（$\Omega(X) = \Theta(X) + X$）會使 F1 暴跌 10 個百分點以上，殘差結構是防止預訓練全域特徵退化的基石。
  - **最佳配置**：將 ATL 部署於 Transformer Block **外部**，搭配 1 層全連接與 GELU 的 `MidLay_m`，性能顯著優於部署在 Block 內部或疊加多層卷積的中間層。
- **損失函數**：BCE Loss + Dice Loss，採用 AdamW 優化器微調 50 個 Epochs。

---

## 3. 資料來源與感測器配置 (Data & Sensor Setup)
- **輸入影像規格**：因應 SAM 架構，影像調整為 $1024 \times 1024$ 像素，**僅使用 RGB 3 通道**。
- **評估資料集**：
  1. **[[Bijie Dataset]]（畢節資料集）**：
     * 感測器：北京二號（TripleSat）光學衛星，空間解析度高達 **0.8m (VHR)**。
     * 規模：貴州畢節市 770 幅崩塌影像，裸露滑坡面紋理細緻，適合視覺幾何輪廓提取。
  2. **[[Landslide4Sense]]**：
     * 感測器：Sentinel-2（10m 解析度），僅截取 RGB 3 通道，經雙線性插值放大至 $1024 \times 1024$。

---

## 4. 實驗設計、評估數據與【學術評估爭鳴】

### A. 畢節高解析度資料集 (Bijie, 0.8m) — VFM 全面碾壓
| 模型 | Precision (%) | Recall (%) | F1-score (%) | OA (%) | MIoU (%) | Landslide-IoU (%) |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| CCNet | 85.24 | 83.64 | 84.43 | 94.49 | 75.09 | 56.11 |
| UNet | 85.81 | 84.77 | 85.29 | 94.75 | 76.20 | 58.06 |
| Deeplabv3+ | 89.19 | 87.40 | 88.29 | 95.85 | 80.28 | 65.06 |
| PSPNet | 90.77 | 86.39 | 88.53 | 96.03 | 80.52 | 65.32 |
| **TransLandSeg (Ours)** | **93.14** | **93.69** | **93.41** | **97.60** | **88.10** | **78.83** |

*結論*：在 0.8m 超高解析度下，SAM 的通用語意輪廓先驗大放異彩，F1 較傳統卷積模型提升 4.88%~8.98%，能精確分割細微邊界與被植被部分遮蔽的滑坡。

### B. Landslide4Sense (10m) 評估 — 【重大資料集切割爭鳴】
1. **TransLandSeg 本文自述成績（採用 70/30 隨機切割）**：
   * Precision: 84.99% / Recall: 83.93% / **F1: 84.46%** / MIoU: 75.99% / Landslide-IoU: 53.41%。
2. **官方標準「留出地點 (Leave-out-location)」重測成績（[[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al., 2025]]）**：
   * 在杜絕空間洩漏的競賽官方測試集上，TransLandSeg (RGB-only) 重新訓練實測僅取得：**68.30% mIoU / 55.39% F1**（Precision 55.23%, Recall 55.55%）。
3. **文獻辯證洞見**：
   * 遙測切片若採隨機切割，相鄰切片存在強烈空間自相關（Spatial Autocorrelation），導致模型在訓練集「看過」相同崩塌地周遭特徵而產生虛高分數（84.46% vs 55.39%）。
   * 在嚴格跨地理測試下，SAM 因**缺乏紅邊、NIR、SWIR 與 DEM 地形通道**，在 10m 解析度下無法區分光譜相似裸土，表現顯著落後於原生多光譜 GeoFM（[[Prithvi]] 60.7%~65.4%、[[Clay]] 64.5%）。

---

## 5. 核心洞見與未解研究缺口 (Insights & Research Gaps)
- **參數高效微調的實用價值**：證實了 Adapter-tuning 能在凍結 98.7% 權重下實現大模型快速收斂，為算力受限場景提供解法。
- **自然影像 VFM 的根本局限（光譜盲區 Spectral Blindness）**：SAM 原生為 RGB 影像設計，難以直接整合多光譜與高程物理波段；強行放大至 $1024 \times 1024$ 亦徒增運算負擔。
- **遙測評估標準化的迫切性**：本篇的成績分歧確立了遙測領域必須採用嚴格地理隔離（Spatially Disjoint / Leave-out-location）測試集的重要性。

---

## 6. 參考文獻引用 (BibTeX)
```bibtex
@article{hou2024translandseg,
  title={TransLandSeg: A transfer learning approach for landslide semantic segmentation based on vision foundation model},
  author={Hou, Changhong and Yu, Junchuan and Ge, Daqing and Yang, Liu and Xi, Laidian and Pang, Yunxuan and Wen, Yi},
  journal={arXiv preprint arXiv:2403.10127},
  year={2024}
}
```
