# 作品集網站規格（Apple Liquid Glass 專業冷色調）— MVVM 架構

## 專案概述
此作品集網站旨在展示個人成就、技能與專案，採用 Apple Liquid Glass 設計語彙，呈現專業冷色調風格，具備現代、簡潔且具質感的玻璃反光外觀。

---

## Model 層（數據與狀態模型）

### 1. 色彩系統（Color Palette）
```
--bg1: #eaf6ff          // 冷色光暈（淺藍）
--bg2: #f0fbff          // 冷青背景層
--accent1: #0a84ff      // 冷藍主色
--accent2: #00c2ff      // 青藍次色（timeline 點、hover 效果）
--glass-bg: rgba(255,255,255,0.78)     // 玻璃卡片背景
--glass-border: rgba(255,255,255,0.9)  // 玻璃卡片邊框
--text: rgba(8,16,28,0.96)              // 文字色（深色）
--muted: rgba(8,16,28,0.62)             // 次級文字色
```

### 2. 間距與半徑常數
```
--radius: 14px           // 圓角標準值
padding-base: 24px / 28px
margin-base: 18px / 20px
```

### 3. 狀態數據結構
- **導航狀態**：current_section（字符串，標記當前位置）
- **卡片狀態**：hover 狀態（布林值，影響 transform / box-shadow）
- **動畫狀態**：prefers-reduced-motion（用戶偏好設定）

---

## View 層（UI 組件與視覺呈現）

### 1. 背景層（Background）
- **元素**：body::before 與 body::after 偽元素
- **效果**：
  - 兩個 radial-gradient 光暈（冷藍 + 冷青）
  - 緩慢飄動動畫（animation: floatA/floatB 18s/22s）
  - filter: blur(80px) 模糊效果
  - z-index: -1（置於背景層）
- **顯示範圍**：覆蓋整個視窗（inset: -20% -20% auto -20%）

### 2. 玻璃卡片組件（Glass Card）
應用於 `header`、`nav`、`section` 等主要區塊：
- **背景**：linear-gradient(180deg, rgba(255,255,255,0.82), rgba(245,255,255,0.78))
- **毛玻璃效果**：backdrop-filter: blur(24px) / -webkit-backdrop-filter: blur(24px)
- **邊框**：1px solid var(--glass-border)
- **圓角**：12px ~ 18px
- **陰影**：box-shadow: 0 10px 36px rgba(6,18,40,0.06)

### 3. 玻璃反光條（Specular Highlight）
應用於 `section`：
- **元素**：section::after 偽元素
- **效果**：
  - linear-gradient(120deg, rgba(255,255,255,0.55) 0%, ..., transparent 40%)
  - transform: rotate(-12deg) 斜向配置
  - mix-blend-mode: soft-light（柔光混合模式）
  - opacity: 0.75
  - pointer-events: none（不干擾互動）

### 4. 區塊列表（Sections）
- **首屏（Hero）**、**關於我（About）**、**技能（Skills）**、**學歷（Education）**、**經歷（Experience）**、**獎項（Awards）**、**作品（Projects）**、**聯絡（Contact）**
- 每個區塊都使用玻璃卡片樣式
- 內部 padding: 36px 24px；max-width: 1100px

### 5. Timeline 組件
- **軸線**：左側垂直 2px 線條（background: linear-gradient(180deg, rgba(10,132,255,0.22), rgba(0,194,255,0.12))）
- **項目**：.timeline-item，使用玻璃卡片樣式
- **圓點**：.timeline-item::before，14×14px，background: var(--accent2)，box-shadow 發光 glow

### 6. Tag 雲（Skill Badges）
- **造型**：膠囊形（border-radius: 999px）
- **背景**：linear-gradient(180deg, rgba(255,255,255,0.94), rgba(245,255,255,0.9))
- **邊框**：1px solid rgba(200,230,255,0.6)（冷色邊框）
- **內部陰影**：inset 0 1px 0 rgba(255,255,255,0.6)
- **padding**：6px 10px

### 7. 導航欄（Nav）
- **位置**：fixed, top: 20px, right: 20px
- **樣式**：玻璃卡片（略低透明度）
- **互動**：hover 時背景聲微藍色漸層、文字轉為 accent1 色
- **max-width**：220px

---

## ViewModel 層（互動邏輯與狀態管理）

### 1. 過渡與動畫
- **卡片 hover**：
  - transform: translateY(-6px)（向上浮起）
  - box-shadow 增強（0 26px 70px rgba(...)）
  - transition: transform .28s cubic-bezier(.2,.9,.2,1), box-shadow .28s
  
- **背景光暈動畫**：
  - @keyframes floatA / floatB（18s / 22s 循環）
  - transform: translate3d(...) scale(...)
  - will-change: transform（性能優化）

- **Nav 連結 hover**：
  - 背景變為 linear-gradient(90deg, rgba(10,132,255,0.06), ...)
  - box-shadow: 0 8px 22px rgba(10,132,255,0.06)
  - color 轉為 accent1

### 2. 響應式行為（Responsive）
- **<=600px 時**：
  - nav：position fixed，寬度自適應（left: 10px, right: 10px）
  - section / header：margin / padding 縮小（12px）
  
### 3. 可及性支援（Accessibility）
- **prefers-reduced-motion 媒體查詢**：
  - 禁用所有動畫（animation: none）
  - 禁用所有過渡（transition: none）
  
### 4. 文字互動
- **nav a**：text-decoration: none，color: var(--text)
- **h1, h2**：color: var(--accent1)（冷藍色）
- **p**：color: var(--text)，line-height: 1.6

### 5. 焦點狀態（Focus States）
- nav a 在 hover 時改變背景與陰影（暗示可互動）

---

## 開發備註

- **使用單一 `index.html` 檔案**：所有 HTML 與 CSS 內嵌於一個檔案中。
- **純 CSS 實作**：不依賴任何 JavaScript 框架或打包工具。
- **跨瀏覽器相容性**：注意 backdrop-filter 的 -webkit- 前綴（Safari）與相容性。
- **性能考慮**：使用 transform / will-change 以觸發 GPU 加速；避免頻繁重繪。
- **設計原則**：可及性優先、冷色調專業感、玻璃質感為主要視覺特徵。