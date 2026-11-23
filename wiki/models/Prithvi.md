# Prithvi (Geospatial Foundation Model)

- **研發團隊/機構**：NASA & IBM Research
- **模型家族**：Prithvi-EO-1.0 (100M，僅美國本土預訓練)、Prithvi-EO-2.0 (300M, 600M)、Prithvi-Weather-Climate
- **Backbone 架構**：Vision Transformer (300M 採用 ViT-L，600M 採用 ViT-H)
- **預訓練機制**：3D Spatio-Temporal Masked Autoencoder (MAE)
- **預訓練資料庫 (4.2M Global HLS)**：
  - 涵蓋 2014–2023 年全球 10 年間 NASA Harmonized Landsat-Sentinel-2 (HLS) 30m 影像。
  - 基於 Copernicus Land Cover 100m 與 RESOLVE 846 個生態區進行全球代表性分層去偏採樣（升採樣城鎮建築區與高熵地貌，抑制沙漠與海洋），形成 3,156 個瓦片、420 萬筆 $4 \times 256 \times 256$ 時空序列切片。
- **核心光學波段**：6 個標準光學通道（B2 Blue, B3 Green, B4 Red, Narrow NIR-B8A, SWIR1-B11, SWIR2-B12）。
- **關鍵架構創新**：
  1. **3D 卷積補丁嵌入 (3D Patch Embeddings)**：在時空立方體（$t=1, h=16, w=16$ 或 $14 \times 14$）上建立連續時序注意力機制。
  2. **時空經緯度元資料偏差嵌入 (Location & Temporal Embeddings)**：將中心經緯度與採樣年份/日（DOY）以 2D sin/cos 正餘弦編碼，以可學習加權係數注入 Token，並設計 0.1 隨機丟棄（Dropout）機制，確保無元資料時仍具泛化性。
- **微調生態庫**：官方建立並開源 **TerraTorch** 微調工具鏈（基於 PyTorch Lightning 與 TorchGeo）。

---

## 1. 在崩塌地偵測領域之實證表現 (Landslide Detection Applications)
依據官方報告 [[Prithvi-EO-2.0 - A Versatile Multi-Temporal Foundation Model for Earth Observation Applications|Szwarcman et al. (2024)]] 與系統評測 [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]]：
1. **全樣本基準 (Landslide4Sense 100% 數據)**：
   * **Prithvi-300M (Lovász loss)**：取得 **71.30% mIoU / 60.70% F1**（Precision 57.14%, Recall 64.73%），全面超越同場 U-Net（70.44% mIoU / 59.66% F1）與 U-Net++（68.96% mIoU）。
   * **Prithvi-600M (wCE)**：繳出 70.41% mIoU / 58.56% F1，但具備全場極佳的 Recall（68.61%）。
2. **多波段全融合極限 (14 波段全融合)**：
   * 搭配淺層卷積非線性 U-Net Adapter 與端到端微調，Prithvi-300M 創下 **73.62% mIoU / 65.42% F1** 的最高紀錄。
3. **極限少樣本表現 (2.5% 與 1.25% 數據)**：
   * 在 2.5% 標註數據（僅 100 樣本）下，**Prithvi-600M 展現最高韌性，mIoU 達 67.1%**（相較於全資料僅微跌 3.3 pp，反觀 U-Net 暴跌至 65.1%）。
   * 在 1.25% 標註數據（約 50 樣本）下，Prithvi-600M 依然保有 **66.96% mIoU**（保留 95.1% 全資料表現，資料效率指標 DE ≈ 0.97）。
4. **跨領域與跨資料集泛化**：
   * 在完全外部獨立之 GVLM-S2 資料集上，Prithvi-300M 與 600M 穩守在 **71% mIoU**，而傳統卷積 Baseline 則災難性暴跌至 48%~53%。

---

## 2. 現存局限與挑戰
* 純 ViT 架構在缺乏多尺度跳躍連接時，若不加外掛 Adapter 或輔助卷積，單獨作為主骨幹對細微崩塌邊界的像素級分割存在局限（引發了 [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]] 採用 Hybrid 混合架構的爭鳴）。
* 參數量大（300M / 600M），微調時需耗費較高 GPU 資源。

---

## 3. 關聯論文文獻 (Referenced Papers)
* [[Prithvi-EO-2.0 - A Versatile Multi-Temporal Foundation Model for Earth Observation Applications|Szwarcman et al. (2024)]] — 官方技術報告，詳細規範預訓練採樣與架構細節。
* [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]] — 針對波段、標籤、領域三維度系統評測 Prithvi 在崩塌地的適應性。
* [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]] — 對比 Prithvi 與 Hybrid U-Net + Clay 架構。
