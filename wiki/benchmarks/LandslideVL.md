# LandslideVL (崩塌地多模態視覺語言基準)

- **發布團隊**：Wang et al. (City University of Macau, TUM, Tsinghua)
- **核心論文**：[[LandslideFM - A Foundation Model Towards Unified Landslide Perception and Understanding|Wang et al. (2026)]]
- **開源下載**：https://huggingface.co/datasets/fluorites/LandslideVL
- **定位**：**全球首個專為崩塌地視覺語言（Vision-Language）智能分析打造的圖文對齊基準資料集**。

---

## 1. 資料庫構成與標註標準 (Dataset Composition)
資料集整合了三大高解析度崩塌地光學來源，共精選並經過多輪**全人工校準標註（Human-annotated & Calibrated）** 4,660 幅樣本：
1. **[[Bijie Dataset]] (BJL)**：TripleSat 衛星 0.8m 光學影像，入選 2,267 幅切片。
2. **GVLM (Global Very High Resolution Landslide Mapping)**：Google Earth 0.59m 雙期光學影像（涵蓋全球六大洲 17 個區域），裁剪為 $512 \times 512$ 後入選 1,298 幅切片。
3. **九寨溝無人機資料集 (Jiuzhai Valley)**：CAS Landslide Dataset 之無人機（UAV）0.5m 超高解析 RGB 影像，入選 1,095 幅切片。

---

## 2. 多模態標註維度 (Multimodal Annotations)
每幅影像均配備兩套不同層級的文本資訊：
1. **五大結構化二元環境標籤 (Structured Scene Attributes)**：
   * `landslide_exists`：是否存在崩塌地表（核心指標）。
   * `has_building`：周遭是否有受威脅的建築物或聚落。
   * `has_road`：周遭是否有受阻斷或切割的道路交通要道。
   * `has_water`：是否鄰近河流、水庫或湖泊（指示堰塞湖、土石流次生災害可能）。
   * `has_vegetation`：邊坡植被擾動覆蓋狀況。
2. **專家級詳細地質評估文本 (Detailed Textual Descriptions)**：
   * 包含微地貌型態描述、失穩機制推測、直接受災對象分析，以及緊急疏散與工程整治建議。

---

## 3. 評估協定與指標 (Evaluation Protocols)
* **結構化標籤評估**：Precision (P), Recall (R), F1-score。
* **文字生成品質評估**：
  * **BLEU**：1 至 4-gram 匹配幾何平均值（含長度懲罰 BP）。
  * **ROUGE-L**：最長公共子序列（LCS）結構一致性。
  * **Word Overlap (WO)**：詞彙覆蓋 F1 分數。

---

## 4. 主流多模態大模型基準排行榜 (Benchmark Leaderboard)

### A. 五大結構化環境屬性平均識別率
| 模型 | mPrecision (%) | mRecall (%) | mF1 (%) | 評述 |
| :--- | :--- | :--- | :--- | :--- |
| Qwen2.5-VL-3B | 91.13 | 56.61 | 62.70 | 通用大模型缺乏地質先驗，漏檢嚴重 |
| Qwen2.5-VL-7B | 91.67 | 59.28 | 66.96 | 召回率依舊偏低 |
| Qwen3-VL-8B | 92.85 | 67.12 | 74.96 | 通用最強，但崩塌召回率仍不足 |
| Kimi-VL-A3B | 87.77 | 65.63 | 68.71 | 水體與道路識別較弱 |
| **[[LandslideFM]] (Ours)** | **93.24** | **94.43** | **93.82** | **全面壓倒性領先**（mRecall 領先 27.31 pp，徹底解決漏檢） |

### B. 詳細防災文字描述生成品質
| 模型 | BLEU (%) | ROUGE-L Precision (%) | ROUGE-L F1 (%) | Word Overlap F1 (%) |
| :--- | :--- | :--- | :--- | :--- |
| Qwen2.5-VL-3B | 0.12 | 4.64 | 7.87 | 17.02 |
| Qwen2.5-VL-7B | 0.24 | 4.29 | 7.44 | 15.55 |
| Qwen3-VL-8B | 0.57 | 5.17 | 8.63 | 19.27 |
| Kimi-VL-A3B | 0.17 | 4.12 | 7.27 | 15.08 |
| **[[LandslideFM]] (Ours)** | **13.16** | **35.44** | **32.50** | **41.94** |

---

## 5. 關聯論文文獻 (Referenced Papers)
* [[LandslideFM - A Foundation Model Towards Unified Landslide Perception and Understanding|Wang et al. (2026)]]
