# DEM & Slope (數值高程模型與地形因子)

- **概念型態**：地理空間地形因子 (Topographic Features)
- **常見資料來源**：ALOS PALSAR DEM (12.5m/30m)、SRTM DEM (30m)、Copernicus DEM (30m)、內政部光達 DEM (1m-5m)
- **衍生參數**：坡度 (Slope)、坡向 (Aspect)、平面曲率 (Plan Curvature)、剖面曲率 (Profile Curvature)、地形濕度指數 (TWI)

---

## 1. 在崩塌地偵測之物理角色與地文意義
* **重力驅動機制**：重力是斜坡失穩滑動的根本動力。崩塌通常發生在具備特定坡度門檻（如 20°~50°）的陡峻山坡。
* **平地假警報抑制**：純光學影像常將平原地區之農田翻土、休耕地或道路施工誤判為崩塌裸露地。加入高程與坡度先驗知識，能有效從幾何物理上排除平地假警報。
* **波段分離度特徵 ($d'$)**：依據 [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]] 量化分析，DEM 與 Slope 的單像素分離度指數雖然偏低（$d' < 0.5$），但其地質地貌環境先驗對於判斷「何處可能發生崩塌失穩」扮演不可或缺的物理約束。

---

## 2. 基礎模型與地形因子融合策略 (Integration Strategies in GFMs)
兩篇先驅論文展現了兩種截然不同的地形因子融合哲學：

1. **架構一：非線性輸入 Adapter 全波段投影**（[[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]]）：
   * 將 DEM 與 Slope 作為第 13、14 通道，與 Sentinel-2 光學波段合併，透過淺層卷積 U-Net Adapter 將 14 通道對齊回 6 通道輸入至 [[Prithvi]] 主編碼器，全參數微調創下 **73.62% mIoU / 65.42% F1**。
2. **架構二：多尺度殘差地形金字塔 (Residual TerrainPyramid)**（[[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]]）：
   * 由於 DEM 與 Slope 屬於非光學測量值，直接送入光學基礎模型（如 [[Clay]]）的波長嵌入層會造成語意衝突。因此改以獨立的卷積 `TerrainPyramid` 提取多尺度高程特徵，再利用**可學習純量 $\alpha$（初始值為 0）** 殘差注入解碼器粗糙尺度（$16 \times 16$ 與 $32 \times 32$），避免高頻地形雜訊干擾精細邊界。
3. **架構三：U-Net 主幹內置融合**（Vu (2026) Arch 2）：
   * 由傳統 U-Net 直接處理含 DEM/Slope 的 14 通道以維持局部空間幾何細節，Clay 僅在瓶頸層提供純光學語意上下文。此混合架構取得 **64.5% F1**。

---

## 3. 關聯論文文獻 (Referenced Papers)
* [[Landslide Hazard Mapping with Geospatial Foundation Models - Geographical Generalizability, Data Scarcity, and Band Adaptability|Li et al. (2025)]]
* [[Clay-CNN Hybrids - Leveraging Geospatial Foundation Models as Auxiliary Context for Landslide Detection|Vu (2026)]]
