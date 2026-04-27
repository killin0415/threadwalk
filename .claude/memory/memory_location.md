---
name: Memory 路徑偏好
description: 此專案的 memory 一律放專案內 .claude/memory/,不放電腦根目錄
type: feedback
---

**規則**:此專案 memory 一律存 `E:\threadwalk\.claude\memory\`(專案內),不放 `C:\Users\potato\.claude\projects\E--threadwalk\memory\`(電腦根目錄)。

**Why**:使用者於 2026-04-27 說明會在**兩個裝置**上開發,memory 走 git 同步才不會分裂。`.claude/settings.local.json` 已在 `.gitignore`,但 `.claude/memory/` 不在,可隨 repo 同步。

**How to apply**:
- 寫新 memory:一律寫到 `E:\threadwalk\.claude\memory\<file>.md`
- 更新索引:同步更新 `E:\threadwalk\.claude\memory\MEMORY.md`
- 不要寫回 `~/.claude/projects/.../memory/`(該位置已清空)
- CLAUDE.md §0 已加覆寫指令,新 session 啟動會先讀本資料夾
