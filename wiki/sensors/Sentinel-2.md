# Sentinel-2

- **營運機構**：歐洲太空總署 (ESA, Copernicus Programme)
- **感測器型態**：多光譜儀 (Multi-Spectral Instrument, MSI)
- **衛星星座**：Sentinel-2A & Sentinel-2B (雙星協同，重訪週期 5 天)
- **空間解析度**：10m (可見光 RGB + 寬近紅外 B8)、20m (紅邊 B5-B7, 窄近紅外 B8A, 短波紅外 SWIR B11-B12)、60m (大氣波段 B1, B9, B10)

---

## 1. 各波段在崩塌地偵測中的物理意義 (Landslide Scientific Relevance)
依據 [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]] 整理：
* **可見光 (B2 Blue, B3 Green, B4 Red - 10m)**：提供自然色彩影像，用於捕捉新鮮崩塌造成的裸露地表、崩塌冠部（Scarp）與堆積體色差。
* **紅邊波段 (B5, B6, B7 Red-edge - 20m)**：對葉綠素吸收與林冠結構變化高度敏感，能精確捕捉斜坡植被輕微擾動與冠層破壞。
* **近紅外 (B8 Broad NIR 10m / B8A Narrow NIR 20m)**：植被反射最強區間。植被破壞時 NIR 反射率急遽驟降，為 NDVI 等植被指標的核心波段。
* **短波紅外 (B11 SWIR1, B12 SWIR2 - 20m)**：對土壤含水量（Soil Moisture）與岩礦吸收特徵極為敏感，是判別潮濕不穩定邊坡與裸露泥岩滑移面的關鍵依據。
* **大氣校正波段 (B1 Aerosol, B9 Water Vapor, B10 Cirrus - 60m)**：用於雲霧、水氣與氣溶膠光學厚度過濾，輔助地表反射率反演。

---

## 2. 基礎模型適配與實證洞見 (Foundation Model Findings)
1. **波段排列對齊關鍵性**：[[Prithvi]] 預訓練預設採用 6 個波段（B2, B3, B4, B8A, B11, B12）。實驗顯示，隨意打亂波段排列順序將導致崩塌偵測 F1-score 由 51.5% 崩跌至 39.1%，證明模型強烈依賴波段光理順序。
2. **資訊量篩選**：利用互資訊（Mutual Information）篩選出最具崩塌識別力之波段組合（如 MI-6a 結合可見光與紅邊/SWIR）可在僅使用 6 個波段下達到 70.08% mIoU，逼近 14 通道全波段水準。
3. **主要挑戰**：受限於光學穿透力，在颱風暴雨剛發生時極易受濃密雲霧遮蔽，需仰賴 SAR 影像或雙期光學進行補充。

---

## 3. 關聯論文文獻 (Referenced Papers)
* [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]]
