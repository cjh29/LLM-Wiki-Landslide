# Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection

- **作者**：Binh Huong Vu
- **發表年份**：2026
- **發表期刊/會議/預印本**：arXiv:2606.14081v3 [cs.CV]
- **DOI / 連結**：https://arxiv.org/abs/2606.14081
- **關聯模型**：[[Clay]], [[Prithvi]]
- **關聯感測器**：[[Sentinel-2]], [[DEM & Slope]]
- **關聯基準**：[[Landslide4Sense]], [[IoU & F1-score]]

---

## 1. 執行摘要 (Executive Summary)
本研究針對遙測崩塌地偵測中「基礎模型與卷積神經網路的整合範式」提出開創性的架構反思。作者評估了地球觀測基礎模型 **[[Clay]] v1.5** 在 [[Landslide4Sense]] 基準上的表現，發現若將 Clay 單獨作為主編碼器（Primary Encoder），由於純 Vision Transformer (ViT) 缺乏卷積網路的多尺度階層式下採樣與跳躍連接（Skip Connections），其像素級分割 F1-score 僅有 55.2%，顯著落後於傳統 U-Net Baseline（59.9%）。然而，當改採 **Hybrid U-Net + Clay 架構**——將 U-Net 作為空間幾何骨幹，而將 Clay 作為瓶頸層（Bottleneck）的輔助語意先驗注入時，配合**兩階段 LoRA 微調策略**，測試集 F1-score 大幅躍升至 **64.5 ± 1.8%**（最高單一 Seed 達 64.93%），不僅超越 U-Net 達 4.6 個百分點，亦超越了獨立 Prithvi-EO-2.0 的 60.7% F1。研究證實：在崩塌地偵測中，GFM 的最佳角色是「輔助並補充空間卷積網路，而非直接取代它」。

---

## 2. 地球基礎模型架構與微調機制 (Foundation Model & Adaptation)
- **基礎模型**：[[Clay]] v1.5（ViT-Large，24 Transformer blocks，特徵維度 1024，參數量約 339M）。
- **預訓練策略**：於全球超過 7,000 萬筆多感測器、多季節衛星影像切片上進行 Masked Autoencoding 自監督預訓練。
- **輸入嵌入創新**：具備**波長感知補丁嵌入（Wavelength-Aware Patch Embedding）**，將各光學波段的物理波長映射至連續嵌入空間，具備任意通道組合的免重訓練適配能力。
- **兩大創新架構設計**：
  1. **架構一 (Arch 1: Clay Backbone + 殘差地形融合)**：
     * Clay 處理 12 個 Sentinel-2 光學波段，產出單一 $16 \times 16$ 語意特徵圖。
     * DEM 與坡度經由 4 層卷積組成的 `TerrainPyramid` 提取，以可學習純量 $\alpha$ 殘差融合於解碼器粗尺度（$16 \times 16$ 與 $32 \times 32$）中。
  2. **架構二 (Arch 2: Hybrid U-Net + Clay，最佳模型)**：
     * **空間主骨幹**：4 階層標準 U-Net，處理全部 14 通道，完整保留所有 4 個尺度的 Skip Connections。
     * **瓶頸輔助融合**：Clay 獨立處理 12 個光學波段，輸出 $16 \times 16$ 特徵，經 $1 \times 1$ 卷積降維並雙線性插值縮放至 $8 \times 8$，與 U-Net 的 $8 \times 8$ 瓶頸特徵進行 Channel Concatenation（由 1024 通道壓回 512 通道），再送入 4 階層 U-Net 解碼器。
- **優化與兩階段訓練策略 (Two-Stage Training)**：
  * **毀滅性梯度干擾問題**：實驗發現若在訓練初期直接啟用 LoRA 微調，未收斂的隨機初始化 Decoder 會嚴重破壞預訓練編碼器（F1 跌至 30.2%）。
  * **解決方案**：
    * **Stage 1 (Epoch 1–10)**：完全凍結 Clay，專注以較高學習率（$3 \times 10^{-4}$）訓練 U-Net 解碼器、地形分支與融合層。
    * **Stage 2 (Epoch 11 起)**：在 Clay 所有 24 個 Transformer 區塊中插入 LoRA（$r=8, \alpha=8$），以低學習率（$3 \times 10^{-5}$）與解碼器（$1 \times 10^{-4}$）進行協同自適應訓練。
- **損失函數**：$0.9 \times \text{BCE}(\text{pos\_weight}=25) + 0.1 \times \mathcal{L}_{\text{Lovász}}$，兼顧梯度穩定性與 IoU/F1 直接優化。

---

## 3. 資料來源與感測器配置 (Data & Sensor Setup)
- **基準資料集**：**[[Landslide4Sense]]**（3,799 訓練 / 245 驗證 / 800 測試切片，尺寸 $128 \times 128$，空間解析度 10m，正樣本像素僅佔約 2%）。
- **地理分層劃分**：採用「留出地點（Leave-out-location）」分層策略，嚴格杜絕尼泊爾、日本北海道、印度與台灣台東四地的空間數據洩漏（Spatial Leakage）。
- **波段分離度指數 ($d'$) 分析**：
  * **高辨識度 ($d' > 1.0$)**：可見光（B1–B4）、紅邊（B5–B7, B8A）與短波紅外（B11–B12），為崩塌裸露地表的核心判別特徵。
  * **低辨識度 ($d' < 0.5$)**：近紅外（NIR B8）與水氣（B9）。
  * **地形資料角色**：[[DEM & Slope]] 像素級單點分離度雖然偏低，但能提供「斜坡重力失穩可能性」的物理地貌先驗。

---

## 4. 實驗設計與評估效能 (Experiments & Performance)

### A. 全消融實驗結果對照 (Landslide4Sense 測試集)
| 編號 | 模型架構與訓練配置 | Precision (%) | Recall (%) | F1-score (%) | 最優門檻 (t) | 核心實驗現象 |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **1** | **U-Net (競賽 Baseline)** | 52.5 | 69.9 | 59.9 | 0.50 | 基準 CNN |
| 2 | Clay (凍結) + 地形拼接 | 39.6 | 50.8 | 49.3 | 0.50 | 純 ViT 單一尺度特徵不足以支撐像素分割 |
| 3 | Clay (LoRA) + 地形拼接 (單階段) | 29.0 | 33.6 | 30.2 | 0.50 | 未成熟 Decoder 梯度破壞預訓練特徵 |
| 4 | Clay (LoRA) + 多尺度地形 + 調整損失 | 46.9 | 57.2 | 54.2 | 0.50 | 多尺度殘差融合有助恢復性能 |
| 5a | Clay (LoRA) + 多尺度地形 + 兩階段訓練 | 51.5 ± 2.9 | 59.6 ± 4.6 | 55.2 ± 3.6 | 0.60 | 兩階段穩定解碼器，但仍落後 U-Net 4.7 pp |
| 5b | ,→ 同上述模型，固定預設門檻 $t=0.50$ | 44.7 ± 4.6 | 68.2 ± 12.7| 53.4 ± 2.6 | 0.50 | 門檻過低導致假警報激增 |
| 6 | Hybrid U-Net + Clay (凍結瓶頸) | 60.3 | 63.2 | 61.7 | 0.70 | 一舉超越 U-Net 基準與所有 Arch 1 |
| **7a**| **Hybrid U-Net + Clay (LoRA 兩階段)** | **62.2 ± 3.0** | **67.1 ± 0.6** | **64.5 ± 1.8** | **0.78** | **全場最佳 (單次最高達 64.93%)** |
| 7b | ,→ 同上述模型，固定預設門檻 $t=0.50$ | 40.0 ± 0.7 | 87.2 ± 2.2 | 54.8 ± 0.7 | 0.50 | 門檻未校準導致 Precision 崩盤至 40% |

### B. 決策門檻漂移 (Decision Boundary Shift)
* **極端不平衡下的門檻校準**：ROC-AUC 高達 0.989，顯示模型具備極佳的排序辨別力。但因崩塌像素僅佔 2%，最佳決策門檻大幅偏向高機率區間（$t=0.78 \sim 0.80$）。若採用常見的預設值 $t=0.50$，會引發大量假警報，導致 F1 崩跌 9.7 個百分點。

---

## 5. 可解釋性、不確定性量化與核心洞見 (Interpretability & Insights)

### A. Signed Grad-CAM 可解釋性分析
* 分析瓶頸層特徵前後差異（$\Delta = \text{fusion} - \text{pre-fusion}$）顯示：
  * Clay 注入的特徵權重並非聚焦於崩塌疤痕內部（內部僅 $\Delta = +0.049$），而是**顯著分佈於疤痕外部區域（$\Delta = +0.100$）**。
  * **核心機制**：Clay 的角色並非單純的「崩塌偵測器」，而是作為**「地貌與光譜校正先驗（Corrective Context Prior）」**。它利用全局光譜理解，成功壓制了農田、河床沈積物等與崩塌光譜極為相似但物理上不可能崩塌的平地假警報，從而將 Precision 由 52.5% 大幅提升至 62.2%。

### B. Monte Carlo Dropout (MC Dropout) 不確定性量化
* 在 20 次 Forward Passes 下，模型的不確定性嚴格遵循：
  $$\text{TN} (0.0017) < \text{TP} (0.0351) < \text{FN} (0.0431) < \text{FP} (0.0450)$$
* 高不確定性高度集中於崩塌邊界（Crown and Runout zones）與細小零碎碎屑上，證明模型具備良好的「知道自己在何處容易犯錯」的自覺性。

### C. 與既有文獻之對比與對本知識庫的貢獻
* **與 [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]] 之架構辯證**：
  * Li et al. (2025) 採用「以 Prithvi 為主骨幹，搭配前置 U-Net Adapter」策略，達成了 60.7% F1（6B）與 65.4% F1（14B）。
  * Vu (2026) 採用「以傳統 U-Net 為主骨幹，將 Clay 注入瓶頸」策略，以僅約 339M 的參數量（遠低於 Prithvi-600M），在標準劃分下同樣達到了 **64.5% F1**，且大幅簡化了架構的工程實作難度。
* **核心理論收穫**：
  * **GFM 負責光譜泛化與全域語意抑噪，CNN 負責多尺度空間幾何邊界定位**。兩者混合（Hybrid）優於任何單一架構。

---

## 6. 參考文獻引用 (BibTeX)
```bibtex
@article{vu2026clay,
  title={Clay-CNN Hybrids: Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection},
  author={Vu, Binh Huong},
  journal={arXiv preprint arXiv:2606.14081},
  year={2026}
}
```
