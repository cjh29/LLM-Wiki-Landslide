# LandslideFM - A Foundation Model Towards Unified Landslide Perception and Understanding

- **作者**：Shanwen Wang, Sirui Wang, Xin Sun, Jungong Han
- **發表年份**：2026
- **發表期刊/會議/預印本**：arXiv:2611.12936v1 [cs.AI]
- **DOI / 連結**：https://arxiv.org/abs/2611.12936
- **代碼庫**：https://github.com/wangshanwen001/LandslideFM
- **關聯模型**：[[LandslideFM]], [[SAM & VFM Adaptation]], DINOv2
- **關聯感測器**：高解析光學遙測影像 (TripleSat, Google Earth, UAV)
- **關聯基準**：[[LandslideVL]], [[Bijie Dataset]], GVLM, [[IoU & F1-score]]

---

## 1. 執行摘要 (Executive Summary)
本研究代表了遙測崩塌地 AI 研究從「單純視覺幾何感知（分割/檢測）」向「視覺語言綜合理解與高階防災推理（Vision-Language Understanding）」邁進的劃時代里程碑。針對現有模型僅能輸出單一任務遮罩且高度依賴大量標註數據的局限，作者提出了全球首個專為崩塌地打造的通用基礎模型 **[[LandslideFM]]**。模型採用獨創的 **SAGT (Source-Agreement Graduated Trust) 半監督學習框架**，利用少量標註與海量無標籤影像進行預訓練，支援分類、檢測與語意分割三大任務；進一步將分割結果作為精準空間先驗注入 LoRA 微調的視覺語言模型（VLM），實現崩塌成因、威脅民宅/道路評估與次生災害鏈推理。同時，研究團隊構建並開源了首個崩塌地多模態圖文基準 **[[LandslideVL]]**（4,660 幅全人工校準樣本）。

---

## 2. 核心架構與三大技術創新 (Architecture & Innovations)

### A. SAGT (Source-Agreement Graduated Trust) 半監督預訓練框架
* **核心動機**：傳統半監督方法在通用視覺模型（SAM）與領域教師模型（DINOv2）預測不一致時，往往直接丟棄該區域，導致模型喪失在「模糊邊界與難分類過渡帶」的學習能力。
* **階梯信任權重機制 (Graduated Trust Weighting)**：
  $$\mathcal{L}_U = \frac{\sum_{i=1}^{N_U} w_i \mathcal{L}_{CE}(p_{u,s}^i, y_{u,t}^i)}{\sum_{i=1}^{N_U} w_i}$$
  權重 $w_i$ 根據兩者的預測一致性與信心度動態給予分級信任：
  * **$w_i = \alpha = 1.0$**：SAM 與 Teacher 均具高信心且類別一致（最高信任）。
  * **$w_i = \beta = 0.3$**：兩者均具高信心但類別衝突（矛盾區域，保留低權重避免噪聲污染）。
  * **$w_i = \gamma = 0.7$**：僅 SAM 具備高信心（信任 SAM 的泛化邊界）。
  * **$w_i = \delta = 0.5$**：僅 Teacher 具備高信心（信任領域專業先驗）。
* **實證威力**：僅需 **1/8 標註數據**，LandslideFM 的分割效能便已全面碾壓採用 100% 標註數據的現有 SOTA 模型！

### B. 級聯式視覺語言推理管線 (Cascaded VLM Reasoning)
* **架構流程**：
  1. SAGT 視覺骨幹先對輸入影像進行高精度滑坡語意分割。
  2. 將原始影像與「疊加預測滑坡區域」的可視化圖共同轉為視覺 Tokens。
  3. 結合使用者 Prompt 文字 Tokens，送入以 LoRA 微調的 VLM。
* **高階場景理解**：模型不僅能回答是否存在崩塌，還能精確辨識「是否有臨近道路阻斷」、「是否有民宅威脅」、「是否有滑坡入水形成堰塞湖」以及給予工程加固建議。

### C. 數據增強關鍵發現：嚴禁 CutMix / Cutout
* 論文特別實驗證明，CV 領域常用的 Cutout/CutMix 會強行切斷滑坡的自然地貌連續性與周遭環境脈絡，對崩塌辨識造成顯著負面損害，因此訓練時嚴格避免區域切割增強。

---

## 3. 實驗設計與量化評估 (Experiments & Performance)

### A. 語意分割對比 (畢節 BJL 與 GVLM 資料集，極限少標籤)
在極端稀疏標籤（1/8 與 1/4 標註比率）下，對比多種主流半監督演算法（FixMatch, UniMatch v2, AllSpark）與專用模型（PF-SAM, Semi-LM）：
* **畢節資料集 (BJL, 1/8 標籤)**：
  * LandslideFM: **mPrecision 86.02% / mRecall 92.05% / mF1 88.93% / mIoU 80.47% / Kappa 77.34%**（領先第二名 UniMatch v2 達 2.57% mIoU）。
* **GVLM 資料集 (1/8 標籤)**：
  * LandslideFM: **mPrecision 93.88% / mRecall 91.89% / mF1 92.87% / mIoU 87.33% / Kappa 85.72%**（領先 UniMatch v2 達 6.79% mIoU）。

### B. 高階多模態推理對比 (LandslideVL 基準測試)
對比業界最強的通用開源多模態大模型（Qwen2.5-VL-3B/7B, Qwen3-VL-8B, Kimi-VL-A3B）：
1. **五大結構化環境屬性平均識別率**：
   * **LandslideFM**：**mPrecision 93.24% / mRecall 94.43% / mF1 93.82%**（Recall 超越第二名 Qwen3-VL-8B 達 27.31 個百分點！徹底根除通用大模型對專業地質災害嚴重漏檢的毛病）。
2. **詳細防災評估文本生成品質**：
   * **LandslideFM**：**BLEU 13.16%**（Qwen3-VL 僅 0.57%）、**ROUGE-L F1 32.50%**（Qwen3-VL 僅 8.63%）、**Word Overlap F1 41.94%**。

---

## 4. 核心洞見與對本知識庫的貢獻
- **完成 AI 演進第四階段拼圖**：將知識庫的研究範疇從「幾何像素識別」正式推進至「多模態災害鏈因果推理與決策支援」。
- **解決長尾少樣本的新途徑**：SAGT 證明透過通用大模型（SAM）與領域大模型（DINOv2）的階梯式互信協同，能以極低的標註成本撬動海量未標註遙測影像。

---

## 5. 參考文獻引用 (BibTeX)
```bibtex
@article{wang2026landslidefm,
  title={LandslideFM: A Foundation Model Towards Unified Landslide Perception and Understanding},
  author={Wang, Shanwen and Wang, Sirui and Sun, Xin and Han, Jungong},
  journal={arXiv preprint arXiv:2611.12936},
  year={2026}
}
```
