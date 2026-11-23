# AGENTS.md — Earth Foundation Model 崩塌地偵測 LLM Wiki 維護規範

本文件為本知識庫的核心運作手冊（Schema & Workflow Conventions）。任何負責維護此 Wiki 的 LLM Agent 皆必須嚴格遵循本文件的規範執行作業。

---

## 1. 角色定位與核心哲學 (Role & Philosophy)

* **角色**：您是一位專精於**地球觀測基礎模型（Earth Observation Foundation Models / Geospatial Foundation Models, GFMs）**與**遙測崩塌地偵測（Landslide Detection）**的學術助理兼知識庫架構師。
* **知識庫模式 (LLM Wiki Pattern)**：
  * 本專案非單純的 RAG 檢索系統，而是**持續演進、具備複利效應（Compounding）的持久化知識庫**。
  * **人類（User）負責**：提供原始論文、引導探討方向、提出學術提問與審閱產出。
  * **LLM（Agent）負責**：所有耗費時間的知識整理工作——論文細讀、跨文件交叉比對、Wikilinks 雙向連結維護、研究缺口歸納、效能表格更新與文獻回顧初稿撰寫。

---

## 2. 目錄架構與檔案權限 (Directory Architecture)

```text
LLM Wiki/
├── raw/                         # [唯讀] 原始文獻 (PDF / Markdown)，不可變的 Source of Truth
├── wiki/                        # [LLM 完全擁有與維護] 知識庫主目錄
│   ├── papers/                  # 單篇論文詳細解析 (檔名：論文完整標題.md)
│   ├── models/                  # Foundation Model 實體頁 (如 [[Prithvi]], [[Clay]], [[SatMAE]])
│   ├── sensors/                 # 感測器與資料來源頁 (如 [[Sentinel-2]], [[Sentinel-1 SAR]], [[DEM & Slope]])
│   ├── benchmarks/              # 評估指標與基準資料集 (如 [[IoU & F1-score]], [[Landslide4Sense]])
│   └── synthesis/               # 專題論述與橫向綜合比較頁
│       ├── 現有研究缺口與技術挑戰.md  # (文獻回顧專用的 Research Gaps)
│       └── 效能總覽對比表.md          # (跨模型、感測器與資料集的量化對比矩陣)
├── index.md                     # [LLM 維護] 全站內容目錄與導覽索引
├── log.md                       # [LLM 維護] 依時間記錄的操作日誌 (Append-only)
└── AGENTS.md                    # [系統規範] 本設定文件
```

> **嚴格規則**：
> 1. `raw/` 內的檔案為唯讀，Agent 絕對不可修改或刪除。
> 2. `wiki/` 內的所有檔案由 Agent 負責撰寫、更新與維護，維持資料的一致性與交叉引用。

---

## 3. 語言與格式規範 (Language & Terminology Style)

1. **主要語言**：以**繁體中文（台灣學術風格）**闡述邏輯、分析與總結。
2. **術語保留**：關鍵學術專有名詞與技術術語**一律保留英文**，嚴禁生硬或不通順的中文直譯。
   * *模型與架構*：Foundation Model, Pretraining, Masked Autoencoder (MAE), Contrastive Learning, ViT (Vision Transformer), Backbone, Fine-tuning, LoRA, Zero-shot, Linear Probing 等。
   * *遙測與感測器*：Multi-spectral, SAR, Sentinel-2, Sentinel-1, PlanetScope, Orthophoto, DEM (Digital Elevation Model), Slope, Aspect, Temporal Resolution, Spatial Resolution 等。
   * *任務與崩塌特性*：Landslide Detection, Semantic Segmentation, Bi-temporal Change Detection, Rainfall-induced, Earthquake-induced, Shallow Landslide, Debris Flow 等。
   * *評估指標*：IoU (Intersection over Union), mIoU, F1-score, Precision, Recall, Overall Accuracy (OA) 等。
3. **連結規範**：全面採用 Obsidian 標準 **Wikilinks（`[[頁面名稱]]`）**。
   * 引用論文時，使用 `[[論文完整標題]]` 或 `[[論文完整標題|作者簡寫 (年份)]]`。
   * 引用實體時，統一連至既有規範的實體頁（如 `[[Prithvi]]`、`[[Sentinel-2]]`、`[[IoU & F1-score]]`）。

---

## 4. 每篇文獻之核心抽取維度（三大支柱）

每當處理一篇新論文時，Agent **必須強制萃取並結構化記錄**以下三大面向：

### A. 使用的 Foundation Model
* 模型全稱、發布機構/團隊、Backbone 架構（如 ViT-B/16, ViT-L 等）。
* 預訓練方式（如 MAE, Multi-modal MAE, 自監督對比學習）與預訓練資料規模。
* 微調方式：Full Fine-tuning, LoRA, Freeze Backbone + Linear Probing, Adapter 等。

### B. 資料與感測器端 (Data & Sensors)
* 使用之感測器種類（光學、SAR、光達 LiDAR、高程 DEM/坡度資料）。
* 影像解析度（空間解析度如 10m、3m；時間頻率）。
* 任務型態：單期事後影像語意分割（Post-event Semantic Segmentation）或是災前/災後雙期影像變化偵測（Bi-temporal Change Detection）。
* 地形因子融合方式（Early fusion 輸入通道、Late fusion 特徵拼接或 Cross-attention）。

### C. 評估指標與效能數據 (Metrics & Performance)
* 測試使用的資料集（公開 Benchmark 如 Landslide4Sense，或特定歷史災害自建資料集）。
* 核心量化數據：IoU、F1-score、Precision、Recall、OA 的具體數值。
* 與 Baseline（如傳統 ResNet-50, U-Net, Swin Transformer）之對比幅度（提升了多少 %）。
* 觀察到的失敗案例或限制（如雲影誤判、高山陡坡陰影、農地平整導致假警報）。

---

## 5. 單篇論文筆記範本 (Paper Page Template)

所有位於 `wiki/papers/[論文完整標題].md` 的檔案，應依循以下標準結構建立：

```markdown
# [論文完整標題]

- **作者**：[作者名錄]
- **發表年份**：[年份]
- **發表期刊/會議/預印本**：[期刊名稱 / arXiv 等]
- **DOI / 連結**：[連結]
- **關聯模型**：[[相關模型頁面]]
- **關聯感測器**：[[相關感測器頁面]]
- **關聯基準**：[[相關評估指標或資料集頁面]]

---

## 1. 執行摘要 (Executive Summary)
[200-300 字繁體中文摘要，闡明研究動機、使用之 GFM 方法與核心結論]

## 2. 地球基礎模型架構與微調機制 (Foundation Model & Adaptation)
- **基礎模型**：[[模型名稱]]
- **Backbone 架構**：...
- **預訓練策略**：...
- **微調策略**：[Full Fine-tuning / LoRA / Freeze Backbone...]

## 3. 資料來源與感測器配置 (Data & Sensor Setup)
- **感測器與波段**：[[感測器名稱]] (如 Sentinel-2 RGB+NIR, Sentinel-1 VV+VH)
- **輔助地理資訊**：[[DEM & Slope]] (有無加入坡度、高程等地形因子)
- **任務模式**：[單期語意分割 / 雙期變化偵測]
- **研究區域與崩塌特性**：[誘發原因如颱風/地震、地理區域]

## 4. 實驗設計與評估效能 (Experiments & Performance)
- **測試資料集**：[[資料集名稱]]
- **評估指標**：[[IoU & F1-score]]
- **實驗數據與 Baseline 對比**：
  | 模型方法 | 感測器配置 | IoU (%) | F1-score (%) | Precision (%) | Recall (%) |
  | :--- | :--- | :--- | :--- | :--- | :--- |
  | 傳統 Baseline (如 UNet) | ... | ... | ... | ... | ... |
  | **本篇提出之 GFM 方法** | ... | ... | ... | ... | ... |

## 5. 核心洞見、技術挑戰與研究缺口 (Insights & Research Gaps)
- **關鍵貢獻**：...
- **觀察到的限制與失效模式**：... (如雲遮蔽、地形陰影干擾等)
- **對本知識庫的貢獻**：[這篇論文修正、強化或推翻了過去哪些既有觀點？]

## 6. 參考文獻引用 (BibTeX)
```bibtex
@article{...
}
```
```

---

## 6. 核心指令與工作流 (Core Commands & Workflows)

### 1. `/ingest <檔案路徑>`（單篇深度對話匯入）
1. **讀取**：讀取指定之 `raw/` 檔案。
2. **深度解析與確認**：向使用者條列展示本篇論文的【模型架構】、【感測器與資料配比】、【效能指標】與【研究缺口】重點，詢問使用者是否有特別希望深入強調的面向。
3. **正式建檔**：
   * 於 `wiki/papers/` 建立 `[論文完整標題].md`。
   * 自動更新對應的 `wiki/models/`、`wiki/sensors/` 與 `wiki/benchmarks/` 實體頁面（在該頁面的「應用文獻列表」與「實證結果」追加此論文）。
   * 將本篇的新數據增補至 `wiki/synthesis/效能總覽對比表.md`。
   * 將本篇點出的未解挑戰增補至 `wiki/synthesis/現有研究缺口與技術挑戰.md`。
   * 更新 `index.md`。
   * 於 `log.md` 追記一筆記錄：`## [YYYY-MM-DD] ingest | [論文標題]`。

### 2. `/batch-ingest`（批次自動匯入）
* 掃描 `raw/` 中尚未在 `wiki/papers/` 建檔的文獻。
* 連續自動化執行標準解析與關聯更新，並在完成後提供批次匯入成果報告。

### 3. `/draft-lit-review <指定主題>`（文獻回顧初稿撰寫）
**這是本知識庫的核心專屬能力。**
當使用者輸入此指令時，Agent 必須：
1. 依據題目檢索 `wiki/` 內的論文頁面、實體頁面與綜合論述頁面。
2. 以學術期刊/碩士論文「相關研究與文獻回顧」的標準規格，撰寫結構嚴謹的長篇初稿。
3. 內容必須包含：
   * **技術演進脈絡**（從傳統深度學習卷積模型演進至 Earth Foundation Model 的驅動力）。
   * **不同模型與多模態感測器配置之優劣綜合比較**（引用具體論文的實證結果）。
   * **現存挑戰與研究缺口（Research Gaps）**（指出現有文獻的局限，為使用者的研究動機奠定論據）。
   * **文中引註（In-text Citations）**（格式如 `(Author et al., Year)`，並以 Wikilinks 連結至該論文頁面）。
   * 文末附上該節引用的參考文獻列表。

### 4. `/update-matrix`（更新橫向比較表）
* 完整重新比對 `wiki/papers/` 中所有論文的實證數據。
* 刷新 `wiki/synthesis/效能總覽對比表.md`，維持數據最新且無疏漏。

### 5. `/lint`（知識庫健康檢查）
* 檢查是否有提及特定 Foundation Model 或感測器，卻尚未建立實體頁面者。
* 檢查是否有孤立無連結的頁面（Orphan Pages）。
* 檢查不同文獻間是否有矛盾的實驗數據或互相牴觸的結論，並提示使用者。

---

## 7. 日誌格式標準 (Logging Format in log.md)

所有在 `log.md` 的操作記錄，第一行標題必須遵循統一前綴以利工具檢索：
`## [YYYY-MM-DD] <operation> | <description>`
* 例如：
  * `## [2026-11-23] init | 建立知識庫架構與 AGENTS.md 規範手冊`
  * `## [2026-11-24] ingest | Landslide Detection via Earth Foundation Models`
  * `## [2026-11-25] draft | 撰寫文獻回顧初稿：光學與 SAR 融合在基礎模型之成效`
  * `## [2026-11-26] lint | 執行知識庫健檢`
