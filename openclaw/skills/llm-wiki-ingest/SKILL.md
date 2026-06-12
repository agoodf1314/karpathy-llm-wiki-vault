---
name: llm-wiki-ingest
description: "將 ~/obsidian/01-raw/ 原始資料攝入到 02-wiki/ 並歸檔。處理完成後將源文件移至 09-archive/。"
user-invocable: true
---

# Ingest — 知識攝取

## 路徑（Obsidian Vault）
- Vault: `/Users/wangwenhong/obsidian/`
- Raw: `01-raw/`
- Wiki: `02-wiki/`

## 觸發
- 說「攝入」、「攝取」、「導入」資料時自動觸發
- 或明確要求時觸發

## 流程

### Step 1：掃描待處理檔案
掃描 `01-raw/`（排除 `09-archive/`），找出未歸檔的檔案。

### Step 2：讀取源文件
- `.md` → `read` 工具
- `.pdf` → `pdf` 工具，失敗則記錄元資訊

### Step 3：提煉核心
- 核心主旨（1-2句話）
- 實體（人物、公司、工具、產品）
- 概念（框架、方法論、理論）

### Step 4：建立 Sources 摘要
`02-wiki/sources/摘要-{slug}.md`

```markdown
---
title: "摘要-檔案slug"
type: source
tags: [來源, 原始文件]
sources: [raw/01-articles/xxx.md]
last_updated: YYYY-MM-DD
last_verified: YYYY-MM-DD
confidence: high
---

## TL;DR
[≤50字的核心摘要]

## 核心摘要
[3-5句話總結]

## 關聯連接
- [[EntityName]]
- [[ConceptName]]
```

### Step 5：建立/更新實體或概念頁面（Ripple Effect）
- 實體 → `02-wiki/entities/`
- 概念 → `02-wiki/concepts/`

**每個 ingest 平均會觸碰 8-12 個現有頁面**，不只是建一個新頁面：
- 檢查相關現有 entity/concept 頁面是否需要加雙鏈
- 檢查是否與現有頁面矛盾，若矛盾 → 加入 `contradicts: [頁面slug]`，**兩者都保留，不覆蓋**

### Step 6：更新 index.md + log.md
- index.md：新增 Sources/Entities/Concepts 條目
- log.md：Append 操作日誌，註明「觸碰了 N 個現有頁面」

### Step 7：歸檔
移至 `01-raw/09-archive/`

**禁止修改源文件內部文字。**

## 注意事項
- 絕對不讀取 `09-archive/` 下的任何檔案
- 每個 wiki 頁面必須包含 `## 關聯連接` 區塊
- 每個 wiki 頁面頂端必須有 `## TL;DR`（≤50字）
- 不得產生孤島頁面
- **Contradiction Protocol**：發現衝突 → 加 `contradicts:` 標記，兩者都保留，絕不覆蓋舊知識