# SAM & VFM Adaptation (通用視覺基礎模型遷移)

- **核心模型**：Segment Anything Model (SAM) 系列 (Meta AI, Kirillov et al., 2023)
- **代表衍生架構**：TransLandSeg (Hou et al., 2024)、LandslideNet (Yu et al., 2024)、SAM-Adapter
- **預訓練基礎**：SA-1B 資料集（1,100 萬張自然影像、超過 10 億個遮罩），以 ViT 為骨幹進行對比學習與 Masked Autoencoding。
- **核心優勢**：極為強大的物體邊界捕捉能力、高解析度自然光（RGB）影像上的泛化幾何輪廓提取能力。

---

## 1. 遙測崩塌偵測中的適配機制 (Adaptation Strategies)
* **為何原生 SAM 在遙測崩塌中失效？**
  * SA-1B 完全由地面常規自然攝影構成，缺乏衛星正射視角與地質光譜特徵。
  * 崩塌地常呈現不規則碎屑擴散、漸變過渡帶與光譜混淆，使得 SAM 原生的 Zero-shot 或簡單提示點（Point Prompts）無法正確圈定滑坡範圍。
* **參數高效微調 (PEFT / Adapter-Tuning)**：
  * 依據 [[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model|Hou et al. (2024)]]，透過 **自適應遷移學習 (ATL)**，凍結 SAM 98.7% 權重，僅訓練 1.3% 參數（4.18M），即可成功將其龐大輪廓能力遷移至崩塌提取。
  * 殘差連接（Residual Connections）在 Adapter 中至關重要，能有效防止微調過程中喪失 SAM 原生的全域感知特徵。

---

## 2. 自然影像 VFM vs. 原生遙測 GeoFM 之本質差異
| 比較維度 | 通用視覺基礎模型 (VFM，如 SAM) | 原生地球觀測基礎模型 (GeoFM，如 [[Prithvi]], [[Clay]]) |
| :--- | :--- | :--- |
| **預訓練資料** | 自然 RGB 攝影照片 (SA-1B) | 全球衛星多光譜影像 (HLS, Sentinel, Landsat, Planet) |
| **輸入通道能力** | **嚴格限制於 RGB 3 波段** | **原生支援 6~14+ 波段 (紅邊, NIR, SWIR, SAR, DEM)** |
| **時空地理先驗** | 無地理座標概念，僅視為通用像素矩陣 | 原生編碼經緯度、海拔與時間戳記 (Location/Temporal Embeddings) |
| **高解析度 RGB (如 0.8m 畢節)** | **表現極佳 (F1 > 93%)**，微觀紋理輪廓清晰 | 需針對超高解析度下採樣或切片重訓 |
| **中解析度多光譜 (如 10m L4S)** | **表現疲軟 (官方 F1 僅 55.4%)**，受限於光譜盲區 | **表現優異 (F1 60.7%~65.4%)**，善用紅邊與土壤濕度 |

---

## 3. 關聯論文文獻 (Referenced Papers)
* [[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model|Hou et al. (2024)]] — 提出 TransLandSeg 與 ATL 架構。
* [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]] — 於官方嚴格標準下重測 TransLandSeg，點出其 RGB 局限。
