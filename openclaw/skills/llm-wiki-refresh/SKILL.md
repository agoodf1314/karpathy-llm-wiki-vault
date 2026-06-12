---
name: llm-wiki-refresh
description: "定期檢視並更新 wiki 現有頁面，確保知識不會隨時間過期或退化。基於 Rohit Ghumare v2 Memory Lifecycle 概念。"
user-invocable: true
---

# Refresh — 知識圖譜定期迭代

## 路徑（Obsidian Vault）
- Vault: `/Users/wangwenhong/obsidian/`
- Wiki: `02-wiki/`

## 觸發
- 每週一次（建議週日早上）
- 或用戶執行 `/refresh`
- Lint 時發現大量過期頁面也可觸發

## 核心概念（基於 Rohit Ghumare v2）

每個 wiki 頁面應具備以下 frontmatter 欄位：
```yaml
last_verified: YYYY-MM-DD   # 最後驗證時間
confidence: high|medium|low  # 信任等級
superseded_by: page-slug     # 被新知識取代（選填）
contradicts: page-slug        # 與某知識矛盾（選填）
```

## Refresh 流程

### Step 1：找出需要檢視的頁面
原則：優先檢視最久未更新的頁面 + 有矛盾標記的頁面

1. 讀取 `02-wiki/index.md` 獲取所有頁面列表
2. 掃描所有 `.md` 的 frontmatter `last_verified`
3. 依據以下優先順序排序：
   - 60 天以上未驗證 + confidence=medium/low → **高優先**
   - 30 天以上未驗證 → **中優先**
   - 有 `contradicts:` 標記但尚未解決 → **高優先**
   - 6 個月以上未驗證 → **高優先**

### Step 2：檢視每個目標頁面
對每個需要檢視的頁面：
1. 讀取頁面內容
2. 搜尋相關新資訊（網路搜尋該主題是否有更新）
3. 判斷是否需要更新：
   - 資訊仍正確 → 更新 `last_verified` 為今天（不改 `last_updated`）
   - 有部分過期 → 更新內容 + 同時更新 `last_verified` 和 `last_updated`
   - 已被新知識取代 → 加入 `superseded_by: [新頁面slug]`，建議設 `confidence: low`
   - 發現矛盾 → 加入 `contradicts: [矛盾頁面slug]`，兩者都保留

**欄位區分：**
- `last_updated` = 內容最後修改時間（只因內容變動而更新）
- `last_verified` = 最後驗證時間（每次 refresh 確認後更新）

### Step 3：更新 TL;DR（如有）
確保頁面頂端仍有 ≤50 字 TL;DR，沒有的話補上。

### Step 4：記錄操作日誌
在 `02-wiki/log.md` 追加：
```markdown
## [YYYY-MM-DD] refresh | 檢視了 N 個頁面
- **高優先**: [頁面列表]
- **中優先**: [頁面列表]
- **更新**: [更新了哪些頁面 + 更新內容摘要]
- **矛盾標記**: [發現了哪些矛盾]
- **備註**: [是否有需要人工決策的事項]
```

## 輸出格式
```markdown
## 🔄 知識庫迭代報告 — YYYY-MM-DD

### 📋 檢視結果
- 高優先（60天+未驗證）: N 個
- 中優先（30天+未驗證）: N 個
- 矛盾待解決: N 個

### ✅ 已更新
- [頁面名] — 更新內容摘要

### ⚠️ 已標記
- [頁面名] — 標記為 superseded_by/contradicts

### ❓ 需要人工決策
- [頁面名] — 矛盾內容說明，等待確認
```

## 觸發 Cron 建議
```json
{
  "name": "Wiki 定期迭代",
  "schedule": { "kind": "cron", "expr": "0 8 * * 0", "tz": "Asia/Taipei" },
  "sessionTarget": "isolated",
  "payload": { "kind": "agentTurn", "message": "執行 /refresh 檢查 wiki 知識庫狀態" }
}
```
每週日上午 8:00 自動執行一次。

## 硬約束
- **不刪除任何頁面**：只能標記 supersede 或降 confidence
- **矛盾只標記不覆蓋**：舊推理是資產
- **TL;DR 不可刪減**：每頁頂端必須有 ≤50 字摘要
- **操作日誌必記**：無論是否實際修改，都要在 log.md 留記錄
