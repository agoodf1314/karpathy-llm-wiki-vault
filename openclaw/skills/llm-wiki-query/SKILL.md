---
name: llm-wiki-query
description: "在本地 Wiki 知識庫中回答用戶提問。先讀 index.md 定位，再深度閱讀，以雙鏈引用格式回答。"
user-invocable: true
---

# Query — 知識查詢

## 路徑（Obsidian Vault）
- Vault: `/Users/wangwenhong/obsidian/`
- Wiki: `02-wiki/`

## 觸發
用戶詢問「我的筆記/歷史決定/知識庫」相關內容時自動觸發。

## 流程

### Step 1：查閱全域索引
**永遠第一步**：讀取 `02-wiki/index.md`

在 index.md 中定位相關的 Entities/Concepts/Sources/Syntheses。

### Step 2：深度閱讀目標檔案
用 `read` 工具讀取 Step 1 找到的最相關頁面。

### Step 3：綜合回答
使用 `[[頁面名稱]]` 雙鏈標注引用來源。

### Step 4：高價值內容固化
當回答超過 2 段落且有分析對比性時，主動詢問是否保存至 `02-wiki/syntheses/`。

### Step 5：記錄操作日誌
在 `02-wiki/log.md` 追加：
```markdown
## [YYYY-MM-DD] query | 操作簡述
- **輸出**: 引用頁面列表或「即時回答未保存」
```

## TL;DR 規範
回答前可先掃描相關頁面的 TL;DR（頂端 ≤50 字摘要），快速判斷相關性。

## 強制約束
- **禁止憑記憶回答**：必須先檢索知識庫
- **知識庫無相關內容時必須聲明**：本地知識庫中未找到相關內容，以下為通用知識回答
- **每個回答必須使用 `[[雙鏈]]` 標注引用來源**
