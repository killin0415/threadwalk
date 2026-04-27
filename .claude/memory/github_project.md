---
name: GitHub Project / Issues 位置
description: MCIS Sprint Board 與 62 張 Issue 的位置與欄位結構,作為日後排程 / 查詢的入口
type: reference
---

**Project**:`https://github.com/users/killin0415/projects/1` (`MCIS Sprint Board`,private,user-level Project v2)

**Issues**:`killin0415/threadwalk` #1–#62,順序對應 `references/schedule.md` §5 的 ticket id(A-1..A-5, B-1..B-4, ..., M-1..M-7)。

**Project 自訂欄位**(查詢 / 設定時用):
- `Status`(預設 single-select)— 目前只有 Todo / In Progress / Done(GitHub 預設,沒改)
- `Sprint`(single-select)— Sprint 0 ~ Sprint 6
- `Story Points`(number)— 設給 dev/spike;design task 留空
- `Owner`(single-select)— D1, D2, S1–S6
- `Epic`(single-select)— Epic A 景點故事 ~ Epic M 平台部署觀測性

**Labels**:`epic-a` ~ `epic-m`(13)+ `g1` / `g2` / `g3` / `platform`(4)+ `design` / `spike`(2)= 19 個。

**重新生成腳本**:`tmp/create_project.py`(可重跑;`tmp/created_issues.json` 是 resume 狀態)。

**Why**:使用者於 2026-04-27 確認 §5 backlog 直接匯成 GitHub Project。Issue 預設**不指派 assignee**(避免 8 人收 60 封 email),Owner 寫在 body + Project field,等 Sprint Planning 再 assign。

**How to apply**:
- 查 backlog 進度:走 Project URL,不要走 Issues 清單(Project 有完整欄位)
- 改 ticket 內容:走 Issues(Project 同步顯示)
- 加新 ticket:`gh issue create` 後 `gh project item-add` 並設 4 欄位;`tmp/create_project.py` 有可參考的 helper
- Status 預設 Todo:若要走 Backlog → To Do → In Progress → In Review → Done 五段(`schedule.md` §7.3),需在 Project UI 編輯 Status field 加兩個 option(Backlog、In Review)
