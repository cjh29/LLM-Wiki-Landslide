# Clay (Geospatial Foundation Model)

- **研發機構/團隊**：Clay Foundation
- **核心模型**：Clay v1.5
- **Backbone 架構**：Vision Transformer (ViT-Large，24 Transformer blocks，Embedding dim 1024)
- **參數量**：約 339M (3.39 億參數)
- **預訓練策略**：Masked Autoencoding (MAE) 自監督預訓練
- **預訓練資料規模**：超過 7,000 萬筆地球觀測切片（涵蓋多種感測器如 Sentinel-1, Sentinel-2, Landsat, PlanetScope，全球多季節覆蓋）
- **核心設計亮點**：**波長感知補丁嵌入 (Wavelength-Aware Patch Embedding)**，將各頻段的物理波長映射為連續嵌入向量，具備「任意感測器與波段組合無須重訓練」的超強適配能力。

---

## 1. 在崩塌地辨識任務中的定位與架構爭鳴
依據 [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]] 的深入研究：
* **獨立 Encoder 的局限 (Standalone Limitation)**：
  * 當 Clay 單獨作為特徵編碼器時，由於純 ViT 輸出為單一尺度的低空間解析度特徵圖（如 $16 \times 16$），缺少卷積網路的多尺度階層與 Skip Connections，導致細微邊界重構困難，在 [[Landslide4Sense]] 上獨立微調僅達 **55.2% F1**，落後於傳統 U-Net（59.9%）。
* **最佳整合角色：輔助瓶頸上下文 (Auxiliary Bottleneck Context)**：
  * 當 Clay 被注入至 U-Net 的瓶頸層（Bottleneck）作為全局語意先驗時，**Hybrid U-Net + Clay 架構一舉達到 64.5 ± 1.8% F1**（最高單一種子達 64.93%），顯著超越獨立 CNN 與獨立 Prithvi-EO-2.0。
* **可解釋性機制 (Grad-CAM)**：
  * Clay 在崩塌地偵測中的核心作用是**「光譜與地貌校正先驗（Corrective Prior）」**，主要在疤痕外部提供抑制訊號，消除平原農田與河床裸土的假警報，將模型 Precision 提升近 10 個百分點。

---

## 2. 微調技巧與最佳實務
* **必須採用「兩階段訓練 (Two-Stage Training)」**：
  * 訓練初期隨機初始化的解碼器會產生劇烈震盪的錯誤梯度，若在此時直接進行 LoRA 微調，會破壞預訓練編碼器的表徵能力（F1 跌至 30.2%）。
  * 建議先凍結 Clay 訓練解碼器 10 個 Epoch，待解碼器穩定後再插入 LoRA（$r=8, \alpha=8$）進行協同適應。

---

## 3. 關聯論文文獻 (Referenced Papers)
* [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]] — 提出 Hybrid U-Net + Clay 瓶頸融合架構與兩階段 LoRA 微調策略。
