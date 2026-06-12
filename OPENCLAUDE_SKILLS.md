# OpenClaw LLM Wiki Skills

## 存放位置

```
karpathy-llm-wiki-vault/
├── OPENCLAUDE.md              # OpenClaw 版核心規範（主定義檔）
└── openclaw/
    └── skills/
        ├── llm-wiki-ingest/   # 知識攝取
        ├── llm-wiki-query/    # 知識查詢
        └── llm-wiki-lint/     # 健康巡檢
```

## 與 Claude Code 版本的差異

| 項目 | Claude Code 版 | OpenClaw 版 |
|------|--------------|------------|
| Skill 目錄 | `.claude/skills/` | `openclaw/skills/` |
| Vault 路徑 | `raw/`、`wiki/` | `~/obsidian/01-raw/`、`~/obsidian/02-wiki/` |
| 觸發方式 | `/ingest` 等命令 | OpenClaw trigger + `user-invocable: true` |
| 語言 | 簡體中文 | 繁體中文 |
| 工具呼叫 | Claude Code 內建 | OpenClaw `read`、`exec`、`write` 等工具 |

## 三個核心 Skill

### 1. Ingest（知識攝取）
將 `01-raw/` 原始資料編譯到 `02-wiki/` 並歸檔。

**流程：** 掃描 → 讀取 → 提煉核心 → 建立 Sources 摘要 → 建立/更新實體或概念頁面 → 更新 index.md + log.md → 歸檔至 `09-archive/`

**注意：** 步驟 3（Sources 摘要）和步驟 7（歸檔）最容易漏做。

### 2. Query（知識查詢）
在本地 Wiki 知識庫中回答用戶提問。

**流程：** 查閱 index.md → 深度閱讀 → 雙鏈引用回答 → 高價值內容固化 → 記錄 log.md

**強制約束：** 禁止憑記憶回答，必須先檢索知識庫。

### 3. Lint（健康巡檢）
掃描 wiki/ 檢測死鏈、孤兒頁面、未同步索引和知識衝突。

**流程：** 索引一致性檢查 → 雙向連結健康檢查 → 認知衝突審查 → 輸出結構化報告

## Vault 路徑（OpenClaw 版）

```
/Users/wangwenhong/obsidian/
├── 01-raw/              # 原始資料收件箱（只讀）
│   ├── 01-articles/     # 網頁剪藏文章
│   ├── 02-papers/       # 論文/PDF 文獻
│   ├── 03-transcripts/  # 影片轉錄文案
│   └── 09-archive/      # 已處理檔案歸檔
└── 02-wiki/             # AI 編譯輸出（可自由寫入）
    ├── sources/          # 資料摘要
    ├── entities/         # 實體（人物、公司、工具、產品）
    ├── concepts/         # 概念（框架、方法論、理論）
    ├── syntheses/        # 綜合報告
    ├── projects/         # 工作專案
    ├── index.md         # 全域字典
    └── log.md           # 操作日誌
```

## 衝突處理原則

發現新舊知識衝突時：
1. **暫停** ingest 流程
2. **報告** 衝突內容
3. **詢問** 用戶選擇：
   - A) 保留兩者，標注為「知識衝突」
   - B) 用新知識覆蓋舊知識
   - C) 放棄本次攝入

## 同步三層原則

當對系統流程提出問題時，應同步更新三層：

| 層 | 檔案 |
|----|------|
| 規則層 | `AGENTS.md` |
| 流程層 | `llm-wiki-ingest/SKILL.md` 等 |
| 知識層 | `02-wiki/syntheses/karpathy-to-openclaw-adapted-skills.md` |

## 相關檔案

- OpenClaw 版核心規範：`openclaw/OPENCLAUDE.md`（也在 workspace 有副本）
- 系統重建記錄：`~/obsidian/02-wiki/syntheses/OpenClaw-6.x-系統重建記錄-2026-06-12.md`
