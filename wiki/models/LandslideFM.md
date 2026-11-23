# LandslideFM (崩塌地專屬多任務與視覺語言基礎模型)

- **研發團隊/機構**：City University of Macau, Technical University of Munich (TUM), Tsinghua University
- **核心論文**：[[LandslideFM - A Foundation Model Towards Unified Landslide Perception and Understanding|Wang et al. (2026)]]
- **開源代碼**：https://github.com/wangshanwen001/LandslideFM
- **核心定位**：全球首個兼具**「下游多視覺感知任務（分類、檢測、分割）」**與**「高階多模態視覺語言推理（VLM Reasoning）」**的崩塌地專屬基礎模型。

---

## 1. 核心技術架構 (Architectural Paradigm)

### A. SAGT (Source-Agreement Graduated Trust) 半監督預訓練
* **解決核心瓶頸**：傳統半監督方法在通用視覺模型（如 [[SAM & VFM Adaptation|SAM 3]]）與領域教師模型（DINOv2）意見不一時直接丟棄偽標籤，導致模型丟失難分割邊界。
* **階梯信任權重 (Graduated Trust Weights $w_i$)**：
  * $\alpha = 1.0$：SAM 與 Teacher 均高信心且預測一致（最高信任）。
  * $\beta = 0.3$：兩者均高信心但預測衝突（降為弱監督訊號，保留困難樣本特徵）。
  * $\gamma = 0.7$：僅 SAM 具備高信心（信任其強大輪廓能力）。
  * $\delta = 0.5$：僅 Teacher 具備高信心（信任其地質領域先驗）。
* **極少標籤威力**：在 [[Bijie Dataset]] 與 GVLM 上，**僅使用 1/8 標註數據的 LandslideFM 便全面超越全標註 SOTA 模型**。

### B. 級聯式視覺語言推理管線 (Cascaded Vision-Language Reasoning)
* **流程機制**：
  1. SAGT 視覺骨幹先對遙測影像進行精準滑坡語意分割。
  2. 將原圖、預測遮罩可視化疊加圖與用戶 Prompt 一併轉換為視覺與文字 Tokens。
  3. 注入透過 LoRA 微調的視覺語言模型（VLM）。
* **高階場景解讀能力**：不再侷限於輸出黑白 Mask，而能生成結構化環境屬性（道路、民宅、水體、植被），並給出「滑坡入水誘發堰塞湖與土石流鏈」之專業防災評估報告。

---

## 2. 實證效能突破
1. **多任務視覺感知**：
   * 分類：在 Bijie 達 99.08% Precision；在 GVLM 達 90.81% Precision / 91.06% F1。
   * 目標檢測：在 Bijie 達 77.56% F1 / 69.85% IoU。
   * 語意分割：在 1/8 標籤下，Bijie 達 **80.47% mIoU / 88.93% mF1**；GVLM 達 **87.33% mIoU / 92.87% mF1**。
2. **多模態災害推理 (LandslideVL 基準)**：
   * 結構化環境屬性辨識：**mRecall 達到 94.43%**（超越 Qwen3-VL-8B 達 27.31 pp，大幅消弭通用大模型對地質災害的漏檢現象）。
   * 詳細報告文本生成：**BLEU 達到 13.16%**（Qwen3-VL 僅 0.57%），ROUGE-L F1 達 32.50%。

---

## 3. 數據增強重大洞見
* **嚴禁 CutMix / Cutout**：實驗證實任意遮擋或拼貼會破壞滑坡空間連續性與地貌物理脈絡，引發語意混亂。

---

## 4. 關聯論文文獻 (Referenced Papers)
* [[LandslideFM - A Foundation Model Towards Unified Landslide Perception and Understanding|Wang et al. (2026)]]
