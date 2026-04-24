# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 開發方式

無需建置步驟。直接用瀏覽器開啟 `gym_planner.html` 即可：

```bash
open "gym_planner.html"
```

修改後儲存檔案，重新整理瀏覽器（Cmd+R）即可看到結果。

## 專案架構

整個應用為單一 HTML 檔案（`gym_planner.html`），分三層：

**CSS（`<style>`）**  
使用 CSS 變數（`:root`）統一管理配色，主題為深色（`--bg`, `--card`, `--card2`, `--border`）搭配橘色主色（`--accent: #f97316`）。

**HTML**  
四個 tab panel（`tab-calendar` / `tab-pr` / `tab-preset` / `tab-profile`），以及一個新增/編輯運動的 modal。Tab 切換由 `switchTab(name)` 控制，預設顯示 `tab-calendar`。

**JavaScript（`<script>`）**  
- **State**：四個全域物件 `workouts`、`presets`、`daily`、`profile`
- **持久化**：全部透過 `localStorage` key `amberGym2` 存取，`loadData()` 讀入、`persist()` 寫出
- **資料結構**：
  - `workouts`：`{ "YYYY-MM-DD": [{ id, name, weight, unit, sets, reps, note }] }`
  - `daily`：`{ "YYYY-MM-DD": { water: number, bubbleTea: boolean|null } }`
  - `presets`：`[{ id, name, icon, exercises: string[] }]`，預設值為 `DEFAULTS` 常數
  - `profile`：身體數據、目標、initWeight/initBodyFat（用於進度計算）

## 重要設計細節

- **週曆**：`renderWeek()` 每次完整重建 DOM，不做局部更新
- **PR 紀錄**：從 `workouts` 即時運算，無獨立快取
- **BMI 色條**：scale 固定為 15–35，針位置 = `(BMI - 15) / 20 * 100%`
- **目標進度 %**：需要 `initWeight`/`initBodyFat` 才能計算，第一次設定目標時自動從當前值記錄
- **DOG_SVG**：header 與個人檔案頁共用同一 SVG 常數，header 用時注入 `width="34" height="34"` 屬性
- **`esc()`**：所有使用者輸入插入 HTML 前必須經過此函式做 XSS 防護
