# 婚禮報到系統 — Session 交接文件

## 專案基本資訊
- **Repo**: `i-tzu/wedding-checkin`
- **開發分支**: `claude/magical-hawking-xjv6fr`
- **PR**: https://github.com/I-TZU/wedding-checkin/pull/1
- **目標檔案**: `Claude/Index.html`（單一 HTML，無 build tools）
- **GitHub Pages 網址**: `https://i-tzu.github.io/wedding-checkin/Claude/Index.html`

---

## 技術架構
- 單一 HTML 檔，Vanilla JS，無框架
- **字型**: Noto Serif TC（標題）+ Noto Sans TC（內文），Google Fonts CDN
- **QR 掃描**: `https://unpkg.com/html5-qrcode`
- **GAS API**: `https://script.google.com/macros/s/AKfycbzjNib56lrJMKki__XWhkOjiDOJn3a1sDkH8TveFQ8D4KSkjWJ394WnwwhDwsS2gkHd/exec`
- API 回應格式：`{ success: true, guests: [...], tables: [...] }`
- Guest 欄位：`id, name, table, status("已報到"/"未報到"), groupId`
- Table 欄位：`table_id, table_name`
- 離線佇列 key：`wedding_sync_queue`（localStorage）

---

## 設計風格
古典中式喜慶風，CSS 變數：
```css
--ivory:   #fdf8f2;
--card:    #fffcf7;
--border:  #e0c898;
--red:     #8c1a1a;
--red2:    #b02020;
--gold:    #b8860b;
--goldbg:  #fffbee;
--ink:     #2c1810;
--sub:     #7a5c48;
--green:   #2d6a4f;
--greenbg: #f0faf4;
```

---

## 已實作功能（目前 Claude/Index.html）
- 古典中式 UI（囍字標頭、米色/硃砂/古金配色）
- 桌機：左右雙欄（5fr:7fr）
- 手機/平板：底部 Tab 切換（搜尋報到 / 座位圖）
- 報到後自動切換到座位圖 Tab 並高亮桌次
- QR 掃描（iOS Safari 相容）
- 群組報到 Modal（底部彈出）
- 分桌警告 + 座位分配卡
- 防呆：未報到預設打勾、已報到只顯示取消按鈕
- 樂觀 UI + 離線佇列（5 秒背景重試）
- 雲端同步狀態徽章

---

## 座位圖待修正（模擬圖已確認 V9，尚未寫入 Index.html）

### Grid 結構
```css
.floor-grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr 12px 1fr;  /* col5 = aisle */
  grid-template-rows: 1fr 1fr 12px 1fr 1fr;           /* row3 = aisle */
  gap: 8px;
}
```

### 走道（L 型，單一元素）
```css
.aisle-L {
  grid-row: 3 / span 3;
  grid-column: 2 / span 4;
  border-top: 2px solid rgba(184,134,11,.6);
  border-right: 2px solid rgba(184,134,11,.6);
  pointer-events: none;
}
```
- 用 `border-top + border-right` 畫 L，轉角必定相連
- 無「走道」文字

### 桌次 Grid 位置
```javascript
const GRID_POS = {
  "主桌": "grid-row:1/span 5;grid-column:1;align-self:center;height:60%",
  "1":  "grid-row:1;grid-column:2",
  "2":  "grid-row:1;grid-column:3",
  "3":  "grid-row:1;grid-column:4",
  "5":  "grid-row:1;grid-column:6",
  "6":  "grid-row:2;grid-column:2",
  "7":  "grid-row:2;grid-column:3",
  "8":  "grid-row:2;grid-column:4",
  "9":  "grid-row:2;grid-column:6",
  "10": "grid-row:4;grid-column:2;transform:translateY(calc(50% + 4px))", // 在11和15中間
  "11": "grid-row:4;grid-column:3",
  "12": "grid-row:4;grid-column:4",
  "13": "grid-row:4;grid-column:6",
  "15": "grid-row:5;grid-column:3",
  "16": "grid-row:5;grid-column:4",
  "17": "grid-row:5;grid-column:6",
};
```

### 搜尋結果顯示
- 每人各自一張卡片（不合併同組姓名）
- 顯示「同組：xxx」標籤
- 點任一張卡片 → 開啟同組所有人的報到 Modal

---

## Git 注意事項
```bash
# 每次 commit 前設定
git config user.email noreply@anthropic.com
git config user.name Claude

# Push
git push -u origin claude/magical-hawking-xjv6fr
```

---

## 下一步工作
將座位圖修正（走道 L 型、桌10位置、搜尋卡片分離）實際寫入 `Claude/Index.html` 並 push 到 PR #1。
