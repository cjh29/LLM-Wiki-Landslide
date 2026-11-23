# 知識庫歷程日誌 (Compounding Log)

本檔案為時間序日誌（Append-only），記錄本知識庫的所有重大演進、論文匯入歷程與健檢結果。

---

## [2026-11-23] init | 建立知識庫架構、目錄層級與 AGENTS.md 規範手冊
- **操作項目**：初始化知識庫環境。
- **產出檔案**：
  - `AGENTS.md`：定義 LLM 運作規範、文獻抽取三大支柱、Wikilinks 標準與專屬指令（含 `/draft-lit-review`）。
  - `index.md`：建立目錄索引架構。
  - `wiki/synthesis/`：建立專題論述頁預設架構（研究缺口與效能對比表）。
  - 各模組子資料夾：`raw/`, `wiki/papers/`, `wiki/models/`, `wiki/sensors/`, `wiki/benchmarks/`。

---

## [2026-11-23] ingest | Landslide Hazard Mapping with Geospatial Foundation Models: Geographical Generalizability, Data Scarcity, and Band Adaptability
- **操作項目**：深度對話式匯入首篇核心論文 (Li et al., 2025, arXiv:2511.04474)。
- **新建與更新檔案清單**：
  - 論文筆記：`[[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability]]`
  - 基礎模型頁：`[[Prithvi]]`
  - 感測器頁：`[[Sentinel-2]]`、`[[DEM & Slope]]`
  - 基準與指標頁：`[[Landslide4Sense]]`、`[[IoU & F1-score]]`
  - 專題論述頁更新：填入 Landslide4Sense 基準數據、波段適配、少樣本數據效率及跨域泛化比較；增補波段物理順序相依性、泥炭地特殊崩塌型態泛化盲區。
  - 全站導覽更新：`index.md`。

---

## [2026-11-23] ingest | Clay-CNN Hybrids: Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection
- **操作項目**：深度對話式匯入第二篇核心論文 (Vu, 2026, arXiv:2606.14081)。
- **新建與更新檔案清單**：
  - 論文筆記：`[[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection]]`
  - 基礎模型頁：建立 `[[Clay]]` 實體頁。
  - 既有實體更新：`[[Landslide4Sense]]`、`[[IoU & F1-score]]`、`[[DEM & Slope]]`。
  - 專題論述頁更新：新增三大架構整合範式橫向對比表、兩階段訓練微調分析、純 ViT 跳躍連接缺失缺陷。
  - 全站導覽更新：`index.md`。

---

## [2026-11-23] ingest | TransLandSeg: A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model
- **操作項目**：深度對話式匯入第三篇論文 (Hou et al., 2024, arXiv:2403.10127)。
- **新建與更新檔案清單**：
  - 論文筆記：`[[TransLandSeg - A Transfer Learning Approach for Landslide Semantic Segmentation Based on Vision Foundation Model]]`
  - 模型實體頁：建立 `[[SAM & VFM Adaptation]]` 實體頁。
  - 基準實體頁：建立 `[[Bijie Dataset]]` 實體頁。
  - 既有實體更新：`[[Landslide4Sense]]`（深度剖析空間洩漏爭鳴）。
  - 專題論述頁更新：三大基礎模型路線對比、VFM 光譜盲區、空間洩漏評估陷阱。
  - 全站導覽更新：`index.md`。

---

## [2026-11-23] ingest | Prithvi-EO-2.0 Technical Report & LandslideFM Unified Perception and Understanding
- **操作項目**：批次/深度雙篇匯入第四篇（Szwarcman et al., 2024）與第五篇（Wang et al., 2026）論文。
- **新建與更新檔案清單**：
  - 論文筆記：
    - `[[Prithvi-EO-2.0 - A Versatile Multi-Temporal Foundation Model for Earth Observation Applications]]`
    - `[[LandslideFM - A Foundation Model Towards Unified Landslide Perception and Understanding]]`
  - 模型實體頁：升級 `[[Prithvi]]`，新建 `[[LandslideFM]]`。
  - 基準實體頁：新建 `[[LandslideVL]]`。
  - 專題論述頁更新：升級四大技術路線表、少樣本 2.5% 與半監督 1/8 標籤對比表、LandslideVL 圖文推理指標表；新增災害鏈推理轉型、SAGT 階梯信任機制、CutMix 地貌破壞缺口。
  - 全站導覽更新：`index.md`。

---

## [2026-11-23] presentation | 建立 Earth Foundation Model 偵測崩塌地互動簡報投影片
- **操作項目**：基於 Wiki 全站 5 篇文獻與綜合論述，設計並生成 10 頁獨立互動式 HTML 簡報投影片。
- **產出檔案**：
  - `presentations/Earth_Foundation_Models_Landslide_Detection.html`：包含深色科技感主題、進度條、鍵盤導覽、全螢幕切換、總覽模式縮圖、量化對比表格與四大範式流程卡片。
  - 更新導覽：`index.md`。


---

## [2026-11-23] presentation | 建立 Graph 式知識圖譜互動簡報投影片 (Earth_Foundation_Models_Landslide_Detection_Graph.html)
- **操作項目**：嚴格依循審閱確認之「製作與檢查流程 DAG」及「6 大知識節點群大綱」，完成 10 頁以向量圖（SVG / Topology Graph / DAG）為視覺核心的新一代互動簡報。
- **產出檔案**：
  - `presentations/Earth_Foundation_Models_Landslide_Detection_Graph.html`：包含 Master Navigation Hub、多源感測整合拓撲、四大範式演進 DAG、TransLandSeg 光譜盲區路徑圖、Clay-CNN 雙串流瓶頸特徵融合圖、LandslideFM 像素-推理級聯架構、SAGT 階梯信任 DAG、空間自相關洩漏與 0.78 閾值位移曲線圖、7 大研究缺口攻關網絡圖，支援節點互動跳轉、全螢幕、鍵盤快捷鍵與總覽網絡。
  - 更新導覽：`index.md`。
