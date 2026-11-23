# Earth Foundation Model 崩塌地偵測 — 知識庫索引目錄

> 本目錄記錄知識庫內所有的頁面導覽。當新增論文或建立實體頁時，Agent 將自動更新此索引。

---

## 🖥️ 互動簡報投影片 (Interactive Presentations)
* **[Earth Foundation Models 偵測崩塌地 — 知識圖譜互動簡報 (Graph 版)](file:///d:/碩/蔡炎龍/LLM%20Wiki/presentations/Earth_Foundation_Models_Landslide_Detection_Graph.html)** — 以 6 大知識節點群、流程圖與演進 DAG 為視覺核心之新一代拓撲簡報，包含 Master Hub 導覽圖、多模態感測拓撲、雙串流瓶頸特徵融合圖、SAGT 階梯信任 DAG 與 7 大研究缺口攻關路徑網絡。
* **[Earth Foundation Models 偵測崩塌地 — 全景演進與前沿簡報 (標準版)](file:///d:/碩/蔡炎龍/LLM%20Wiki/presentations/Earth_Foundation_Models_Landslide_Detection.html)** — 10 頁沉浸式現代化簡報，整合 5 篇先驅論文之四大演進範式、三大架構爭鳴、少樣本/半監督排行榜與未來研究缺口，支援全螢幕、鍵盤導覽與總覽縮圖。

---

## 📚 專題綜合論述 (Synthesis Pages)
* [[現有研究缺口與技術挑戰]] — 梳理文獻回顧專用難題：從純視覺感知邁向「次生災害鏈推理」之典範轉型、半監督衝突區域階梯信任利用、通用增強（CutMix）破壞地貌連續性、通用視覺模型光譜盲區、隨機切片空間資料洩漏、純 ViT 跳躍連接缺失缺陷、兩階段微調防護、門檻向 0.8 漂移現象、泥炭地地貌泛化盲區。
* [[效能總覽對比表]] — 橫向跨四大技術路線（VFM 遷移 vs. 原生 GeoFM 主骨幹 vs. CNN-GeoFM 混合 vs. 視覺語言多任務 LandslideFM）、Landslide4Sense 官方標準排行榜、極限少樣本（1.25%/2.5%）與半監督（1/8 標籤）對比、LandslideVL 圖文推理指標矩陣（BLEU/ROUGE/環境屬性召回率）。

---

## 📄 論文文獻 (Papers)
* [[Prithvi-EO-2.0 - A Versatile Multi-Temporal Foundation Model for Earth Observation Applications|Prithvi-EO-2.0: A Versatile Multi-Temporal Foundation Model for Earth Observation Applications (Szwarcman et al., 2024)]] — NASA/IBM 官方奠基技術報告，詳解 420 萬 HLS 採樣、時空位置編碼與 TerraTorch 生態。
* [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Landslide Hazard Mapping with Geospatial Foundation Models (Li et al., 2025)]] — 系統評估 Prithvi-EO-2.0 在崩塌地偵測中之波段適應、極限少樣本學習與跨地理泛化性。
* [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Clay-CNN Hybrids: Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection (Vu, 2026)]] — 提出 Hybrid U-Net + Clay 瓶頸融合架構、兩階段 LoRA 微調與門檻校準，創下 64.5% F1 SOTA。
* [[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model|TransLandSeg: A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model (Hou et al., 2024)]] — 提出基於 Meta SAM 的自適應遷移學習（ATL），在 0.8m 畢節高解析度資料集取得 93.41% F1，並引發遙測切片空間資料洩漏之評估爭鳴。
* [[LandslideFM - A Foundation Model Towards Unified Landslide Perception and Understanding|LandslideFM: A Foundation Model Towards Unified Landslide Perception and Understanding (Wang et al., 2026)]] — 全球首個專用崩塌多任務與視覺語言推理基礎模型，提出 SAGT 半監督階梯信任預訓練與 LandslideVL 基準。

---

## 🧠 地球基礎模型與視覺模型實體 (Foundation Models)
* [[Prithvi]] — NASA & IBM 研發之 ViT 3D-MAE 地球觀測基礎模型（支援 300M/600M 與時空位置嵌入，TerraTorch 工具庫）。
* [[Clay]] — Clay Foundation 研發之波長感知 ViT 地球觀測基礎模型（7,000 萬切片預訓練，擅長作為輔助瓶頸語意先驗）。
* [[LandslideFM]] — 專屬崩塌地多任務與視覺語言基礎模型（結合 SAGT 半監督預訓練、DINOv2+SAM 協同與 LoRA-VLM 災害鏈推理）。
* [[SAM & VFM Adaptation]] — Meta SA-1B 預訓練之通用視覺大模型，透過 ATL/Adapter 進行 1.3% 參數微調，高解析度輪廓極強但受限於 RGB 3 波段。
* *(待補實體頁：[[SatMAE]], [[TerraMind]])*

---

## 🛰️ 感測器與資料來源 (Sensors & Data Sources)
* [[Sentinel-2]] — ESA 10m/20m 多光譜光學衛星星座（涵蓋 RGB、紅邊、NIR、SWIR 波段，具備明確光譜分離度 $d'$）。
* [[DEM & Slope]] — 數值高程模型與坡度重力地形因子（抑制平地假警報之關鍵物理約束；對比輸入 Adapter vs 殘差 TerrainPyramid 兩種融合機制）。

---

## 📊 基準資料集與評估指標 (Benchmarks & Metrics)
* [[Landslide4Sense]] — 包含 S2 與 ALOS 高程坡度之 14 通道多模態崩塌語意分割基準資料集（涵蓋官方留出地點測試集排行榜與隨機切分空間洩漏分析）。
* [[Bijie Dataset]] — 北京二號 0.8m 超高空間解析度光學崩塌資料集（VFM 通用幾何輪廓與半監督模型之最佳驗證基準）。
* [[LandslideVL]] — 全球首個專為崩塌地視覺語言推理打造的圖文對齊基準資料集（4,660 幅全人工校準樣本，包含 5 大環境二元標籤與詳細地質評估文本）。
* [[IoU & F1-score]] — 崩塌地極端不平衡情境下的核心評估指標群（剖析 CNN 與 ViT 的 Precision/Recall 權衡、決策門檻向 0.78 漂移之現象與 MC Dropout 不確定性量化）。
