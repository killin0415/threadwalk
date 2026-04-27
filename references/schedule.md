# 開發進度表 (Agile / Scrum)

> 專案:外國旅客在台灣初期探索文化的體驗設計(MCIS Project)
> 期程:**6 sprints × 1 週 = 6 週**(可壓縮版見 §11)
> 團隊:**設計組 2 人 + 系統組 4–6 人**
> 框架:**1 週 Sprint 的 Scrum**,輔以 Kanban 板與每日站會
> 來源依據:`requirements.md`、`architecture.md`、`diagram.md`、`CLAUDE.md`

---

## 1. 文件目的

把 use case 優先級轉換成「**每週可 demo 的工作軟體**」。每個 sprint 都要產出能整合走通的功能切片(thin slice),不是拆 layer 各做各的。

**優先順序(使用者指定)**

| 群組 | UC 順序 | 對應主要 Epic |
|---|---|---|
| **G1 必做** | UC3 → UC2 → UC10 → UC6 | Epic A 景點故事、Epic B 主題瀏覽、Epic C 自動分類、Epic D 現場導航 |
| **G2 主功能** | UC5 → UC4 → UC13 → UC8 → UC11 → UC12 → UC14 | Epic E 路線推薦、Epic F 地圖式行程、Epic G 多語、Epic H 足跡記錄、Epic I 敘事生成、Epic J 分享、Epic K 費用預估 |
| **G3 維運** | UC16 → UC17 → UC18 | Epic L Admin CMS |
| **未列(降級)** | UC1, UC7, UC9, UC15, UC17 | UC9 仍需最小版以支撐 UC10/UC11 |

---

## 2. Agile 框架

### 2.1 Sprint Cadence(週循環)

| 時間 | 儀式 | 時長 | 出席 | 產出 |
|---|---|---|---|---|
| **週一 09:30** | Sprint Planning | 60 分 | 全員 | Sprint Goal、選好的 Stories、各自 ticket |
| **週一–週五 10:00** | Daily Standup(站會) | 15 分 | 全員 | 昨日 / 今日 / 阻塞 三句話 |
| **週三 16:00** | Mid-Sprint Sync(整合對齊) | 30 分 | 兩組代表 | API 契約 / Figma 交接確認 |
| **週五 14:00** | Sprint Review / Demo Gate | 60 分 | 全員 + 外部觀眾(老師 / 同學) | 工作軟體 demo、是否達成 Sprint Goal |
| **週五 15:30** | Sprint Retrospective | 30 分 | 全員 | Start / Stop / Continue 三欄 |
| **週五 16:30** | Backlog Refinement | 30 分 | PO + Tech Lead | 下週 stories 估點 + 拆分 |

### 2.2 角色

| 角色 | 由誰兼任 | 職責 |
|---|---|---|
| **Product Owner (PO)** | 設計組 lead(D1) | 維護 Backlog 優先序、定義驗收條件、Demo Gate 主持 |
| **Scrum Master (SM)** | 系統組 lead(S1) | 排除阻塞、儀式主持、追 burndown |
| **Tech Lead** | 系統組 lead(S1)兼任 | 架構決策、API 契約把關、code review 把關 |
| **設計組成員 (D1, D2)** | — | UI/UX、prototype、內容種子、可用性測試 |
| **系統組成員 (S2–S6)** | — | 開發、測試、部署 |

> 角色可兼任,但**不要讓同一人同時是 PO + SM**(會自我衝突)。

### 2.3 工件 (Artifacts)

- **Product Backlog**:全部 Epic / Story 的優先序清單(本文件 §5)
- **Sprint Backlog**:本 Sprint 選入的 Stories + 各自拆出的 Tasks
- **Increment**:Sprint 結束時可 demo 的工作軟體
- **看板 (Kanban Board)**:`Backlog → To Do → In Progress → Review → Done`,建議用 GitHub Projects 或 Linear

### 2.4 估點

採 **Fibonacci Story Points**(1, 2, 3, 5, 8, 13);> 13 點必須拆。

**參考校準**:
- 1 點 = 半天內可完成的微任務(改一支 API 欄位、調 padding)
- 3 點 = 一個典型 CRUD endpoint + 對應前端整合
- 5 點 = 一個完整 feature(地圖頁含拖曳排序)
- 8 點 = 跨服務整合或不熟悉技術(LLM prompt 工程)
- 13 點 = 風險高,要 spike 探查

**單人 velocity 預估**:後端 / Android ≈ 10–13 點 / sprint;設計組以 task 計數,不估點。

### 2.5 Definition of Ready (DoR) — Story 進 Sprint 前必達

- [ ] 有清楚的 user story 敘述(身為 X,我想要 Y,以便 Z)
- [ ] 有明確的驗收條件(Given / When / Then)
- [ ] 已估點且 ≤ 8 點(否則拆)
- [ ] 有 owner
- [ ] 跨組相依已標註(設計圖 ready / API 契約 ready)

### 2.6 Definition of Done (DoD) — Story 結束時必達

- [ ] 程式碼通過 lint(`./gradlew check` / `pnpm lint` / `uv run ruff check`)
- [ ] 單元測試覆蓋率 ≥ 80%(Service 層);整合測試走 happy path
- [ ] PR 經至少 1 人 code review 並 merge 到 `main`
- [ ] 已部署到 `dev` 環境(W4 之後)
- [ ] 對應 API 已在 Swagger UI 呈現(後端)
- [ ] 對應元件已在 Figma 標註交付狀態(設計)
- [ ] 在 **Sprint Review** 真機 / 真環境 demo 過

---

## 3. 團隊組成與分工

### 3.1 設計組(2 人)

| 代號 | 角色 | 主責 |
|---|---|---|
| **D1** | Lead Designer / PO | UI 設計系統、視覺主導、Sprint Review 主持、使用者測試規劃 |
| **D2** | Content / UX Researcher | **景點內容種子(訂閱 LLM 協作生成)**、可用性測試、跨文化類比文案(NFR-02)、admin 視覺 |

### 3.2 系統組(4–6 人,依到位狀況)

| 代號 | 角色 | 4 人版 | 5 人版 | 6 人版 |
|---|---|---|---|---|
| **S1** | Backend Lead / Tech Lead / SM | ✅ Spring Boot + 架構 | ✅ | ✅ |
| **S2** | Backend Engineer | ✅ Content / Route / Trip Service | ✅ | ✅ |
| **S3** | Android Engineer | ✅ Compose + 地圖 | ✅ | ✅ |
| **S4** | AI / Python Engineer | ✅ 兼 Admin CMS(分時) | ✅ Narrative + Classification | ✅ |
| **S5** | Admin CMS / Frontend | — | ✅ React + react-admin | ✅ |
| **S6** | DevOps / Android #2 | — | — | ✅ K8s + Android UI 雙線 |

> **4 人版**:S4 同時做 AI service 與 Admin CMS,Admin 只做 Sprint 5 的核心三頁;Android 沒有 backup,休假 / 卡 issue 風險最高。
> **5 人版(建議下限)**:Admin 與 AI 拆分,品質有保障。
> **6 人版(理想)**:可做 PWA Kiosk + 完整 K8s + 觀測性。

---

## 4. Sprint 全景圖

```
Sprint 0 (週 0, 0.5 週準備)
   ↓ 環境就緒 + 內容種子第一批
Sprint 1 (週 1) ─ Epic A + B 起 ─ Demo: 看到景點故事
   ↓
Sprint 2 (週 2) ─ Epic C + D ─ G1 完成 ─ Demo: 導航 + 分類
   ↓
Sprint 3 (週 3) ─ Epic E + F + G ─ Demo: 規劃 + 多語
   ↓
Sprint 4 (週 4) ─ Epic H + I + J + K ─ G2 完成 ─ Demo: 敘事 + 分享
   ↓
Sprint 5 (週 5) ─ Epic L + 部署 ─ G3 完成 ─ Demo: Admin 上線 + K8s
   ↓
Sprint 6 (週 6) ─ Polish + 整合 + Final Demo
```

每個 Sprint 結束都必須能 demo;**沒 demo 不算結束**。

---

## 5. Product Backlog(全 Epic 概覽)

> 估點為**單一 owner 的工作量**。多 owner 協作時,各 owner 個別計入。

### Epic A — 景點文化故事 (UC3 / FR-05)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| A-1 | **內容種子**:台南 16 個景點故事(時代/宗教/功能 三段式) × 繁中 + 英文 | task | D2 | S0–S1 |
| A-2 | 後端:`Spot`/`Story` schema(Flyway V1)+ `GET /spots/{id}` API | 5 | S2 | S1 |
| A-3 | 後端:多語版本 `Translation` 表 + `Accept-Language` 路由 | 3 | S2 | S1 |
| A-4 | 設計:景點故事頁 high-fi(故事化排版 + 圖像規範 + 多語切換) | task | D1 | S1 |
| A-5 | Android:`SpotDetailScreen`(Compose)+ 多圖載入(Coil) | 5 | S3 | S1 |

### Epic B — 主題化景點瀏覽 (UC2 / FR-03 / FR-03a)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| B-1 | 後端:`Tag`/`SubTag` 階層 schema + 多選查詢 API | 5 | S1 | S1 |
| B-2 | 後端:Postgres `tsvector` 全文檢索(暫不引 ES) | 3 | S1 | S1 |
| B-3 | 設計:主題瀏覽頁 + 多主題複選 + 子標籤層次互動 | task | D1 | S1 |
| B-4 | Android:`SpotListScreen` + Tag chip 多選 + 篩選列 | 5 | S3 | S1 |

### Epic C — 自動分類已訪景點 (UC10 / FR-11)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| C-1 | Python:Classification Engine 規則式版(依 `SpotCategory` enum 直接歸類) | 5 | S4 | S2 |
| C-2 | 後端:`POST /footprints/classify` API + 6 筆 mock footprint seed | 3 | S2 | S2 |
| C-3 | 設計:分群視覺化(地圖 cluster + 環圈統計圖雙視圖) | task | D1 | S2 |
| C-4 | Android:足跡分群圖表(MPAndroidChart 或 Compose canvas) | 5 | S3 | S2 |
| C-5 | (S5+) 升級:依 LLM embedding + cosine 分群,留待 Sprint 6 增強 | 8 | S4 | S6(若有空) |

### Epic D — 現場導航 (UC6 / FR-07)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| D-1 | 後端:Navigation Service + `GET /navigation/route?from=&to=` | 5 | S1 | S2 |
| D-2 | Spike:OSM + Leaflet/MapLibre 對台南路徑準確度驗證(8 條 demo 路線) | 3 | S1 | S2 |
| D-3 | 後端:整合台灣 PTX/TDX 公車與山路備援 | 8 | S1 | S2 |
| D-4 | 設計:導航 UI(目前位置 → 下一景點 + 移動中故事卡 placeholder) | task | D1 | S2 |
| D-5 | Android:`MapScreen` + Google Play Services Location + 同意 flow(NFR-14) | 8 | S3 | S2 |

### Epic E — 主題路線推薦 (UC5 / FR-03 / FR-22)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| E-1 | 後端:Route Planning Service + `Route` / `RouteType` schema | 5 | S2 | S3 |
| E-2 | 後端:跨時期路線演算法(`isCrossEra=true` 時混搭不同 era) | 5 | S2 | S3 |
| E-3 | 設計:路線推薦卡片(美食/建築/歷史 模板) | task | D1 | S3 |
| E-4 | Android:`RouteCardScreen` + 套用至 Trip 流程 | 3 | S3 | S3 |

### Epic F — 地圖式行程規劃 (UC4 / FR-04 / FR-15 / FR-16)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| F-1 | 後端:Trip & Itinerary Service(`createTrip` / `addSpot` / `reorder`) | 8 | S1+S2 | S3 |
| F-2 | 後端:`TripItem.estimateCost` + `estimatePace`(費用 / 節奏) | 5 | S2 | S4(K) |
| F-3 | 設計:地圖式行程 UI — **不同 tag 顏色路徑** + 拖曳排序 + 時間軸並陳 | task | D1 | S3 |
| F-4 | Android:行程地圖頁 + 拖曳排序(Compose `Modifier.dragAndDrop`) | 8 | S3 | S3 |

### Epic G — 多語切換 (UC13 / FR-09 / NFR-01 / NFR-02)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| G-1 | 後端:i18n Service + 內容多語 fallback 規則 | 3 | S1 | S3 |
| G-2 | 設計:Locale Switcher 元件 + 文案表 v1(繁中/英) | task | D2 | S3 |
| G-3 | Android:`AppCompatDelegate.setApplicationLocales()` + `strings.xml` 雙語 | 3 | S3 | S3 |
| G-4 | 內容:跨文化類比文案校對(廟宇 / 割包 / 赤崁樓)— NFR-02 | task | D2 | S3–S4 |

### Epic H — 足跡記錄 (UC8 / FR-10 / FR-21)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| H-1 | 後端:Footprint Service + `POST /footprints` + 標籤集合 + 心情關鍵字 | 5 | S2 | S4 |
| H-2 | 後端:UGC tag 提交 + pending 審核 flag(FR-21,管理員審核留 S5) | 3 | S2 | S4 |
| H-3 | 設計:標籤式快速記錄 UI(**不強制打字** FR-10)+ 心情選單 | task | D1 | S4 |
| H-4 | Android:`AddFootprintBottomSheet` + 照片拍攝 / 選取 | 5 | S3 | S4 |
| H-5 | UC9 最小版:足跡地圖頁(支撐 UC10/UC11 入口) | 3 | S3 | S4 |

### Epic I — 旅程敘事生成 (UC11 / FR-13)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| I-1 | Spike:Gemini 2.5 Flash 與 Ollama 雙路 prompt 工程 + 比對品質 | 5 | S4 | S3 末–S4 初 |
| I-2 | Python:Narrative Service + `POST /narratives` + Gemini 主路 / Ollama fallback | 8 | S4 | S4 |
| I-3 | 後端:Narrative 結果快取進 Redis(NFR-05)+ 重試政策 | 3 | S1 | S4 |
| I-4 | 設計:**明信片式敘事預覽**(FR-13 寶可夢蒐集意象) | task | D1 | S4 |
| I-5 | Android:敘事預覽頁 + loading skeleton(LLM 5–10s 等待視覺) | 5 | S3 | S4 |

### Epic J — 分享 (UC12 / FR-14 / NFR-15)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| J-1 | 後端:Share Service + 短連結(自架 nanoid)+ 匿名化開關 | 5 | S2 | S4 |
| J-2 | 後端:出圖(Pillow / Java BufferedImage)生成分享卡 | 5 | S2 | S4 |
| J-3 | 設計:分享卡視覺(連結 / 圖片 / 地圖路線 三格式) | task | D1 | S4 |
| J-4 | Android:Android Sharesheet 整合 | 3 | S3 | S4 |

### Epic K — 費用預估 (UC14 / FR-15)

> UC14 依需求文件為 UC4 的 `<<include>>`,實作上和 Trip Service 緊耦合。

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| K-1 | 後端:`estimateCost`(交通用 PTX 估;門票 / 餐飲先用景點 metadata 平均) | 5 | S2 | S4 |
| K-2 | 設計:費用拆解卡 UI(交通 / 門票 / 餐飲 三段) | task | D1 | S4 |
| K-3 | Android:行程確認頁加費用區塊 | 2 | S3 | S4 |

### Epic L — Admin CMS(維運)(UC16 / UC17 / UC18 / FR-18)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| L-1 | Admin 框架:Vite + React + TypeScript + react-admin / Refine 起骨架 | 5 | S5 | S5 |
| L-2 | 後台:景點 / 故事編輯頁(UC16)+ TipTap 富文本 | 5 | S5 | S5 |
| L-3 | 後台:跨景點脈絡關聯編輯(UC17)+ 視覺化關聯圖 | 5 | S5 | S5 |
| L-4 | 後台:翻譯審核佇列(UC18)+ 通過 / 退回 + diff 對照 | 5 | S5 | S5 |
| L-5 | 後台:UGC tag 審核(承接 H-2) | 3 | S5 | S5 |
| L-6 | 後端:Auth + Role(admin / guide)JWT + 權限 guard | 5 | S1 | S5 |
| L-7 | 設計:Admin CMS 視覺(以 react-admin 預設為底改 brand) | task | D2 | S5 |

### Epic M — 平台 / 部署 / 觀測性(跨 sprint)

| ID | Story | 估點 | Owner | Sprint |
|---|---|---|---|---|
| M-1 | Repo 骨架 + docker-compose(PG/Redis)+ Flyway V1 init | 5 | S1 | S0 |
| M-2 | OpenAPI 自動生成 + Swagger UI + Postman collection | 3 | S1 | S0–S1 |
| M-3 | GitHub Actions:backend / android / admin / ai-services CI | 5 | S6(或 S1) | S0–S2 |
| M-4 | Dockerfile × 4 服務(multi-stage)+ Docker Hub push | 5 | S6 | S4–S5 |
| M-5 | K8s Kustomize `dev` overlay + 部署到自架 k3s | 8 | S6 | S5 |
| M-6 | 觀測性 stack(Prometheus + Grafana + Loki + GlitchTip)Helm 安裝 | 5 | S6 | S5 |
| M-7 | 離線 demo 包(Ollama 本地版 + 預載資料) | 3 | S4 + S6 | S6 |

---

## 6. Sprint Plans

### Sprint 0 — 環境與內容就緒(週 0,半週)

> 不是正式 sprint,但有明確 entry criteria;**結束前所有人可在本機 `./gradlew bootRun` 與 `pnpm dev`**。

**Sprint Goal**:第一行 commit 之前,工具鏈、API 契約雛形、內容種子、設計系統 baseline 都到位。

| Owner | 任務 |
|---|---|
| **D1** | 設計系統 / Design tokens / Figma library;Persona 與旅程地圖收斂 |
| **D2** | 用 Claude Pro / ChatGPT Plus 訂閱版**生成 8 個台南景點 × 故事 × 繁中+英文**(A-1 第一批) |
| **S1** | Repo 骨架(`backend/`, `android/`, `ai-services/`, `admin/`)+ Spring Boot skeleton + Flyway V1 init(M-1) |
| **S1+S6** | docker-compose.dev.yml 起 PG + Redis;OpenAPI + Swagger 跑通(M-2) |
| **S2** | `Spot` / `Story` / `Tag` schema 草案;DTO 結構與 Figma 對齊 |
| **S3** | Android skeleton(Compose + Koin + Retrofit)+ 連到 mock backend |
| **S4** | Gemini API key 申請 + hello world;Ollama 本地裝 Qwen 2.5 7B 與 Llama 3.1 8B 比對 |
| **S5** | Vite + React + TypeScript + react-admin scaffolding(L-1 雛形) |
| **S6** | GitHub Actions backend-ci.yml(lint + test)第一版(M-3) |

**Gate 0 驗收**:
- [ ] 設計組:Figma 第一版 wireframe 走過 UC3、UC2(供 Sprint 1 切版)
- [ ] 系統組:`curl http://localhost:8080/spots/1` 回傳一筆 seeded 內容
- [ ] 任意成員 clone repo 後 30 分鐘內可起服務

---

### Sprint 1 — Epic A + B(週 1)

**Sprint Goal**:旅客打開 App,選歷史 / 美食主題,看到台南景點清單,點開可看完整故事(中英)。

**選入 Stories**:A-2、A-3、A-4、A-5、B-1、B-2、B-3、B-4、A-1(D2 持續做到 16 個景點)

**容量配置**(以 5 人系統組為例):

| 成員 | 點數 | Tickets |
|---|---|---|
| D1 | task | A-4 + B-3 |
| D2 | task | A-1(內容種子完成第一批 16 個) |
| S1 | 8 | B-1 + B-2 |
| S2 | 8 | A-2 + A-3 |
| S3 | 10 | A-5 + B-4 |
| S4 | — | C-1 spike(為 Sprint 2 暖身) |
| S5 | — | L-1 持續推進(沒到 sprint 但先做) |

**Gate 1 Demo Script**:
1. 打開 App,首頁顯示「歷史 / 美食 / 建築 / 宗教」四個主題
2. 選「歷史 + 建築」,顯示赤崁樓、安平古堡、孔廟…等清單
3. 點赤崁樓 → 看到時代 / 宗教 / 功能 三段式故事 + 多語切換按鈕
4. 切英文 → 整頁文字翻譯(至少 16 個景點有英文版)

**Retro 觸發點**:若 D1 設計交付落後超過 2 天,Sprint 2 必須調整。

---

### Sprint 2 — Epic C + D(週 2)— **G1 完成**

**Sprint Goal**:旅客可在地圖上導航到下一景點;旅程結束開回顧頁看到分群圖。

**選入 Stories**:C-1、C-2、C-3、C-4、D-1、D-2、D-3、D-4、D-5

| 成員 | 點數 | Tickets |
|---|---|---|
| D1 | task | C-3 + D-4 |
| D2 | task | A-1 收尾 + 開始撰寫 Scenario Video 腳本 |
| S1 | 13 | D-1 + D-2 + D-3(Navigation + PTX 整合,本 sprint 最重) |
| S2 | 3 | C-2 |
| S3 | 13 | D-5 + C-4 |
| S4 | 5 | C-1 |
| S5 | — | L-2 起稿 |

**風險點**:
- D-3(PTX 整合)為 spike-heavy,**S1 若卡住超過 2 天,降級為「公車路線寫死 demo 用 5 條」**,真實串接延到 Sprint 6
- D-5 位置權限同意 flow 要嚴格符合 NFR-14,設計與工程要對齊

**Gate 2 Demo Script**:
1. 從 Sprint 1 的清單選赤崁樓 → 點「導航前往」
2. 地圖顯示步行路線(OSM + Leaflet),含預估時間
3. 假裝走完 → 切到「我的足跡」頁
4. 看到 6 筆 mock footprint 自動分群成「廟宇 / 商業 / 歷史」+ 環圈圖
5. **G1 全部 4 個 UC 在同一台真機跑通**

---

### Sprint 3 — Epic E + F + G(週 3)

**Sprint Goal**:旅客可選雙主題自動推薦跨時期路線,在地圖上拖曳排序,中英隨切。

**選入 Stories**:E-1、E-2、E-3、E-4、F-1、F-3、F-4、G-1、G-2、G-3、I-1(spike 提前)

| 成員 | 點數 | Tickets |
|---|---|---|
| D1 | task | E-3 + F-3 |
| D2 | task | G-2 + G-4(跨文化類比文案) |
| S1 | 11 | F-1(共擔)+ G-1 |
| S2 | 10 | E-1 + E-2 |
| S3 | 11 | F-4 + E-4 |
| S4 | 5 | I-1(LLM spike,為 Sprint 4 暖身) |
| S5 | — | L-2 推進 |
| S6 | — | M-3 / M-4 補強 CI |

**Mid-Sprint Sync(週三)強制議題**:
- F-4 拖曳互動的 UX 細節:設計與 Android 對齊「排序動畫流暢度標準」
- I-1 結果:Gemini 2.5 Flash 是否品質夠?或要切 2.5 Pro?(影響 Sprint 4 預算)

**Gate 3 Demo Script**:
1. 選「美食 + 建築」雙主題 → 系統推薦「台南古蹟 + 在地小吃」跨時期路線
2. 在地圖上拖曳順序,**不同 tag 用不同顏色路徑**
3. 切英文,整頁(含主題名 / 景點 / 故事)翻過去
4. 確認行程 → 進入 Sprint 4 將串接的足跡 / 敘事入口

---

### Sprint 4 — Epic H + I + J + K(週 4)— **G2 完成**

> 本 sprint 最重也最高風險:LLM 第一次上 runtime + 同時做四個 Epic。
> **若 5 人版人力吃緊,Epic K 可降級**(只顯示總額,不拆解三段)。

**Sprint Goal**:走完一日台南 → 開回顧頁 → 點「生成旅程故事」→ 拿到明信片敘事 → 分享連結出去。

**選入 Stories**:H-1、H-2、H-3、H-4、H-5、I-2、I-3、I-4、I-5、J-1、J-2、J-3、J-4、K-1、K-2、K-3、F-2

| 成員 | 點數 | Tickets |
|---|---|---|
| D1 | task | H-3 + I-4 + J-3 + K-2 |
| D2 | task | **使用者測試 round 1** + 跨文化文案收尾 |
| S1 | 11 | F-2(K)+ I-3 + 共擔 review |
| S2 | 13 | H-1 + H-2 + J-1 + J-2 + K-1(吃力,可拆給 S4) |
| S3 | 13 | H-4 + H-5 + I-5 + J-4 + K-3 |
| S4 | 11 | I-2(主軸)+ 補強 K-1 |
| S5 | — | L-2 + L-3 推進 |
| S6 | — | M-4 Dockerfile 完成 |

**並行流量警告**:
- S2 與 S3 各超載 ≈ 1.5x,**SM(S1)必須在週三 sync 砍 ticket**
- 砍法建議:K-1 簡化(平均值寫死)、J-2 出圖簡化(只出靜態 1 張不要動態合成)

**Gate 4 Demo Script**:
1. 走完一日台南三景點 → 用 tag 記錄(無打字)+ 心情關鍵字
2. 開「足跡地圖」(UC9 最小版)→ 看到分群 + 時間軸
3. 點「生成旅程故事」→ loading 5–10 秒 → 明信片預覽
4. 點分享 → 出現連結 + 圖片 + 地圖路線 三選項
5. 選地圖路線 → 透過 Android Sharesheet 傳出去
6. **G2 全部 7 個 UC + UC9 最小版在真機跑通**

---

### Sprint 5 — Epic L + 部署 + 觀測性(週 5)— **G3 完成**

**Sprint Goal**:後台可獨立操作景點 / 故事 / 翻譯審核;整套服務在 K8s `staging` 跑起來,Grafana 看得到 metrics。

**選入 Stories**:L-1 收尾、L-2、L-3、L-4、L-5、L-6、L-7、M-4、M-5、M-6、C-5(若有空升級分類)

| 成員 | 點數 | Tickets |
|---|---|---|
| D1 | task | App 視覺 polish:轉場 / 空狀態 / loading skeleton / 首屏圖像 |
| D2 | task | Accessibility 巡檢(NFR-09 / NFR-10)+ Scenario Video 拍攝 |
| S1 | 5 | L-6(Auth + Role) |
| S2 | — | bug fix + 補測試 |
| S3 | — | App 內 polish issue + 對接 admin 改的內容 |
| S4 | 8 | C-5(LLM embedding 升級分類,選做)+ I 系列調 prompt |
| S5 | 18 | L-2、L-3、L-4、L-5(本 sprint 主軸) |
| S6 | 18 | M-4、M-5、M-6(K8s + 觀測性) |

**Gate 5 Demo Script**:
1. Admin CMS 登入(JWT)→ 新增「神農街」景點 + 三語故事
2. 點「送審」→ 翻譯審核佇列出現 → 通過
3. **App 立刻看得到**(NFR-16 解耦驗證)
4. 切到 Grafana → 看到剛剛兩個 API 請求的 latency 與 200 status
5. **G3 全部 3 個 UC 完成,可獨立跑**

---

### Sprint 6 — Polish + 整合 + Final Demo(週 6)— **緩衝週**

**Sprint Goal**:期末 demo 一鏡到底跑完;備用離線版可跑;簡報 / 海報 / Scenario Video 全部上線。

**選入 Stories**:M-7(離線包)、bug fix、UI polish、Scenario Video 收尾、Demo 排練

| 成員 | 任務 |
|---|---|
| D1 | 期末簡報視覺 + Demo storyboard + 海報 |
| D2 | 使用者測試 round 2 + 修最後 5–10 個 UX 議題 + Scenario Video 剪接 |
| S1 | E2E 測試:UC2 → UC3 → UC4 → UC6 → UC8 → UC10 → UC11 → UC12 一鏡到底 |
| S2 | bug fix 衝刺;效能調校(NFR-03 首屏 ≤ 2s 抽查) |
| S3 | App 最後一輪 polish + Play Store internal track 上架(可選) |
| S4 | M-7 離線 demo 包(Ollama + 預載 8 條路徑 / 16 景點) |
| S5 | Admin 補洞 + UGC tag 審核 demo |
| S6 | `prod` overlay 部署一輪;DB 備份腳本驗證 |

**Final Gate**:
- [ ] 期末 demo 走完整流程不卡
- [ ] 備用離線版可跑(現場萬一斷網)
- [ ] Scenario Video 上線
- [ ] README / 部署文件 / .env.example 收齊

---

## 7. 同步與整合機制

### 7.1 設計組 ↔ 系統組 對接協議

| 接點 | 規則 |
|---|---|
| **Figma → 系統組** 切版交接 | 設計組永遠領先 0.5–1 個 sprint;**畫完才丟,不要邊畫邊改** |
| **API 契約 (OpenAPI)** | 由 S1 定義,設計組可在 Figma 旁註標 endpoint;**改規格要兩組同意** |
| **Mock data** | 雙方共用一份 `dev` 環境的 seed,前端不自己生 mock |
| **設計變更窗口** | Sprint 開始後第 3 天 (週三) 之後,**只接 P0 bug,不接 enhancement** |

### 7.2 站會三句話模板

> 每人不超過 90 秒。

```
昨天我做了:[story id] [動詞] [產出]
今天我要做:[story id] [動詞] [產出]
我被卡住:[阻塞 / 沒有 → "沒有"]
```

阻塞當場記在看板「Blocked」欄,SM 站會後 1 小時內解。

### 7.3 看板狀態定義

```
Backlog → To Do → In Progress → In Review → Done
                               ↑
                            (PR 開啟)
```

- **In Progress 上限**:每人同時 ≤ 2 張 ticket(WIP limit)
- **In Review** 超過 2 天未 merge,SM 主動推進

### 7.4 Code Review 規則

- 至少 1 人 approve(後端/Android/Admin 各自找對口 reviewer)
- PR ≤ 400 行(超過就拆)
- CI 沒綠不 merge(GitHub Actions checks 必過)

---

## 8. 風險登記簿 (Risk Register)

| ID | 風險 | 機率 | 影響 | 緩解 |
|---|---|---|---|---|
| R1 | Gemini API 免費額度撐不住 demo 反覆測試 | M | H | 開發期用 Ollama 本地;demo 才打 Gemini;Narrative 結果快取 Redis |
| R2 | OSM 對台南覆蓋不完整 / 路徑奇怪 | M | M | 預先把 demo 8 條路徑手動驗證;真不行 fallback Google Maps free tier |
| R3 | 三語翻譯校對來不及(NFR-02) | H | M | 先英文,簡中可在 Sprint 6 補;不要為三語延 demo |
| R4 | UC10 規則式分類品質差 | L | M | 先規則式 demo,Sprint 5/6 升級 LLM embedding(C-5) |
| R5 | 設計組 vs 系統組節奏脫鉤 | H | H | 每週五 demo gate 強制整合;**整合不過下週不開新工** |
| R6 | F-4 拖曳互動技術 spike 失敗 | M | M | fallback:長按 + 上下移動箭頭(非拖曳)demo |
| R7 | S6 缺席(4 人版)導致 K8s 部署延後 | M | H | 4 人版改用 docker-compose 在單台 VM 上 demo,放棄 K8s |
| R8 | Sprint 4 同時 4 Epic 過載 | H | H | SM 週三必須砍 ticket;K-1 / J-2 為砍刀候選 |
| R9 | Android 模擬器在 Demo 時掛 | L | H | 帶 2 台實體 Android(API 30+)備援 |
| R10 | LLM prompt 在不同景點品質差 | M | M | I-1 spike 必須測 ≥ 5 個景點,prompt 模板要 generalize |

---

## 9. 度量 (Metrics)

每個 Sprint Review 後更新:

| 指標 | 目標 | 怎麼量 |
|---|---|---|
| Velocity(完成點數 / sprint) | 目標趨勢穩定 | GitHub Projects 自動計 |
| Sprint Goal 達成率 | ≥ 80%(6 sprint 中 5 個達標) | Demo Gate 主觀 + 客觀混判 |
| 阻塞解決時間 | < 24 小時 | 看板 Blocked 欄停留時間 |
| PR Review 時間 | < 24 小時(工作日) | GitHub PR 統計 |
| Bug 數量(Demo Gate 後新增) | 趨勢遞減 | GitHub Issues `bug` label |
| 測試覆蓋率(Service 層) | ≥ 80% | Jacoco 報告 |

---

## 10. 工具

| 工具 | 用途 | 備註 |
|---|---|---|
| **GitHub Projects** | Backlog + 看板 + Sprint planning | 免費 |
| **GitHub Actions** | CI/CD | 開源專案無上限 |
| **Figma** | 設計協作 | 免費版即可 |
| **Discord / Telegram** | 即時溝通 + Grafana alert webhook | 免費 |
| **Google Meet** | 站會與 Sprint 儀式 | 免費 |
| **Notion / GitHub Wiki** | 會議記錄 + Retrospective 紀錄 | 免費 |
| **Postman / Bruno** | API 共用 collection | 免費 |

> 所有工具都符合 CLAUDE.md §1.1「免費 / 自架 / 開源優先」原則。

---

## 11. 壓縮版 — 4 週可砍方案

若期程從 6 週壓到 4 週(預備週不算),以下範圍砍掉:

| 砍項 | 影響 |
|---|---|
| Epic L 整個降為「**只用 Swagger UI 給後台同學人工編輯**」 | 失去 Admin 視覺,但內容仍可改 |
| K-1 簡化為總額顯示 | 失去交通 / 門票 / 餐飲 三段拆解 |
| J-2 簡化為靜態圖卡 | 失去動態合成的精緻分享圖 |
| C-5 不做 | UC10 永遠是規則式分類 |
| M-5 / M-6 不做 K8s,改 docker-compose 單機 demo | 失去觀測性 stack |
| Sprint 6 緩衝週砍掉 | 沒有修 bug 的時間,風險高 |

對應 Sprint 重排:

```
Sprint 1 = 原 Sprint 1 + Sprint 2(雙倍人力做 G1)
Sprint 2 = 原 Sprint 3 (G2 上半)
Sprint 3 = 原 Sprint 4 (G2 下半,LLM)
Sprint 4 = Final Demo + 修 bug,G3 維持 Swagger UI
```

**強烈建議**:能跑 6 週就跑,壓 4 週只在「無可避免」時用。

---

## 12. Retrospective 模板

每個 Sprint Review 後 30 分鐘:

```
🟢 Start(下個 sprint 想開始做)
🔴 Stop(下個 sprint 想停止做)
🟡 Continue(下個 sprint 想繼續做)
```

每人輪流提 1–2 條,SM 紀錄到 Notion。**下個 Sprint Planning 開頭必引用**,確保 retro 不只是發洩。

---

## 13. 變更紀錄

| 日期 | 修訂 | 作者 |
|---|---|---|
| 2026-04-27 | 初版,六個 Sprint 安排 | Claude + 專案組 |

---

> **本文件為活的 backlog,每個 Sprint Retrospective 後更新。**
> 章節 §5(Backlog)與 §6(Sprint Plans)會隨進度刷新;§2 / §7 / §8(框架與機制)穩定後不輕易動。
