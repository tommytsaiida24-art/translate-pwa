# translate-pwa 專案日誌

> 即時多語言翻譯 PWA — 部署網址：https://translate-pwa-nu.vercel.app
> GitHub：https://github.com/tommytsaiida24-art/translate-pwa

---

## 待解決：圖片 OCR 功能（v1.5）需要重新設計

### 🔴 已放棄：Paddle.js OCR（v1.5.1 - v1.5.2）

**嘗試歷程：**

1. **一開始用 Tesseract.js v5** — 發現所有 `.traineddata.gz` 檔案在 jsDelivr CDN 上全是 404（Tesseract.js 官方已不再維護 CDN）
2. **改用 Paddle.js OCR（`@paddlejs-models/ocr@1.2.4`）** — CDN (`jsdelivr.net`) 和模型檔 (`paddlejs.bj.bcebos.com`) 全都 200 OK，乍看可用
3. **第一個 bug：`import()` 失去 `this` 綁定** — ES module 的 `this = undefined`，但 Paddle.js 內部用 `this.paddlejs`，改成 `<script>` 標籤載入後解決
4. **第二個 bug：變數名不一致** — `initOCR()` 改了 `tesseractWorker` → `paddleOCR`，但 `runOCR()` 裡的檢查沒跟著改，導致永遠顯示「OCR 載入失敗」，已修
5. **第三個 bug（根本原因）：WebGL 初始化失敗**

   用 Playwright headless Chromium 實測發現：
   ```
   Error: paddlejs.core init failed, WebGL: null
   ```
   Paddle.js OCR 底層用 WebGL 做 inference，但 headless 環境（和湯米的瀏覽器環境）的 `getContext('webgl')` 回傳 `null`。

**為什麼 Paddle.js 行不通：**
- Paddle.js OCR 需要 WebGL 才能跑
- 湯米的瀏覽器（和 server 環境的 headless Chromium）WebGL context 是 `null`
- 嘗試 `--enable-webgl --use-gl=swiftshader` 等 flag 在 headless 環境無效（需要真正的 X server）
- 即使湯米當地瀏覽器有 WebGL，Paddle.js 的 WebGL backend（`paddlejs-backend-webgl`）init 也失敗

**所有嘗試過的環境：**
- ✅ `curl` 測試 CDN/模型 URL → 全部 200 OK
- ❌ `import()` 動態載入 → `this.paddlejs` undefined
- ✅ `<script>` 標籤載入 → `window.paddlejs.core` 有負載
- ❌ `window.paddlejs.ocr.init()` → WebGL context null
- ❌ 湯米的正式瀏覽器 → 同樣錯誤

---

### 🟡 待評估：Tesseract.js + Vercel Proxy

**發現：**
- Tesseract.js v4.1.1 JS CDN 是通的
- GitHub raw 檔案（`raw.githubusercontent.com/tesseract-ocr/tessdata/main/eng.traineddata`）全部 200 OK
- 問題是瀏覽器直接 fetch GitHub raw 會被 CORS 阻擋

**可行方案：在 Vercel 架一個 `/api/proxy-tessdata.js`**
```javascript
// Tesseract.js 改成從自己的 Vercel API 拿 traineddata，繞過 CORS
const langPath = '/api/proxy-tessdata?lang=eng';
```
然後在 Vercel serverless function 裡 fetch GitHub raw，回傳給客戶端。

---

### 🟢 建議的替代方案

1. **Tesseract.js v4 + Vercel CORS proxy**（如上所述）
2. **純後端 OCR** — 上傳圖片到 Vercel Function，在 server 端用 `tesseract` CLI 或 Python `pytesseract` 處理，回傳文字
3. **第三方 OCR API** — MyMemory（翻譯有，OCR 可能沒有）或 Google Cloud Vision（需要付費 key）
4. **使用者的電腦有 WebGL** — 如果湯米在桌機 Chrome 開啟 WebGL，Paddle.js 可能就能跑了

---

## 版本歷史

### v1.0 — 初始版本
- 即時文字翻譯（MyMemory API）
- 20+ 語言，支援繁體中文標籤
- 版本 badge + GitHub Actions 自動更新 version
- PWA manifest（可安裝到桌面）
- Git tag: `v1.0`

---

## 技術棧

| 項目 | 技術 |
|------|------|
| 前端 | 純 HTML/CSS/JS（無框架）|
| 翻譯 API | MyMemory（免費，無需 key）|
| OCR | Paddle.js（已放棄，需 WebGL）|
| 部署 | Vercel（GitHub auto-deploy）|
| 版本管理 | Git tag + GitHub Actions |
| 備份 | rclone → Google Drive |

---

## Vercel 部署設定

- **Token**: （請向湯米索取，舊 token 已過期）
- **CLI**: `/home/tommy/.npm-global/bin/vercel`
- **部署指令**: `VERCEL_TOKEN=xxx vercel --prod`
- **專案 ID**: `tommytsaiida24-5759s-projects/translate-pwa`
