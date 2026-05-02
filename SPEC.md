# Translate PWA — 規格書

## 1. Concept & Vision

一個優雅、專業的即時翻譯工具。上下雙欄設計：上方自由輸入，下方即時呈現譯文。介面乾淨有質感，讓翻譯這件事看起來很從容。

風格方向：**Modern Minimal** — 大面積留白、柔和漸層、微妙的陰影與動態。

## 2. Design Language

**Aesthetic**: 柔和專業，有溫度但不幼稚

**Color Palette**:
- Background: `#f0f4f8`（淺灰藍）
- Card/Surface: `#ffffff`
- Primary accent: `#4F46E5`（靛藍紫）
- Secondary: `#7C3AED`（紫羅蘭）
- Text primary: `#1e293b`
- Text secondary: `#64748b`
- Border: `#e2e8f0`
- Success/translate: `#10B981`（翠綠）

**Typography**:
- Primary: `Inter`（Google Fonts）— 現代、清晰
- Fallback: `-apple-system, BlinkMacSystemFont, sans-serif`
- 語言名稱用母語顯示（如「日本語」）

**Spatial System**:
- 8px base grid
- Card border-radius: 16px
- Padding: 24px
- Gap between sections: 24px

**Motion**:
- 翻譯中：按鈕有旋轉載入動畫
- 翻譯完成：結果文字 fade-in（200ms ease-out）
- 語言切換：下拉選單有 150ms 過渡
- 上下箭頭按鈕點擊：scale 0.95 → 1（100ms）

**Visual Assets**:
- 語言標籤用 Emoji 國旗（🇺🇸/🇯🇵/🇰🇷 等）
- Swap（交換語言）按鈕用箭頭圖示
- 翻譯按鈕用翻譯機 emoji 或 icon

## 3. Layout & Structure

```
┌─────────────────────────────────────┐
│           🌐 Translate PWA          │  ← Header（簡單標題）
├─────────────────────────────────────┤
│  ┌─────────────┐  🔄  ┌───────────┐ │
│  │ 🇺🇸 English │ ⟷   │ 🇹🇼 繁體中文│ │  ← 語言選擇列（兩欄+交換）
│  └─────────────┘     └───────────┘ │
├─────────────────────────────────────┤
│  ┌─────────────────────────────────┐ │
│  │                                 │ │  ← 上半：輸入文字區
│  │   輸入文字...                   │ │
│  │                                 │ │
│  │                        [翻譯]   │ │
│  └─────────────────────────────────┘ │
├─────────────────────────────────────┤
│  ┌─────────────────────────────────┐ │
│  │                                 │ │  ← 下半：譯文顯示區
│  │   翻譯結果...                   │ │
│  │                                 │ │
│  │              [📋 複製] [🔊 朗讀] │ │
│  └─────────────────────────────────┘ │
└─────────────────────────────────────┘
```

- 響應式：手機上兩欄變成一欄（豎向排列）
- 最大寬度 720px，居中顯示
- 主體垂直居中於視窗

## 4. Features & Interactions

### 核心功能
- **即時翻譯**：輸入文字後點擊翻譯或按 `Ctrl+Enter`
- **多語言切換**：支援 10+ 語言，含繁體中文
- **交換語言**：一鍵交換 source ↔ target（內容也跟著交換）
- **複製譯文**：一鍵複製到剪貼簿，顯示「已複製」提示
- **文字朗讀**：用 Web Speech API 朗讀譯文（可選語音）

### 支援語言
| 語言 | 代碼 | Emoji |
|------|------|-------|
| 繁體中文 | zh-TW | 🇹🇼 |
| 簡體中文 | zh-CN | 🇨🇳 |
| 日本語 | ja | 🇯🇵 |
| English | en | 🇺🇸 |
| 한국어 | ko | 🇰🇷 |
| Español | es | 🇪🇸 |
| Français | fr | 🇫🇷 |
| Deutsch | de | 🇩🇪 |
| Italiano | it | 🇮🇹 |
| Português | pt | 🇵🇹 |
| ภาษาไทย | th | 🇹🇭 |
| Tiếng Việt | vi | 🇻🇳 |
| Русский | ru | 🇷🇺 |
| العربية | ar | 🇸🇦 |

### 互動細節
- **翻譯按鈕**：點擊後顯示旋轉載入，禁用防止重複點擊
- **複製按鈕**：點擊後文字變「已複製 ✓」，2 秒後恢復
- **交換按鈕**：點擊後上下語言交換，輸入區內容也交換
- **空白輸入**：翻譯按鈕禁用，譯文區顯示提示文字
- **API 錯誤**：顯示錯誤訊息，紅色提示

## 5. Component Inventory

### Header
- 標題「Translate PWA」+ 🌐 emoji
- 副標題「即時多語言翻譯」
- 底部有 subtle gradient line

### Language Selector
- 下拉選單，顯示 Emoji + 語言名稱（用該語言）
- Hover：背景變淺紫色
- Focus：邊框變 primary 色
- 選項右側顯示可用目標語言數量

### Text Area（輸入區）
- 高度 200px，resize: vertical
- Placeholder：「輸入文字翻譯...」
- 右下角有小字計數（字數）
- 右下角翻譯按鈕（內嵌在 textarea 右下角）

### Text Area（譯文區）
- 高度 200px，readonly
- 有淡綠色背景（`#f0fdf4`）
- 右下角有複製和朗讀按鈕

### Swap Button（交換）
- 圓形按鈕，48px
- 兩個箭頭組成的 icon
- Hover：背景變 primary 色，icon 變白
- Click：scale 0.95 → 1

### Translate Button
- Full width，primary 色
- Hover：背景加深
- Loading：顯示旋轉 + 「翻譯中...」
- Disabled：opacity 0.5

## 6. Technical Approach

### 架構
- 純前端單頁 HTML + CSS + JS（Vercel 部署）
- 無後端，API 呼叫直接從瀏覽器發出

### API
- **翻譯 API**：MyMemory API（免費，無需 API Key）
  - Endpoint: `https://api.mymemory.translated.net/get`
  - 格式：`?q={text}&langpair={source}|{target}`
  - 響應：`responseData.translatedText`

### PWA
- `manifest.json`：可安裝到桌面
- Service Worker：快取離線可用（可選，等網站完成後加）

### 部署
- Vercel CLI：`vercel --prod`
- 專案名稱：`translate-pwa`
