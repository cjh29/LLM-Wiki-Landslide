# Earth Foundation Model 遙測崩塌地偵測 — LLM 學術知識庫

本專案基於 **LLM Wiki Pattern（可持續複利演進的學術知識庫模式）** 構建，專注於研究**地球觀測基礎模型（Earth Observation / Geospatial Foundation Models, GFMs）**在**崩塌地災害偵測（Landslide Detection）**領域的前沿技術演進、跨模態感測器融合與少樣本落地應用。

---

## 🖥️ 互動簡報投影片 (Interactive Presentations)

專案內建兩款完全獨立（Standalone）、免安裝依賴的互動式學術簡報：

* 🌟 **[Graph 知識圖譜互動簡報 (推薦)](presentations/Earth_Foundation_Models_Landslide_Detection_Graph.html)**
  * 以 10 幅向量圖（SVG Canvas）為視覺核心，涵蓋全域導覽 Master Hub、多感測器拓撲、四大範式演進 DAG、雙串流特徵融合圖、SAGT 階梯信任半監督 DAG、0.78 最佳閾值位移曲線與 7 大缺口攻關網絡。
  * 支援全螢幕（`F`）、總覽模式（`O`）、首頁圖譜跳轉（`H`）與鍵盤快捷鍵。
* 📄 **[全景演進與前沿標準版簡報](presentations/Earth_Foundation_Models_Landslide_Detection.html)**

> 💡 **線上即時預覽**：若啟用本儲存庫的 **GitHub Pages**，即可直接在瀏覽器線上進行全螢幕簡報！

---

## 📚 知識庫架構 (Repository Structure)

```text
.
├── presentations/               # 互動式 HTML 學術簡報
│   ├── Earth_Foundation_Models_Landslide_Detection_Graph.html
│   └── Earth_Foundation_Models_Landslide_Detection.html
├── wiki/                        # 知識庫核心實體與論述
│   ├── papers/                  # 5 篇核心論文深度解析筆記
│   ├── models/                  # Foundation Models 實體頁 (Prithvi, Clay, SAM, LandslideFM)
│   ├── sensors/                 # 感測器頁面 (Sentinel-2, DEM & Slope, SAR)
│   ├── benchmarks/              # 評估基準 (Landslide4Sense, Bijie, LandslideVL, IoU/F1)
│   └── synthesis/               # 跨文獻橫向綜合論述
│       ├── 效能總覽對比表.md     # 4 大技術路線量化矩陣與排行榜
│       └── 現有研究缺口與技術挑戰.md # 9 大未解瓶頸與研究缺口
├── raw/                         # 原始文獻存放目錄
├── index.md                     # 全站 Wikilinks 索引目錄
├── log.md                       # 依時間記錄的操作日誌 (Append-only)
├── AGENTS.md                    # LLM Wiki 運作規範手冊 (含 /draft-lit-review 指令)
└── README.md                    # 本導覽說明文件
```

---

## 🔬 收錄核心論文 (Ingested Papers)

1. **Prithvi-EO-2.0 技術報告** (*Szwarcman et al., Dec 2024*) — NASA/IBM 4.2M 遙測景預訓練之 3D-MAE 基礎模型。
2. **Prithvi 崩塌製圖與泛化性評估** (*Li et al., Nov 2025*) — 揭示波段物理順序敏感性與 1.25% 少樣本適應能力。
3. **Clay-CNN 混合架構** (*Vu, Jun 2026*) — 提出雙串流瓶頸特徵融合，創下 64.5% F1 SOTA 並發現 0.78 最佳決策閾值位移。
4. **TransLandSeg: SAM 轉移學習** (*Hou et al., Mar 2024*) — 1.3% PEFT 轉接器，揭示自然圖像 VFM 之光譜盲區與隨機切片空間資料洩漏評估爭鳴。
5. **LandslideFM: 統一多任務與地質推理大模型** (*Wang et al., Nov 2026*) — 首創 SAGT 階梯信任半監督學習（1/8 標籤超越全監督）與 LandslideVL 災害鏈圖文推理基準。

---

## 🛠️ 維護與使用

本知識庫遵循 Obsidian 標準 Wikilinks 語法，推薦使用 [Obsidian](https://obsidian.md/) 打開本目錄作為 Vault 進行知識圖譜雙向瀏覽。
