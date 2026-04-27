# Agile 開發指南

> 給沒做過 agile / scrum 的隊員的入門書。
> 配套文件:`references/schedule.md`(我們**做什麼** — 6 週 sprint 計畫)與本文(我們**怎麼做** — 流程、儀式、規則)。
> 不需要從頭讀完 — 先看 §1 三分鐘版,有事再翻。

---

## 0. 給誰看

- **第一次做 agile 的隊員**:從 §1 開始讀,讀到 §5 你就能參與所有儀式
- **做過 scrum 的隊員**:跳到 §9 看我們的 GitHub Project 怎麼用、§10 看兩組怎麼合作
- **PO / SM**:全部讀,§11 / §12 / §13 是你最常用的

---

## 1. 三分鐘版:Agile / Scrum 是什麼

### 1.1 Agile 解決什麼問題?

傳統開發像蓋房子:**先把藍圖畫完整,再一次蓋好**。問題是軟體不像房子 — 你以為要的需求,在做到一半使用者一看就「啊我想要的不是這個」。藍圖一改,後面全炸。

Agile 的精神:

> **不要押三個月的賭注。每週交一個能跑的東西,看一次,改一次。**

### 1.2 Scrum 是 Agile 的一種做法

Agile 是哲學,Scrum 是「具體怎麼做」的食譜。Scrum 規定了:
- **節奏**:把工作切成 1–4 週的 sprint,每個 sprint 結束都要有可 demo 的東西
- **儀式**:每週固定的會議格式(planning / standup / review / retro)
- **角色**:誰負責什麼(PO / SM / 工程師)
- **工件**:共同維護的清單(backlog / sprint backlog / 看板)

我們用 **1 週 sprint 的 Scrum**(快節奏,適合期末專案)。

### 1.3 為什麼期末專案要用?

> 「這只是期末作業,搞這麼複雜?」

三個理由:
1. **強迫每週交 demo**:期末有死線,不能拖到最後一週才整合 — 那一定爆
2. **設計組與系統組要對齊**:沒有同步機制,設計畫到一半工程才發現 API 拿不到
3. **為將來職場暖身**:大部分軟體公司都在跑某種 agile,先熟悉流程

---

## 2. 詞彙速查

> 看到不懂的詞回來查。

| 詞 | 意思 | 在我們的專案 |
|---|---|---|
| **Sprint** | 一個固定長度的迭代週期 | 1 週 |
| **Backlog** | 「之後可能做」的工作池 | GitHub Project 裡 Status=Backlog 的 issue |
| **Sprint Backlog** | 「這個 sprint 承諾要做」的清單 | Status=To Do / In Progress / In Review 的 issue |
| **User Story** | 從使用者角度寫的功能描述 | 例:「身為旅客,我想看到景點故事,以便理解文化」 |
| **Ticket / Issue** | 一個可被分配的工作單元 | 一張 GitHub Issue,通常對應一個 story |
| **Epic** | 一群相關 stories 的集合 | Epic A 景點故事(含 A-1 ~ A-5) |
| **Story Points** | 工作大小的「相對」單位(不是小時) | Fibonacci 1, 2, 3, 5, 8, 13(詳見 §7) |
| **Velocity** | 一個 sprint 完成的點數 | 用來預測下個 sprint 能吃多少 |
| **DoR (Definition of Ready)** | story 進 sprint 前必達 | 詳見 §8 |
| **DoD (Definition of Done)** | story 結束時必達 | 詳見 §8 |
| **PO (Product Owner)** | 決定優先序的人 | D1(設計組 lead) |
| **SM (Scrum Master)** | 排除阻塞、主持儀式的人 | S1(系統組 lead) |
| **Demo Gate** | 每週五展示工作軟體的時間點 | 沒 demo 不算結束 |
| **Spike** | 「先探查」的時間盒任務 | 例:I-1 LLM prompt 工程 |
| **WIP** | Work In Progress,正在做的事 | 我們限制每人同時 ≤ 2 張 |
| **Burndown** | sprint 中剩餘工作量的下降曲線 | GitHub Projects 自動畫 |

---

## 3. 你的角色

### 3.1 工程師 / 設計師(大部分人)

**你的責任**:
- 從 Sprint Backlog 領 ticket → 做 → 交付
- 每天參加 standup,講三句話(§5.2)
- 卡住超過半天 → 主動講(SM 不會通靈)
- PR 開了 → 盯 review,不要丟著爛

**你不用煩惱**:
- 整個 sprint 要做哪些 ticket(planning 時集體決定)
- 整個 backlog 的優先序(PO 決定)
- 跨組對齊問題(SM 推進)

### 3.2 PO (Product Owner)

> 我們專案 = 設計組 lead D1

- 維護 backlog 優先序(哪個 epic 先做、哪張 ticket 砍)
- Sprint Planning 時主持「我們這週要做什麼」討論
- Demo Gate 時主持驗收(達成 sprint goal 沒?)
- 拍板需求模糊時的決定(「使用者到底要看到什麼?」)

### 3.3 SM (Scrum Master)

> 我們專案 = 系統組 lead S1

- 排除阻塞(「reviewer 不在?我去找他」)
- 主持儀式(planning, standup, review, retro)
- 看板看護人(WIP 超限時提醒)
- 維護 velocity / 回顧資料

**重點**:SM **不是 manager**,不是「指派誰做什麼」的人。SM 是「**讓團隊跑得順**」的人。

### 3.4 Tech Lead

> 我們專案 = 系統組 lead S1(兼任)

- 架構決策、API 契約把關
- code review 把關(品質基準)
- 技術選型討論時的拍板者

---

## 4. 一週的節奏

```
週一  09:30  Sprint Planning      ⏱ 60 分    [全員必到]
週一  10:30  開工 ──────────────────────────────────────┐
週二  10:00  Daily Standup        ⏱ 15 分    [全員必到] │
週三  10:00  Daily Standup        ⏱ 15 分    [全員必到] │
週三  16:00  Mid-Sprint Sync      ⏱ 30 分    [兩組代表] │  整合對齊
週四  10:00  Daily Standup        ⏱ 15 分    [全員必到] │
週五  10:00  Daily Standup        ⏱ 15 分    [全員必到] │
週五  14:00  Sprint Review (Demo) ⏱ 60 分    [全員 + 觀眾]├ 收尾
週五  15:30  Retrospective        ⏱ 30 分    [全員]      │
週五  16:30  Backlog Refinement   ⏱ 30 分    [PO + Lead] │
週五  17:00  下班 ─────────────────────────────────────┘
```

> 時間是建議,跟你的課表 / 隊員作息對齊;但**儀式種類與順序不要跳**。

---

## 5. 儀式逐個說明

### 5.1 Sprint Planning(週一,60 分)

**目的**:全員一起決定「這個 sprint 做什麼」。

**到會前你要做**:
- 看一眼 `references/schedule.md` 本 sprint 章節 — 知道大方向
- 看一眼 GitHub Project 上有哪些 ticket 是 owner=你
- 想一下:**上週有沒有沒做完的東西要先收尾?**

**會議流程**(SM 主持):

| 步驟 | 時間 | 做什麼 |
|---|---|---|
| 1. 上 sprint 回顧 | 5 分 | 「上週承諾的 8 張,做完 6 張」 |
| 2. 本 sprint goal | 5 分 | PO 講一句:「這週結束,demo 要看到 X」 |
| 3. 從 backlog 挑 ticket | 30 分 | 一張一張看,確認 owner、估點、相依;OK 才從 Backlog 拖到 To Do |
| 4. 容量檢查 | 10 分 | 每人本週 ≤ 13 點?有人爆 → 砍或挪 |
| 5. 風險 / 阻塞 | 10 分 | 有什麼一定會卡的?例:「等設計圖才能開工」 |

**輸出**:GitHub Project 上 Status=To Do 的 ticket,就是本週承諾。

**新手常見誤解**:
- ❌ 「我什麼都會,我認領 5 張」→ WIP 限制每人 ≤ 2,先做完再領
- ❌ 「我估這張 2 點」→ 別人估 5 點?**馬上對話 5 分鐘,不要安靜各做各的**。意見不同代表你們對範圍認知不一樣
- ❌ 「Planning 我不發言可以」→ 你的 ticket 要由你估、由你承諾。不發言代表你沒參與承諾

---

### 5.2 Daily Standup / 站會(每天早上 10:00,15 分)

**目的**:每天 15 分鐘,**讓全隊知道彼此在哪、誰卡住**。**不是工作報告**。

**規則**:
- 真的站著(線上開鏡頭),逼自己短
- 每人 ≤ 90 秒,**只講三句話**
- **不解決問題**(問題另約)

**三句話模板**:

```
昨天我做了:[ticket-id] [動詞] [產出]
今天我要做:[ticket-id] [動詞] [產出]
我被卡住:[阻塞 / 沒有 → 說「沒有」]
```

**範例**(好):

```
昨天我做了:A-2,寫完 Spot CRUD + 單元測試,PR 已開
今天我要做:A-3,加多語 fallback,順便 review 你的 B-1
卡住:沒有
```

**範例**(壞):

```
昨天我做了:呃,我看了 Spring Security 的文件大概兩小時,
然後試著跑 testcontainers 但是 Docker 不知道為什麼掛了,
查了 stackoverflow,結果發現…
```

→ 太長、沒有產出 / ticket id、把卡點藏在話裡。

**卡住怎麼辦?**

當場記到看板「Blocked」欄,SM 站會結束後 1 小時內找你解。**不要在站會裡 debug**。

**新手常見誤解**:
- ❌ 「我昨天沒進度,不講好了」→ 沒進度是重要訊號,要講
- ❌ 「我直接講半小時把所有事說清楚」→ 站會是同步,不是報告;延伸討論另約

---

### 5.3 Mid-Sprint Sync(週三 16:00,30 分)

**目的**:**設計組 ↔ 系統組整合對齊**,避免兩條線各做各的。

**只有兩組代表到**(D1 + S1 + 該 sprint 有交付的人),不必全員。

**討論主題(固定)**:

1. 設計交付狀態:Figma 哪幾頁 ready-for-dev?
2. API 契約:後端有沒有改規格需要前端對齊?
3. Demo Gate 風險:**到週五能不能跑通?** 不能的話現在砍什麼?

**這週的 3 號殺手**:**Sprint 開始後第 3 天 (週三) 之後只接 P0 bug,不接 enhancement**。設計變更請放下個 sprint。

---

### 5.4 Sprint Review / Demo Gate(週五 14:00,60 分)

> 這是 sprint 的高潮 — **沒 demo 不算結束**。

**目的**:把這週的工作軟體給人看 — 包含團隊外的觀眾(老師、同學)。

**會議流程**:

| 步驟 | 時間 | 做什麼 |
|---|---|---|
| 1. Sprint goal 回顧 | 2 分 | PO 講:「我們承諾的是 X」 |
| 2. Demo | 40 分 | **真機 / 真環境**跑流程,不是 PPT。看 `schedule.md` §6 各 sprint 的「Demo Script」 |
| 3. 問答 | 15 分 | 觀眾問問題,團隊回答 |
| 4. Sprint goal 達成判定 | 3 分 | PO 拍板「達成 / 部分達成 / 沒達成」 |

**Demo 規則**(重要):

- **真機跑,不是 screenshot**:跑不起來就是沒做完
- **誰做的誰 demo**:不能總是 PO / SM 一個人 demo
- **失敗也要 demo**:「我們試了 X,結果失敗,所以本週改用 Y」也是有效的 demo

**新手常見誤解**:
- ❌ 「程式碼寫完了,但還沒整合到 main,等下週」→ 沒上 main 就不能 demo,等於沒做完
- ❌ 「我做了 80% 但還沒測試,先 demo 再說」→ DoD 沒過不能 demo;砍範圍上 demo 比硬上品質爛的好
- ❌ 「demo 失敗了好丟臉」→ Demo 失敗是真實狀態,比假裝完成更有價值

---

### 5.5 Retrospective / 回顧(週五 15:30,30 分)

**目的**:**改進流程**,不是改進產品。

> 「為什麼 J-1 卡了三天 review?」
> 「為什麼 Sprint Planning 拖到 90 分?」
> 「為什麼設計圖週四才到?」

這些是 retro 的範圍。「景點故事頁排版要不要圓角?」**不是** retro 範圍。

**格式 — Start / Stop / Continue**:

```
🟢 Start  (下個 sprint 想開始做)
🔴 Stop   (下個 sprint 想停止做)
🟡 Continue (下個 sprint 想繼續做)
```

**規則**:
- 每人輪流提 1–2 條
- **對事不對人**:「PR review 平均拖 2 天」可以;「@Alice 太慢」不行
- SM 紀錄到 Notion / GitHub Wiki
- **下個 Sprint Planning 開頭必引用**,確保 retro 不只是發洩

**新手常見誤解**:
- ❌ 「我覺得沒什麼好說的」→ 沒事也要至少提 1 條 Continue(肯定的事也要記下來)
- ❌ 「retro 在抱怨而已」→ 抱怨後沒行動才叫抱怨;retro 結尾要有 1–2 條 action item

---

### 5.6 Backlog Refinement(週五 16:30,30 分)

**目的**:讓**下週**的 ticket 處於「ready 狀態」,planning 時不卡。

**只有 PO + Tech Lead 必到**,其他人視情況。

**做什麼**:
- 看 backlog 上面前 10–15 張(下個 sprint 候選),**逐張過 DoR(§8)**
- 沒過 DoR 的:補敘述、估點、拆大張、釐清驗收條件
- 估點不確定的:標 `needs-estimate`,planning 時討論

---

## 6. User Story / Issue / Ticket / Epic 的關係

容易混。一圖看懂:

```
Epic(大)
  └─ User Story / Issue / Ticket(小,= GitHub Issue 一張)
       └─ Tasks(更小,= 你 todo 上的小事,不上 GitHub)
```

| 名稱 | 在我們的專案 |
|---|---|
| **Epic** | 一群相關功能的集合,例:Epic A 景點故事(5 張 issue) |
| **User Story** | 一個從使用者角度的功能,例:「身為旅客,我想看景點故事…」 |
| **Issue / Ticket** | GitHub 上一張 Issue,通常 = 一個 user story。**Issue / Ticket 是同一件事的不同名字** |
| **Task** | Issue 內部的小步驟(寫測試、改 schema、push branch),不上 GitHub,寫在你個人 todo |

### User Story 標準格式

```
身為 [角色],
我想要 [功能],
以便 [達成目的]。
```

**範例**(我們的 A-2):

```
身為 系統組後端工程師,
我想要 Spot 與 Story 的 schema + GET API,
以便 Android 端能拉到景點故事 demo。
```

我們的 Issue title 為了易讀,通常**省略「身為…我想要…」前綴**,直接寫「A-2 後端 Spot/Story schema + GET API」。但 body 要保留三段式驗收條件。

---

## 7. Story Points — 不是工時

### 7.1 為什麼不用小時?

「這要 8 小時做完」這種估法,實務上幾乎都會錯,因為:
- 不同人速度不同(新手 8h vs 老手 2h)
- 中間會被打斷(會議、code review、同事問問題)
- 卡住一個 bug 半天就燒了

點數改成「**這件事相對於那件事大多少**」,反而比較準。

### 7.2 它衡量三件事的綜合

1. **複雜度**(邏輯難不難)
2. **工作量**(東西多不多)
3. **不確定性**(會不會卡住、要不要 spike)

### 7.3 Fibonacci 校準

| 點數 | 大概感覺 | 範例(從 backlog) |
|---|---|---|
| **1** | 半天內可完成的微任務 | 改一支 API 欄位、調 padding |
| **2** | 一天內可完成 | K-3「Android 行程確認頁加費用區塊」 |
| **3** | 1–1.5 天 | A-3「多語版本 Translation 表 + Accept-Language 路由」 |
| **5** | 2–3 天的完整 feature | A-2「Spot/Story schema + GET API」 |
| **8** | 半週,跨服務或不熟技術 | I-2「Narrative Service + Gemini + Ollama fallback」 |
| **13** | 高風險,要 spike | 我們刻意沒給任何 story 13 點 — 超過 8 就會拆 |

### 7.4 為什麼跳號?

人類對「8 vs 9」感受不出差異,但對「8 vs 13」感受得出。**跳躍式尺度逼你選邊站**,避免 5.5 / 6 / 6.5 這種無意義的精確。

### 7.5 估點的正確心態

- 不是「精確估時」,是「**對齊認知**」
- 兩個人估點差超過 2 階(例:3 vs 8)=**對範圍理解不同**,馬上講
- 估錯不改點:retro 檢討為什麼,下次校準
- 點數**不能拿來做 KPI**(會逼人灌水)

### 7.6 設計組為什麼是 task 不是點數?

因為設計工作的「**完成定義**」不像工程那麼明確 — 一張 mockup 改 3 次 vs 改 10 次差很多,但點數估不出來。所以設計組改用「**任務件數**」而非點數,Sprint 結束問「mockup 是否到 ready-for-dev 狀態」這個 binary 問題。

---

## 8. Definition of Ready / Done

### 8.1 Definition of Ready (DoR)

> 「這張 ticket **可以開始做**了嗎?」

進 Sprint(從 Backlog → To Do)前必達:

- [ ] 有清楚的 user story 敘述
- [ ] 有明確驗收條件(Given / When / Then)
- [ ] 已估點且 ≤ 8 點(否則拆)
- [ ] 有 owner
- [ ] 跨組相依已標註(設計圖 ready / API 契約 ready)

### 8.2 Definition of Done (DoD)

> 「這張 ticket **真的做完**了嗎?」

#### Dev / Spike 類

- [ ] 程式碼通過 lint(`./gradlew check` / `pnpm lint` / `uv run ruff check`)
- [ ] 單元測試覆蓋率 ≥ 80%(Service 層)
- [ ] PR 經 ≥ 1 人 code review 並 merge 到 `main`
- [ ] 已部署到 `dev` 環境(W4 之後)
- [ ] API 已在 Swagger UI 呈現(後端)
- [ ] 在 **Sprint Review 真機 / 真環境 demo 過**

#### Design 類

- [ ] Figma 標註 ready-for-dev(畫面尺寸、互動、空狀態完整)
- [ ] 與工程組對齊技術可行性
- [ ] 在 Sprint Review 走過

> **DoD 沒過,Status 不能拖到 Done**。寧可承認沒做完,也不要假完成 — 假 Done 會在期末爆。

---

## 9. GitHub Project 操作速查

> Project URL:`https://github.com/users/killin0415/projects/1`

### 9.1 第一次來,先做這些

1. 點 Project URL → 你應該看到所有 62 張 issue
2. 切到 **Sprint view**(如果有設好):只看本週的 issue
3. 用右上角的 filter:`owner: 你的代號`(D1, D2, S1...)→ 只看你的

### 9.2 看板列(Status)的意思

| 欄位 | 意思 | 你該做什麼 |
|---|---|---|
| **Backlog** | 還沒排進任何 sprint 的池子 | 不要碰,這是 PO 的領地 |
| **To Do** | 本 sprint 承諾要做,但還沒開工 | 你領了就拉進 In Progress |
| **In Progress** | 有人正在做 | 每人 ≤ 2 張 |
| **In Review** | PR 已開,等別人 review | 盯 reviewer,不要丟著 |
| **Done** | DoD 全達標,merge 進 main | 慶祝 |

### 9.3 一張 ticket 的生命週期

```
Backlog (PO 排序)
   ↓ Sprint Planning 挑進來
To Do (有 owner,等開工)
   ↓ owner 開始做(建 branch、寫 code)
In Progress (寫 code 中)
   ↓ 寫完開 PR
In Review (等 reviewer)
   ↓ PR merge
Done
```

### 9.4 你會做的操作

#### 領一張 ticket(planning 時)
- 從 Backlog 拖到 To Do
- 在 ticket 裡 @ 自己當 assignee(或讓 SM 在 planning 時統一指派)

#### 開工
- 把 ticket 拖到 In Progress
- 建 branch:`feature/A-2-spot-schema`(branch 名含 ticket id 才好追)
- 開 PR 時 body 寫 `Closes #2`(GitHub 會自動連結 + 合併時關閉 issue)

#### 卡住
- 在 ticket 留 comment:「卡在 X,需要 Y 協助」
- 站會時提
- **不要默默卡 3 天**

#### 完成
- PR merge 後,issue 會被自動關閉(因為 `Closes #2`)
- Status 自動 → Done
- 沒有 `Closes` 連結:手動把 Status 拖到 Done

### 9.5 留 comment 的時機

| 時機 | 留什麼 | 給誰看 |
|---|---|---|
| 開工前發現規格不清 | 「這個欄位允許 null 嗎?」 | PO / Tech Lead |
| 中途調整作法 | 「決定用 Postgres tsvector 而不是 ES」 | 自己 + 未來看的人 |
| 卡住 | 「等 D1 的 Figma confirm」 | 阻塞方 |
| Spike 結論 | 「Gemini 2.5 Flash 品質可接受,prompt 模板已 commit」 | 全員 |

---

## 10. 設計組 ↔ 系統組怎麼合作

### 10.1 黃金規則

> **設計永遠領先工程 0.5–1 個 sprint。**

設計這 sprint 畫的東西,工程**下個 sprint** 才實作。理由:畫到一半就丟給工程 → 工程實作到一半設計又改 → 重做。

### 10.2 交付物標準

#### 設計給工程

- **Figma 頁面標 ready-for-dev**(藍色標籤)
- 含:
  - 完整尺寸 / 間距 / 字級(可量)
  - 互動規範(hover、tap、滑動)
  - 空狀態 / loading / error 三態
  - 多語版本(中文最長 + 英文最短)
- 工程**不要實作沒標 ready 的設計** — 對方還在改

#### 工程給設計

- **OpenAPI spec 是唯一契約**(`http://localhost:8080/swagger-ui.html`)
- 設計可在 Figma 旁註標 endpoint,例:`GET /spots/{id}`
- 改規格 → **必須兩組同意**,不要單方面改

### 10.3 Mock data

兩組共用一份 `dev` 環境的 seed,**前端不自己生 mock**(避免 schema 走偏)。

### 10.4 變更窗口

Sprint 開始後第 3 天 (週三) 之後:**只接 P0 bug,不接 enhancement**。設計變更請放下個 sprint。

---

## 11. 常見情境 FAQ

### Q1:我這週 ticket 提早做完,怎麼辦?

選項(優先序):
1. **Review 別人 PR**(很常見,你會發現你的 review 速度跟著變快)
2. 從 To Do 領下一張(若已清空,跟 SM 確認可不可以從 Backlog 提前領)
3. 寫測試 / 補文件
4. 處理技術債(retro 提到的小議題)

**不要做的**:自己偷做下個 sprint 的 ticket — 沒過 planning 不算數。

### Q2:我的 PR 開了三天沒人 review,怎麼辦?

1. 站會明確點名:「@reviewer 我的 PR #42 開三天了,今天可以 review 嗎?」
2. 跟 SM 講(SM 工作之一就是推 review)
3. 翻看板,把這張 issue 的 In Review 標為「blocked-by-review」 — 給 retro 數據用

不要做的:**自己 self-merge** — 我們規定 ≥ 1 人 approve(`schedule.md` §7.4)

### Q3:我覺得這張 ticket 估點明顯不對,怎麼辦?

- **動工後發現低估**:不改點,標 `underestimated` label,retro 檢討
- **動工前發現低估**:站會講,planning 重估
- **整張範圍超出原意**:把超出部分拆成新 ticket,放下個 sprint

**不要做的**:默默做 3 天,然後 sprint 結束說「啊我做不完」。

### Q4:我跟 reviewer 對作法意見不合?

1. 在 PR 直接討論(用 GitHub review comment)
2. 30 分鐘解不了 → 約 15 分鐘 video call
3. 還解不了 → Tech Lead 拍板
4. **不要冷戰兩天 PR 卡著**

### Q5:設計圖跟我實作的不一樣,我以哪個為準?

- 看 Figma 是否標 **ready-for-dev**
  - 是 → 以 Figma 為準,有差異是你的 bug
  - 否 → 設計還沒定,先實作骨架,Figma ready 後再對齊細節
- **不確定就在 ticket 留 comment**,不要憑感覺猜

### Q6:站會我臨時不能到怎麼辦?

- 在群組丟「三句話」(昨天/今天/卡點),時間是站會時段內
- **不要消失** — 隊員會擔心你進度

### Q7:我 sprint 中途請假 / 期中考?

- 在 planning 時就提,容量降低
- ticket 提前交給隊員(不是「我回來再說」)

### Q8:Demo 時某張 ticket 跑不起來?

- **誠實展示** — 「我們本來要 demo X,但 Y 卡住,所以改 demo 部分結果」
- 假裝跑得起來會被當場拆穿,**遠比承認沒做完丟臉**

### Q9:我覺得 retro 沒什麼好提的?

- 至少提一條 **🟡 Continue**(肯定的事)
- 想不出來?問自己:「如果下週重來一次,我會做一樣的事嗎?」哪裡會改 = retro 素材

---

## 12. 紅燈:這些事情不要做

### 🔴 流程上

- **不要邊 planning 邊改 ticket 規格** — Refinement 應該在前一週做完
- **不要在 standup 解 bug** — 另約
- **不要 self-merge PR** — 至少 1 人 approve
- **不要假 Done** — DoD 沒過硬拖到 Done = 期末爆
- **不要默默卡 > 半天** — 卡住是訊號,不是丟臉
- **不要為了週五 demo 跳測試** — 砍範圍比品質爛好

### 🔴 工具上

- **不要直接 push 到 main** — 一律走 PR
- **不要 force push 共享 branch** — 會覆蓋別人工作
- **不要 commit `.env`** — 已 gitignore,但仍要警覺
- **不要在 issue 描述貼大段對話 log** — 摘要結論就好,細節在 PR

### 🔴 心態上

- **不要「我覺得這樣比較好」就改架構** — 跟 Tech Lead 講過再改
- **不要把「我做完了」當作「DoD 過了」** — 看 §8.2 清單一條條檢
- **不要把 retro 當吐槽大會** — 對事不對人,結尾要有 action

---

## 13. 不確定時去哪

| 困惑類型 | 找誰 / 看哪 |
|---|---|
| 「這張 ticket 範圍是什麼?」 | PO(D1)or 在 ticket 留 comment |
| 「這個架構決策合理嗎?」 | Tech Lead(S1) |
| 「我卡住了」 | 站會 / SM(S1) |
| 「這個流程為什麼這樣?」 | 本文 / `references/schedule.md` |
| 「程式碼規範?」 | `CLAUDE.md` §4 |
| 「部署 / CI 怎麼做?」 | `CLAUDE.md` §7 / §10 |
| 「需求是什麼?」 | `references/requirements.md` |
| 「系統怎麼設計的?」 | `references/architecture.md` / `references/diagram.md` |
| 「這個 sprint 計畫是什麼?」 | `references/schedule.md` 對應 sprint |

---

## 附錄 A:相關文件

- `references/requirements.md` — 完整需求(UC1–UC18, FR-01–FR-23, NFR-01–NFR-20)
- `references/architecture.md` — 系統架構與 service 分工
- `references/diagram.md` — Activity / Domain Class / Sequence Diagram
- `references/schedule.md` — 6 週 sprint 進度表(隨 retro 更新)
- `CLAUDE.md` — 開發準則(編碼規範、工具、Git、CI/CD)
- GitHub Project:`https://github.com/users/killin0415/projects/1`
- GitHub Repo:`https://github.com/killin0415/threadwalk`

---

## 附錄 B:推薦延伸閱讀(選讀,各約 10–30 分鐘)

- [Scrum Guide(官方,2020 版)](https://scrumguides.org/scrum-guide.html) — Scrum 的權威定義
- [User Story Mapping(Jeff Patton)](https://www.jpattonassociates.com/the-new-backlog/) — Story 與 Epic 的視覺化
- [The Phoenix Project](https://itrevolution.com/product/the-phoenix-project/) — 小說形式的 DevOps + Agile 實戰

---

> 本文件是活的。流程在 retro 中改,本文件也跟著改。**有疑問就 PR 改它**,不要當作天條。
