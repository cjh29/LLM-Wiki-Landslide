# Bijie Dataset (畢節高解析度崩塌地資料集)

- **發布機構/來源**：TripleSat (北京二號) 遙測衛星光學影像
- **地理位置**：中國貴州省畢節市 (Bijie City, Guizhou Province, China)
- **空間解析度**：**0.8 m (超高空間解析度 VHR)**
- **影像波段**：RGB 可見光波段
- **樣本規模**：770 幅崩塌切片 (包含清晰的滑坡體標記)
- **地貌地質環境**：典型喀斯特侵蝕山區，地形起伏劇烈，崩塌多表現為基岩出露與植被剝離。

---

## 1. 資料集科學價值與特性
* **超高解析度幾何紋理**：相較於 Sentinel-2 的 10m 解析度，0.8m 的畢節資料集呈現了細微的滑坡後壁冠部（Scarp）、側向擦痕與堆積體紋理，是評估模型**「局部邊界細節重建能力」**的黃金基準。
* **VFM 與 CNN 表現的試金石**：在這種紋理豐富的 RGB 影像上，自然影像視覺基礎模型（如 [[SAM & VFM Adaptation|SAM]]）能最大化發揮其預訓練輪廓優勢。

---

## 2. 基準效能排行榜 (Benchmark Results)
依據 [[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model|Hou et al. (2024)]] 評測：

| 排名 | 模型架構 | Precision (%) | Recall (%) | F1-score (%) | MIoU (%) | 備註 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | **TransLandSeg (SAM + ATL)** | **93.14** | **93.69** | **93.41** | **88.10** | **超高解析度下 SOTA** (微調 1.3% 參數) |
| 2 | PSPNet | 90.77 | 86.39 | 88.53 | 80.52 | 金字塔池化多尺度表現良好 |
| 3 | Deeplabv3+ | 89.19 | 87.40 | 88.29 | 80.28 | 空洞卷積擴展感受野 |
| 4 | UNet | 85.81 | 84.77 | 85.29 | 76.20 | 傳統卷積基準 |
| 5 | CCNet | 85.24 | 83.64 | 84.43 | 75.09 | 十字交叉注意力 |

---

## 3. 關聯論文文獻 (Referenced Papers)
* [[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model|Hou et al. (2024)]]
