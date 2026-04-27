---
name: Use Case 優先順序分組
description: 使用者指定的三組 UC 優先順序,決定 sprint 排程與功能裁切先後
type: project
---

**G1 必做核心**(各組內順序代表優先序):UC3 → UC2 → UC10 → UC6
**G2 主功能**:UC5 → UC4 → UC13 → UC8 → UC11 → UC12 → UC14
**G3 維運**:UC16 → UC17 → UC18
**未列(降級)**:UC1, UC7, UC9, UC15, UC17 等;但 UC9(足跡地圖回顧)需做最小版才能支撐 UC10/UC11。

**Why**:使用者於 2026-04-27 確認此分組以排 6 週 agile schedule(`references/schedule.md`)。理由是 G1 是「必須走通的 thin slice」,G2 補完旅客旅程,G3 後台用 react-admin/Refine 撐起來不從零刻。

**How to apply**:
- 排 sprint 時 G1 先做 (Sprint 1–2),G2 中段 (Sprint 3–4),G3 後段 (Sprint 5)
- 砍範圍時從未列 UC 開始砍,接著砍 G3,最後才動 G2
- UC10 雖在 G1 但需要足跡資料,實作時先用 mock footprint(Sprint 2)再切真實(Sprint 4 接 UC8)
- UC4 依需求 `<<include>>` UC14,實作上一起做不分開
