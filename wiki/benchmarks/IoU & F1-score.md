# IoU & F1-score (評估指標)

- **適用領域**：影像語意分割 (Semantic Segmentation)、崩塌地偵測 (Landslide Hazard Mapping)
- **核心指標群**：Intersection over Union (IoU / Jaccard Index)、F1-score (Dice Coefficient)、Precision (精確率)、Recall (召回率)、Overall Accuracy (OA / mAcc)

---

## 1. 崩塌偵測情境下的數學定義
設 $TP$ 為正判崩塌像素、$FP$ 為平地誤判為崩塌像素（假警報）、$FN$ 為崩塌漏判像素（漏檢）：
* **IoU (Jaccard Index)**：
  $$\text{IoU} = \frac{TP}{TP + FP + FN}$$
  mIoU 則為各類別（背景與崩塌）IoU 之平均值，為不平衡影像分割最關鍵的指標。
* **F1-score (Harmonic Mean)**：
  $$\text{Precision} = \frac{TP}{TP + FP}, \quad \text{Recall} = \frac{TP}{TP + FN}$$
  $$\text{F1} = \frac{2 \times \text{Precision} \times \text{Recall}}{\text{Precision} + \text{Recall}}$$

---

## 2. 崩塌地極端不平衡下的指標陷阱與實證現象
依據 [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]] 與 [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]] 的共同洞見：

1. **mAcc (平均準確率) 的嚴重失真**：
   * 在崩塌像素僅佔 2% 的資料集（如 [[Landslide4Sense]]）中，模型將全部像素判為背景即可獲得 98% 的準確率。mAcc 完全無法衡量真實減災能力。
2. **決策門檻漂移 (Decision Boundary Shift)**：
   * 極端類別不平衡會導致模型輸出的後驗機率分佈向低數值擠壓。雖然 ROC-AUC 可達 0.989，但**最佳 F1 決策門檻並非預設的 0.50，而是大幅向右漂移至 $t = 0.78 \sim 0.80$**。
   * *實證案例*：在 Vu (2026) 的 Hybrid U-Net + Clay 模型中，若固定門檻 $t=0.50$，F1 立即由 64.5% 崩跌至 54.8%（Precision 由 62.2% 暴跌至 40.0%）。
3. **模型架構與 Precision / Recall 的結構性偏好**：
   * **傳統 CNN (如 U-Net)**：具備局部空間先驗，但光譜表徵能力弱，傾向過度預測（Recall 偏高、Precision 偏低，假警報多）。
   * **純 Vision Transformer (如 SwinV2-B, Clay Standalone)**：全域注意力範圍廣，但缺少多尺度 Skip Connections，邊界模糊，誤將廣大平原裸土視為崩塌（SwinV2-B 的 Precision 僅 47.1%）。
   * **GFM-CNN Hybrid 架構 (如 U-Net + Clay)**：CNN 提供精確邊界，GFM 提供全域光譜與地貌語意抑噪，成功將 Precision 拉抬至 62.2%，達成最佳平衡。

---

## 3. 不確定性量化 (Uncertainty Quantification)
* **Monte Carlo Dropout (MC Dropout)**：
  * 在 20 次隨機推理下，不確定性標準差 ($\sigma$) 表現為：
    $$\sigma_{\text{TN}} (0.0017) < \sigma_{\text{TP}} (0.0351) < \sigma_{\text{FN}} (0.0431) \approx \sigma_{\text{FP}} (0.0450)$$
  * 模型對「預測錯誤處（FP 與 FN）」的不確定性顯著高於「正確預測處（TN 與 TP）」，且高不確定性像素密集分佈於崩塌冠部邊界與微小崩塌碎屑，具備極佳的災情現場可信度標註能力。

---

## 4. 關聯論文文獻 (Referenced Papers)
* [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]]
* [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]]
