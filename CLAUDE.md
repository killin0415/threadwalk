# 開發準則 (Prototype — 改名為 CLAUDE.md 後生效)

> 本文件為 Claude Code / 開發者共用的專案指南。請在你檢視並調整後改名為 `CLAUDE.md`。
> 內容會在 Claude Code 啟動時自動讀入,所有 AI 協作行為都會依此文件對齊。

---

## 1. 專案概覽

- **專案名稱**:外國旅客在台灣初期探索文化的體驗設計(MCIS Project)
- **專案性質**:**期末專案 / 學術用途** — 非營運產品
- **核心目標**:重新設計「文化被理解的方式」— 解決資訊存在卻無法被連結與理解的問題。
- **MVP 範圍**:**先以台南為單一城市試點**(NFR-18),驗證後再擴張全台。
- **參考文件**(務必先讀):
  - `references/requirements.md` — 完整需求規格(UC1–UC18, FR-01~FR-23, NFR-01~NFR-20)
  - `references/diagram.md` — Activity / Domain Class / System Sequence Diagram
  - `references/architecture.md` — 系統架構圖 + service 用途速查

### 1.1 成本原則(最高層級規則)

> **本專案為期末作業,所有服務以「免費 / 自架 / 開源」為第一原則。** 任何引入付費服務的決定都要先在 PR / 對話中明確說明理由,並評估是否有開源替代。

- 優先順序:**自架開源 > 免費 SaaS 額度 > 付費 SaaS**
- 例外:Apple Developer Program($99/年)— 但本專案先不上 iOS,可避開
- LLM 策略:**Gemini API 免費 tier(runtime)+ 訂閱版網頁(build-time 內容種子)+ Ollama(fallback)**;OpenAI / Anthropic API 期末階段不啟用

---

## 2. 技術棧 (Tech Stack)

### Backend
- **主要框架**:Kotlin + Spring Boot
- **建置工具**:Gradle(Kotlin DSL,`build.gradle.kts`)
- **JDK 版本**:JDK 21 LTS
- **資料庫**:PostgreSQL(主)、Redis(快取)
- **ORM**:Spring Data JPA / JOOQ(視場景)
- **DB Migration**:**Flyway**(`backend/src/main/resources/db/migration/V*.sql`)
- **API 文件**:**SpringDoc OpenAPI**(自動生成 OpenAPI 3 spec + Swagger UI)

### AI / Data Services
- **語言**:Python 3.11+
- **環境管理**:**一律使用 `uv`**,禁止使用 `pip` / `python -m venv`
  - 建立環境:`uv venv`
  - 安裝依賴:`uv pip install -r requirements.txt` 或 `uv add <pkg>`
  - 執行:`uv run python script.py` / `uv run pytest`
- **用途**:LLM 整合(敘事生成、跨文化類比)、分類引擎、資料處理

### Frontend
- **行動端**:**原生 Android(Kotlin)** 或 **Kotlin Multiplatform (KMP)** — 兩者擇一
  - 若僅優先 Android:純 Kotlin + Jetpack Compose
  - 若需同時涵蓋 iOS:Kotlin Multiplatform + Compose Multiplatform(共用 domain / data 層,UI 視情況共用或各平台原生)
  - 與後端共用語言與 model 是優勢之一,可考慮抽出 `shared` module 共享 DTO / domain 模型
- **Kiosk**:瀏覽器 SPA(Next.js / Vite)
- **Admin CMS**:**自建 React 後台**
  - 框架:Vite + React + TypeScript
  - UI library:**react-admin** 或 **Refine**(資料層/列表/表單/RBAC 都有現成 hooks),不從零刻
  - 串接後端:走 Spring Boot 的 REST API(同一份 API 給 Mobile / Kiosk / Admin 共用)
  - 富文本:TipTap 或 Lexical(撰寫景點故事用)
  - 狀態管理:TanStack Query(server state)+ Zustand(必要的 client state)

### External Services(免費 / 開源優先)

| 用途 | 免費 / 自架選項(優先) | 付費備援 | 註 |
|---|---|---|---|
| **LLM — Runtime API**(narrative service 即時呼叫) | **Gemini API 免費 tier**(2.5 Flash:15 RPM / 1500 RPD;2.5 Pro:5 RPM / 25 RPD) | Anthropic / OpenAI API(**期末階段不啟用,避免帳單**) | demo 流量綽綽有餘 |
| **LLM — Build-time 內容種子**(景點故事、脈絡、翻譯校對) | **Claude Pro / ChatGPT Plus / Gemini Advanced 訂閱版**(網頁版,人工操作) | — | 一次性產出,匯入 Content DB,App runtime 不需呼叫 |
| **LLM — Fallback / 離線** | **Ollama** + Qwen 2.5 7B / Llama 3.1 8B(本地跑) | — | 若 Gemini 免費額度撐不住、或要在 demo 機離線跑 |
| **地圖 / 路徑** | **OpenStreetMap + Leaflet / MapLibre**(完全免費)+ 台灣 **PTX / TDX**(免費 API) | Google Maps Platform(月 $200 免費額度) | OSM 對台灣覆蓋不錯;MapLibre 是 Mapbox GL 的開源 fork |
| **翻譯**(NFR-02) | **LibreTranslate**(開源自架)/ **DeepL Free**(50 萬字元/月) | Google Translate API | 文化詞彙仍須訂閱版 LLM 人工校對(NFR-02) |
| **推播(Android)** | **FCM**(完全免費,無上限) | — | 若改用 KMP + iOS 才需要 APNs |
| **物件儲存** | **MinIO**(自架,S3 相容) | AWS S3 / Cloudflare R2 | MinIO 跑在 K8s 內,無外部依賴 |
| **郵件(若需要)** | **MailHog**(本地開發)/ Resend / SendGrid 免費版(3K/月) | — | MVP 階段大概不需要 transactional email |

### LLM 分工原則(重要)

> **訂閱 ≠ API**:ChatGPT Plus / Claude Pro / Gemini Advanced 是網頁版訂閱,不能直接被後端呼叫。

| 階段 | 用什麼 | 做什麼 |
|---|---|---|
| **Build-time(一次性,內容種子)** | Claude Pro / ChatGPT Plus / Gemini Advanced **訂閱版網頁** | 人工協作生成景點故事、跨景點脈絡、翻譯校對 → 匯入 Content DB |
| **Runtime(系統執行時)** | **Gemini API 免費 tier**(預設) | narrative-service 即時生成旅程敘事(FR-13)、跨文化類比(NFR-02) |
| **Fallback** | **Ollama 本地** | 免費額度用完、或 demo 機要離線時 |
| **禁用** | OpenAI API / Anthropic API | 期末階段不啟用,**除非使用者明確同意付費** |

設計含義:
- 80% 內容(景點故事、脈絡、預設路線)是 **build-time 預生成**,App runtime 直接讀資料庫
- 只有「**個人化旅程敘事**」(FR-13,依旅客足跡 + 文化脈絡 + 個人標註)需要 runtime LLM 呼叫
- 這讓 Gemini 免費 tier 的 1500 RPD 完全夠期末 demo 用

### CI / CD & 部署
- **CI/CD**:**GitHub Actions**(workflow 定義在 `.github/workflows/`)
- **容器化**:**Docker**(每個服務一個 `Dockerfile`,multi-stage build)
- **Image Registry**:**Docker Hub**(`docker.io/<org>/<service>`)
- **部署平台**:**Kubernetes**(自建為主、AKS 備援)
  - 部署清單:**Kustomize**(自家服務,plain YAML + overlay)
  - 第三方元件(ingress-nginx、cert-manager、Prometheus 等):用 Helm 安裝(現成 chart 不重造)
  - 環境分離:`dev` / `staging` / `prod` namespace
- **基本流程**:
  1. PR 開啟 → 跑 lint + test + build + Trivy/SAST 掃描
  2. PR merge 到 `main` → 自動 build image + push Docker Hub + `kustomize build overlays/staging | kubectl apply -f -`
  3. 手動 approve(GitHub Environments)→ 部署到 `prod`

---

## 3. 專案結構(預期)

```
mcis_project/
├── backend/                    # Kotlin Spring Boot 主後端
│   ├── src/main/kotlin/
│   │   └── com/mcis/
│   │       ├── auth/           # Auth Service
│   │       ├── user/           # User & Preference
│   │       ├── content/        # 景點/故事/脈絡
│   │       ├── route/          # Route Planning
│   │       ├── trip/           # Trip & Itinerary
│   │       ├── nav/            # Navigation
│   │       ├── footprint/      # 足跡 / UGC
│   │       ├── share/          # 分享
│   │       ├── i18n/           # 多語
│   │       └── common/         # 共用 (config, error, util)
│   ├── src/main/resources/
│   │   ├── application.yml
│   │   ├── application-{dev,staging,prod}.yml
│   │   └── db/migration/        # Flyway migration scripts (V1__init.sql, ...)
│   └── src/test/kotlin/
├── ai-services/                # Python LLM / 分類 / 敘事
│   ├── narrative/              # FR-13 敘事生成
│   ├── classification/         # FR-11 自動分群
│   ├── i18n_assist/            # NFR-02 跨文化類比
│   └── pyproject.toml
├── android/                    # 原生 Android(Kotlin + Jetpack Compose)
│   └── app/src/main/kotlin/com/mcis/android/
│       ├── ui/                 # Compose screens / components
│       ├── data/               # repository / api client
│       ├── domain/             # use case / model
│       └── di/                 # Koin module (appModule, dataModule, ...)
│   # 若改採 Kotlin Multiplatform,改為以下結構:
│   # mobile/
│   # ├── shared/               # KMP 共用層 (domain / data / network)
│   # │   └── src/commonMain/kotlin/
│   # ├── androidApp/           # Android 入口 (Jetpack Compose)
│   # └── iosApp/               # iOS 入口 (Compose Multiplatform 或 SwiftUI)
├── kiosk/                      # Kiosk Web
├── admin/                      # Admin CMS (Vite + React + TypeScript)
│   └── src/
│       ├── pages/              # 路由頁面 (景點 / 故事 / 翻譯審核 / UGC)
│       ├── features/           # 領域模組 (spot, story, context, ugc-tag, translation)
│       ├── components/         # 共用 UI 元件
│       ├── api/                # API client (對接 Spring Boot)
│       ├── hooks/              # TanStack Query hooks
│       └── stores/             # Zustand stores
├── references/                 # 需求/設計文件(已存在)
├── deploy/                     # K8s 部署清單(Kustomize)
│   ├── base/                   # 各服務 base manifests
│   │   ├── backend/
│   │   ├── ai-services/
│   │   └── admin/
│   └── overlays/               # 環境差異 patch
│       ├── dev/
│       ├── staging/
│       └── prod/
├── docker/                     # 共用 Dockerfile / compose
│   └── docker-compose.dev.yml  # 本地開發起 PG + Redis
├── .github/
│   └── workflows/              # GitHub Actions
│       ├── backend-ci.yml
│       ├── ai-services-ci.yml
│       ├── admin-ci.yml
│       ├── android-ci.yml
│       └── deploy.yml          # build image → push → 部署
└── CLAUDE.md
```

> 尚未建立的資料夾,請在第一次需要時補上,並於本文件對應段落補充說明。

---

## 4. 編碼規範

### 4.1 Kotlin / Spring Boot

- **命名**:Class `PascalCase`、function/property `camelCase`、constant `SCREAMING_SNAKE_CASE`、package 全小寫
- **層次劃分**:`Controller → Service → Repository`,業務邏輯一律放 Service,Controller 只做請求轉接與驗證
- **DTO / Entity 分離**:對外暴露 DTO,絕不直接回傳 JPA Entity
- **錯誤處理**:統一 `@RestControllerAdvice` + 自訂 `ApiException`,不要在 Controller 散落 try-catch
- **NULL 安全**:善用 Kotlin 的 nullable type,避免 `!!`(只在你能保證非 null 才用)
- **Immutable 優先**:`val` over `var`、`List` over `MutableList`
- **依賴注入**:**用 constructor injection,不要用 `@Autowired field**
- **設定外部化**:所有可變設定(API key、URL、limits)放 `application.yml` 或環境變數,不寫死
- **Logging**:用 SLF4J(`KotlinLogging` 或 `LoggerFactory`),禁止 `println`
- **DB Migration**:**所有 schema 變更走 Flyway**,**禁止手動下 SQL 改 prod schema**
  - 檔名格式:`V<版本>__<簡述>.sql`,例:`V3__add_preferred_language_to_users.sql`
  - 版本號**只增不改**;已合併到 main 的 migration 不可再修改 — 要修就寫新一支
  - Spring Boot 啟動時 Flyway 會自動偵測 + 執行,但**正式環境改用獨立 K8s Job 執行**(避免多 pod 並發跑)
- **API 文件**:Controller 加 `@Operation` / `@Parameter` 註解 → SpringDoc 自動生成
  - 開發時 Swagger UI 在 `http://localhost:8080/swagger-ui.html`
  - 生成的 OpenAPI spec(`/v3/api-docs`)可給 React Admin 端 codegen TS client(維持後端與 admin 端 DTO 同步)

### 4.2 Android / Kotlin Multiplatform

- **UI 框架**:Jetpack Compose(原生)/ Compose Multiplatform(KMP)
- **架構**:MVVM 或 MVI;單一 source of truth(Repository)→ ViewModel → Composable
- **依賴注入**:**Koin**(原生與 KMP 統一使用,避免後續切換成本)
- **非同步**:Kotlin Coroutines + Flow,**禁止 RxJava**(新專案無理由引入)
- **網路**:Retrofit + OkHttp(原生)/ Ktor Client(KMP);序列化用 `kotlinx.serialization`
- **本地儲存**:DataStore(偏好)/ Room(結構化資料)— 對齊 NFR-12 離線優先
- **地圖**:Google Maps SDK for Android(原生模組);若 KMP 則 iOS 端需另接 MapKit
- **多語**:Android 原生 `strings.xml` 多 locale 資源;KMP 則用 `moko-resources` 或自製
- **Module 結構**:**KMP 階段**先抽 `shared` module 包 domain/data,UI 仍可各平台獨立
- **與後端共用**:盡可能共用 DTO 定義(可放後端的 `api-spec` module 後 publish 給 Android,或用 OpenAPI codegen)
- **CLI-first 開發**:本專案要求所有 build / install / test / 模擬器操作都能在終端機完成,**不依賴 Android Studio GUI**。詳見 §10 Android 命令速查

### 4.3 Admin CMS (React + TypeScript)

- **語言**:TypeScript(嚴格模式 `strict: true`),禁止 `any`(無法避免時用 `unknown` 並收斂)
- **建置**:Vite + React 18+
- **目錄組織**:**feature-based**(`features/spot/`、`features/story/` 各自包 components / hooks / api),不是 type-based(避免 `components/` 變成大雜燴)
- **資料層**:**TanStack Query 為主**,server state 不放 Zustand;mutations 後手動 invalidate query
- **Client state**:Zustand 只裝跨頁面、非同步無關的 UI state(語系、開合狀態等)
- **表單**:React Hook Form + Zod schema,**Zod schema 與後端 DTO 同步**(理想:OpenAPI codegen 一份)
- **路由**:React Router v6+ 或 TanStack Router;權限 guard 寫成 wrapper component
- **UI 元件**:react-admin / Refine 提供的元件優先;客製需求才自寫
- **樣式**:Tailwind CSS;避免 inline style 與 styled-components(統一視覺與 Lint)
- **權限**:後端發 JWT,前端解析 role(admin / guide)決定可見頁面與按鈕
- **錯誤呈現**:統一的 toast / error boundary,不在每個頁面散落 try-catch

### 4.4 Python

- **格式**:`ruff format`(取代 black),lint 用 `ruff check`
- **型別**:**所有 public function 加 type hints**,以 `mypy` 或 `pyright` 檢查
- **字串**:f-string 優先
- **路徑**:`pathlib.Path` 優先,禁止 `os.path.join` 字串拼接
- **依賴**:用 `uv add` 加入,寫進 `pyproject.toml`;不要 `pip install` 後忘了同步
- **執行入口**:`if __name__ == "__main__":` 包起來,或用 typer / click 做 CLI

### 4.5 共通

- **語言**:**程式碼識別子用英文**;commit / PR 描述可中英混用,優先寫使用者讀得懂的中文
- **註解**:預設**不寫註解**。只有在「為什麼這樣寫」非顯而易見時才寫(workaround、特殊不變式、會讓讀者困惑的行為)
- **不要寫**:
  - 解釋程式碼做什麼的註解(命名要好到不需註解)
  - 引用當前任務 / 議題編號的註解
  - 預期未來需求的抽象 / 參數
- **檔案放置**:優先**修改既有檔案**,不必要不新增檔案;不主動建立 `*.md` 文件

---

## 5. 測試

### Kotlin (Backend)
- **單元測試**:JUnit 5 + Kotest assertion + MockK
- **整合測試**:Spring Boot Test + Testcontainers(`PostgreSQLContainer`、`GenericContainer` for Redis)
- **規則**:**整合測試用真實 DB / Redis,不要 mock**(避免 mock/prod 落差)
- **覆蓋率**:Service 層 ≥ 80%,Controller 層走整合測試

### Android / KMP
- **單元測試**:JUnit 5 + MockK + Turbine(測 Flow);KMP 共用層用 `kotlin.test`
- **UI 測試**:Compose UI Test(`createComposeRule`);關鍵畫面寫 screenshot test 防 regression
- **規則**:ViewModel 層必測,Composable 走 UI test;不對 framework 寫測試(`Activity` 內部行為信任系統)

### Python
- **框架**:`pytest` + `pytest-asyncio`(若用 async)
- **執行**:`uv run pytest`
- **LLM 呼叫**:單元測試 mock LLM,整合測試走 sandbox / 低成本模型

---

## 6. Git / Commit

- **分支策略**:
  - `main` — 永遠可部署
  - `feature/<scope>-<short-desc>` — 功能分支
  - `fix/<short-desc>` — Bug fix
- **Commit message**:
  - 中文或英文皆可,**重點是「為什麼」而非「做了什麼」**
  - 一行 summary(<= 72 字),空一行,再寫詳述
  - 範例:`feat(route): 加入跨時期路線推薦` / `fix(nav): 修正山路換車計算`
- **PR 規則**:
  - 標題簡短(<70 字)
  - body 包含:Summary(why)、Changes(what)、Test plan
- **提交前**:跑過 lint + 單元測試
  - Kotlin 端:`./gradlew check`
  - Python 端:`uv run ruff check && uv run pytest`
  - React Admin 端:`pnpm lint && pnpm test`
- **PR 必過 GitHub Actions checks**(無法 merge 直到綠燈):lint、test、build、image scan

---

## 7. CI / CD & 部署

### 7.1 CI(GitHub Actions)

每個 service 一支 workflow,觸發條件用 `paths` 過濾(只有相關目錄變更才跑):

| Workflow | Path 過濾 | 主要步驟 |
|---|---|---|
| `backend-ci.yml` | `backend/**` | `./gradlew check` + Testcontainers 整合測試 |
| `ai-services-ci.yml` | `ai-services/**` | `uv sync` + `uv run ruff check` + `uv run pytest` |
| `admin-ci.yml` | `admin/**` | `pnpm lint && pnpm test && pnpm build` |
| `android-ci.yml` | `android/**` | `./gradlew test lint`(connectedAndroidTest 視 runner 能力) |
| `deploy.yml` | merge 到 `main` 後觸發 | build image → push ghcr.io → 部署 |

**通用慣例**:
- 用 `actions/cache` 快取 Gradle / pnpm / uv 依賴
- Secrets 一律走 GitHub Secrets,**不寫進 workflow 檔**
- PR 上開 `concurrency` 取消舊的 workflow run(節省 minutes)

### 7.2 容器化(Docker)

- 每個 service 自帶 `Dockerfile`,**multi-stage build**(builder + runtime),最終 image 只裝 runtime 必要物
- **基底映像**:
  - Backend(Spring Boot):`eclipse-temurin:21-jre-alpine` 或 `gcr.io/distroless/java21`
  - AI services(Python):`python:3.11-slim`
  - Admin(靜態檔):`nginx:alpine` 服務 build 出來的 `dist/`
- **規則**:
  - **不要在 image 裡放 secrets**(用 K8s Secret 或 envFrom)
  - 跑 non-root user
  - `HEALTHCHECK` 一定要有(K8s liveness/readiness probe 也對應)
  - 標 image tag:`docker.io/<org>/<service>:<git-sha>` + `docker.io/<org>/<service>:<branch>` + `:latest`(僅 main)
  - **Docker Hub 限速注意**:匿名拉取每 IP / 6h 100 次,K8s nodes 透過 `imagePullSecrets` 用付費或登入帳號拉取避免被打回 429

### 7.3 部署(Kubernetes)

- **託管策略**:**自建為主**(裸機 / VM 上跑 k3s 或 kubeadm),**AKS(Azure Kubernetes Service)為備援選項**
  - 自建主因:成本、資料主權、實驗彈性
  - AKS 適用情境:正式上線時若需要託管 control plane、整合 Azure AD / Azure Monitor / Azure Files
  - **manifests 必須雲端中立**:不寫死 AWS/Azure 專屬 annotation,要用 cloud-specific 功能就用 overlay/values 隔離
- **環境**:`dev` / `staging` / `prod` 各自 namespace(同集群即可,prod 上線後再考慮分群)
- **部署清單**:**Kustomize**(自家服務專用),結構為 `deploy/base/<service>/` + `deploy/overlays/<env>/`
  - 部署指令:`kubectl apply -k deploy/overlays/<env>`
  - **第三方元件用 Helm 安裝**(ingress-nginx、cert-manager、Prometheus、Grafana、CloudNativePG):有現成 chart 不重造輪子
  - 兩者並存的判斷:**自家服務 Kustomize、外來服務 Helm**(看 `helm list -n <ns>` 知道有哪些第三方)
- **核心物件**:`Deployment` + `Service` + `Ingress` + `ConfigMap` + `Secret` + `HPA`(Horizontal Pod Autoscaler)
- **必設**:
  - `resources.requests` / `resources.limits` — 不裸跑
  - `livenessProbe` / `readinessProbe` — 對應 NFR-11(99.5% 可用度)
  - `PodDisruptionBudget` — 升版時不全掛
- **Ingress / TLS**:
  - 自建:ingress-nginx + cert-manager(Let's Encrypt)
  - AKS:Application Gateway Ingress Controller(AGIC)或同樣裝 ingress-nginx
- **Storage / DB**:
  - 自建:PostgreSQL / Redis 用 operator(CloudNativePG、Redis Operator)或外部 VM
  - AKS:可改用 Azure Database for PostgreSQL Flexible Server + Azure Cache for Redis(用 managed,不要自架)
- **Secrets 管理**(分階段):
  - **前期開發階段**:本地 `.env` 檔(每個 service 自帶 `.env.example` 提交,真實 `.env` 一律 `.gitignore`)
  - **K8s 上線後**:**K8s Secret**(透過 GitHub Secrets → CI 注入 → `kubectl create secret` / Kustomize `secretGenerator`)
  - **未來規模再升級**:sealed-secrets / External Secrets Operator + Azure Key Vault(若搬到 AKS)— 不在 MVP 範圍
- **資料庫 migration**:CI 透過 Flyway/Liquibase Job 跑,**不要寫進應用啟動流程**(避免並發跑)
- **流量切換**:rolling update 預設;敘事生成等高風險改動可用 blue-green 或 canary

### 7.4 觀測性(全免費 / 自架)

> 對齊 §1.1 成本原則,以下整套都自架在 K8s 內,**零外部費用**。

| 職責 | 工具 | 部署方式 | 備註 |
|---|---|---|---|
| **Metrics** | **Prometheus** | `kube-prometheus-stack` Helm chart 一鍵 | 包含 Node Exporter、ServiceMonitor、現成 K8s dashboard |
| **視覺化 + Alerting** | **Grafana** | 跟著 Prometheus stack 一起裝 | Alert 走 Webhook → Discord / Telegram(免費) |
| **Logs** | **Loki + Promtail** | `loki-stack` Helm chart | 比 ELK 輕得多;只 index label,儲存便宜 |
| **Errors** | **GlitchTip**(Sentry 開源相容版,免費自架) | Helm chart | API 與 Sentry SDK 完全相容,可直接用 `@sentry/*` 套件 |
| **Tracing**(後期) | Tempo / Jaeger | 等 service 之間呼叫複雜再加 | MVP 不一定要 |
| **Uptime**(外部) | **UptimeRobot 免費版**(50 monitor)/ **Better Stack 免費版** | SaaS,從外部 ping ingress | K8s 內監控不到「整個 cluster 掛了」需要外部 |

### 7.4.1 應用層整合

- **Logs**:輸出**結構化 JSON**到 stdout(Logback JSON encoder / Python `python-json-logger`),K8s 自動由 Promtail 收集
- **Metrics**:
  - Spring Boot:加 `spring-boot-starter-actuator` + Micrometer Prometheus registry → 自動暴露 `/actuator/prometheus`
  - Python:`prometheus_client` 套件 → 暴露 `/metrics`
- **Errors**:用 Sentry SDK 但指向 GlitchTip 自架 endpoint(`SENTRY_DSN=https://...@glitchtip.mcis.local/1`)
- **Tracing**(若加):OpenTelemetry SDK,trace-id 隨 HTTP header 傳遞

### 7.4.2 Alert 通道(免費)

- 不用 PagerDuty / OpsGenie(都收費)
- Grafana Alerting → Webhook → **Discord / Telegram bot**(完全免費)
- 程度區分:warning 進 Discord、critical 才進 Telegram(個人推播)

---

## 8. 安全 / 隱私

對齊 NFR-13、NFR-14、NFR-15:

- **絕不**將 API key、credentials、`.env`、keystore commit 進 git(已在 `.gitignore` 排除,但仍要警覺)
- **位置資料**:預設不蒐集精準位置,需明確同意才啟用導航
- **足跡 / 筆記**:儲存須加密,符合 GDPR / 個資法
- **第三方分享**:匿名化或經使用者明確確認
- **上傳前必檢**:檢查是否含個資、token、secret

### 8.1 Secrets 管理流程(分階段)

**前期開發(現階段)**:
- 每個 service 自帶 `.env.example`(提交)+ `.env`(本地實際值,**必 `.gitignore`**)
- 載入方式:
  - Backend(Spring Boot):`spring-dotenv` 或啟動時 `--spring.config.import=optional:file:.env[.properties]`
  - Python:`python-dotenv` 或 `os.environ`
  - React Admin:Vite 原生 `import.meta.env.VITE_*`(只放 **public** 設定,API URL 之類)
- **變更 `.env.example` 時必同步通知團隊**(在 PR 描述提及)

**K8s 上線後**:
- 真實 secrets 走 `GitHub Secrets → CI → K8s Secret`
- Kustomize 用 `secretGenerator` 從 CI 注入,**不要把明文 Secret YAML 提交進 git**
  ```yaml
  # deploy/overlays/staging/kustomization.yaml
  secretGenerator:
    - name: backend-secrets
      envs:
        - secrets.env  # CI 在 build 階段寫入,.gitignore
  ```
- Pod 透過 `envFrom: secretRef:` 讀取,程式碼不需改 — `.env` 與 K8s Secret 的 key 命名要一致

**何時升級**:`sealed-secrets` 或 ExternalSecrets+Vault 等到「team 規模 ≥ 3 人 / 多人需要部署權限」時再導入,MVP 階段不做。

---

## 9. 與 AI 協作的原則(給 Claude Code 的偏好)

> 這段直接寫給 Claude — 以下行為請主動遵守。

### 9.1 範圍控制
- **不要過度設計**:Bug fix 就只 fix bug,不順便重構
- **不要為「未來可能」加抽象**:三段相似程式碼比過早抽象好
- **不要加不會發生情境的 error handling**:信任內部呼叫者與框架,只在系統邊界(使用者輸入、外部 API)驗證

### 9.2 文件與註解
- **不要主動建立 `*.md`**(除非使用者明確要求)
- **不要在程式碼中寫多段 docstring** — 一行內為原則
- 設計決策、規劃、分析放在對話 / PR 描述,不要落地成文件

### 9.3 風險動作
以下需先**徵求使用者同意**:
- `git push --force` / `git reset --hard` / 刪 branch
- 修改 CI / 部署設定
- 跑會發 mail / Slack / 對外 API 的指令
- 升降級套件主版本
- `--no-verify` 跳過 hook(預設禁止,除非使用者明確要求)

### 9.4 探索與實作
- 多步任務先用 TaskCreate 列計畫,完成一項就 mark
- 廣度探索(超過 3 個 grep)用 Agent (Explore);單檔已知路徑直接 Read
- **不要重複 subagent 已做的搜尋**

### 9.5 回應風格
- 短而精,用 bullet 而非長段落
- 引用程式碼用 `file_path:line_number` 格式
- 只在使用者要求時才使用 emoji
- 不在每次回應結尾貼「總結」(看 diff 即知)

---

## 10. 執行命令速查

### Backend (Kotlin)
```bash
# 開發模式啟動(會自動跑 Flyway migration)
./gradlew bootRun

# 編譯 + 測試 + lint
./gradlew check

# 只跑單元測試
./gradlew test

# 整合測試(含 Testcontainers)
./gradlew integrationTest

# 打包
./gradlew bootJar

# Flyway:檢查 migration 狀態(不執行)
./gradlew flywayInfo

# Flyway:手動執行 migration(部署 / CI)
./gradlew flywayMigrate

# Flyway:驗證 schema 與 migration 是否一致
./gradlew flywayValidate

# 取 OpenAPI spec(Swagger UI 用)— bootRun 起來後
curl http://localhost:8080/v3/api-docs > openapi.json
open http://localhost:8080/swagger-ui.html
```

### Android(原生 / KMP 共用)

> Android 官方 CLI 由四套工具組成,**不再需要開 Android Studio 即可完成 build / install / test 全流程**。
> 來源:[Android Developers — Command-line tools](https://developer.android.com/tools)(經 ctx7 查證)

#### Gradle Wrapper(專案內建,build / test 主入口)

```bash
# 從 android/ 目錄執行
./gradlew tasks                  # 列出所有可用 task
./gradlew assembleDebug          # 打包 debug APK
./gradlew assembleRelease        # 打包 release APK
./gradlew bundleRelease          # 打包 AAB(Play Store 上架格式)
./gradlew installDebug           # 安裝 debug APK 到連線裝置
./gradlew uninstallAll           # 解除安裝所有 variant
./gradlew test                   # JVM 單元測試
./gradlew connectedAndroidTest   # 連線裝置 / 模擬器跑 instrumentation test
./gradlew lint                   # Android Lint(靜態分析)
./gradlew :app:dependencies      # 看依賴樹(找版本衝突)
./gradlew --stop                 # 停掉 Gradle daemon(疑難排解)
```

#### adb (Platform-Tools — 裝置 / 模擬器互動)

```bash
adb devices -l                   # 列出已連線裝置 / 模擬器
adb install -r app-debug.apk     # 安裝 / 重裝 APK(-r 取代既有)
adb uninstall com.mcis.android   # 解除安裝
adb shell am start -W \
  -a android.intent.action.VIEW \
  -d "mcis://spot/123" \
  com.mcis.android              # 測 deep link(對應 FR-08 移動中故事卡 / FR-14 分享)
adb shell pm list packages       # 列裝置上安裝的 packages
adb logcat -s MCIS:V             # 只看自家 tag 的 log
adb shell input keyevent 4       # 模擬返回鍵
adb shell screencap /sdcard/s.png && adb pull /sdcard/s.png  # 截圖
```

#### sdkmanager (cmdline-tools — 管 SDK / 套件)

```bash
sdkmanager --list                 # 列出所有可裝套件
sdkmanager --list_installed       # 已安裝
sdkmanager "platform-tools" "platforms;android-35" "build-tools;35.0.0"
sdkmanager --install "system-images;android-35;google_apis;arm64-v8a"
sdkmanager --update               # 更新所有套件
sdkmanager --licenses             # 接受授權(CI 必跑)
```

#### avdmanager + emulator (建立 / 啟動模擬器)

```bash
# 建立模擬器(API 35 / Pixel 8 / Google APIs)
avdmanager create avd -n mcis_test \
  -k "system-images;android-35;google_apis;arm64-v8a" \
  -d "pixel_8"

avdmanager list avd               # 列已建立的 AVD
emulator -list-avds               # 同上,從 emulator 視角
emulator -avd mcis_test           # 啟動模擬器
emulator -avd mcis_test -no-window -gpu swiftshader_indirect  # CI 無頭模式
```

#### 其他常用

```bash
# 分析 APK 大小、權限、resource 使用
apkanalyzer apk summary app-release.apk
apkanalyzer dex packages app-release.apk

# Compose preview / hot reload 期間的 build 加速
./gradlew --configuration-cache --build-cache assembleDebug
```

#### 在 CI(GitHub Actions 等)的最小組合

```bash
# 1. 接受授權
yes | sdkmanager --licenses

# 2. 安裝必要套件
sdkmanager "platform-tools" "platforms;android-35" "build-tools;35.0.0" \
           "system-images;android-35;google_apis;x86_64"

# 3. 建立 AVD + 啟動(connectedAndroidTest 需要)
echo "no" | avdmanager create avd -n ci_avd \
  -k "system-images;android-35;google_apis;x86_64" --force
emulator -avd ci_avd -no-window -no-audio -gpu swiftshader_indirect &

# 4. 等待開機完成後跑測試
adb wait-for-device shell 'while [[ -z $(getprop sys.boot_completed) ]]; do sleep 1; done'
./gradlew test connectedAndroidTest
```

### Admin CMS (React)
```bash
# 從 admin/ 目錄執行
pnpm install                     # 安裝依賴(或 npm install)
pnpm dev                         # 開發模式
pnpm build                       # 打包正式版
pnpm preview                     # 預覽 build
pnpm lint                        # ESLint + TypeScript 檢查
pnpm test                        # Vitest
```

### AI Services (Python)
```bash
# 建立環境
uv venv && source .venv/bin/activate

# 安裝依賴
uv sync

# 跑某個 service
uv run python -m narrative.main

# 跑測試
uv run pytest

# Lint + format
uv run ruff check
uv run ruff format
```

### Docker(本地開發 / build image)
```bash
# 本地起依賴(PG + Redis)
docker compose -f docker/docker-compose.dev.yml up -d
docker compose -f docker/docker-compose.dev.yml down

# 建 image(以 backend 為例)
docker build -t docker.io/<org>/mcis-backend:dev -f backend/Dockerfile backend/

# 在本地跑單一 service
docker run --rm -p 8080:8080 \
  --env-file backend/.env.local \
  docker.io/<org>/mcis-backend:dev

# 推送 Docker Hub(CI 會自動跑;本地除錯用)
echo $DOCKERHUB_TOKEN | docker login -u <user> --password-stdin
docker push docker.io/<org>/mcis-backend:dev
```

### Kubernetes / Kustomize
```bash
# Context 切換(避免錯部署)
kubectl config get-contexts
kubectl config use-context mcis-dev

# 看 pods / logs
kubectl -n mcis get pods
kubectl -n mcis logs -f deployment/backend
kubectl -n mcis describe pod <pod-name>

# 進入 pod 除錯
kubectl -n mcis exec -it deployment/backend -- sh

# 看 K8s events(除錯部署失敗)
kubectl -n mcis get events --sort-by=.lastTimestamp

# Kustomize 部署(自家服務)
kustomize build deploy/overlays/dev | kubectl apply -f -
kubectl apply -k deploy/overlays/dev          # 同上,kubectl 內建版

# 預覽 render 結果(部署前必看)
kustomize build deploy/overlays/dev

# 動態改 image tag(CI 用)
cd deploy/overlays/dev
kustomize edit set image backend=docker.io/<org>/mcis-backend:$(git rev-parse --short HEAD)
kubectl apply -k .

# 回滾(Kustomize 沒有內建 rollback,用 kubectl)
kubectl -n mcis rollout undo deployment/backend
kubectl -n mcis rollout history deployment/backend

# Helm — 只用於第三方元件(ingress-nginx 等)
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm upgrade --install ingress-nginx ingress-nginx/ingress-nginx \
  -n ingress-nginx --create-namespace
helm list -A
```

### Docker Hub 拉取限制處理
```bash
# K8s 建立 imagePullSecret 避免 429
kubectl -n mcis create secret docker-registry dockerhub-cred \
  --docker-server=docker.io \
  --docker-username=<user> \
  --docker-password=$DOCKERHUB_TOKEN \
  --docker-email=<email>

# 在 Deployment manifest 引用:
# spec.template.spec.imagePullSecrets:
#   - name: dockerhub-cred
```

### Secrets 操作
```bash
# === 前期開發:本地 .env ===
cp backend/.env.example backend/.env       # 第一次設定
$EDITOR backend/.env                       # 填實際值

# === K8s 上線後:K8s Secret ===
# 1. 從 .env 直接建 secret(快速)
kubectl -n mcis create secret generic backend-secrets \
  --from-env-file=secrets.env

# 2. 或用 literal 一個一個建
kubectl -n mcis create secret generic backend-secrets \
  --from-literal=DB_PASSWORD=<value> \
  --from-literal=ANTHROPIC_API_KEY=<value>

# 看 secret(會被 base64 編碼,不是真的加密)
kubectl -n mcis get secret backend-secrets -o yaml
kubectl -n mcis get secret backend-secrets -o jsonpath='{.data.DB_PASSWORD}' | base64 -d

# 更新 secret(K8s 不支援 patch single key,要 replace 整個)
kubectl -n mcis create secret generic backend-secrets \
  --from-env-file=secrets.env \
  --dry-run=client -o yaml | kubectl apply -f -

# 刪 secret
kubectl -n mcis delete secret backend-secrets
```

### Azure / AKS(若採用)
```bash
# 登入
az login
az account set --subscription <SUBSCRIPTION_ID>

# 取得 AKS kubeconfig(merge 進 ~/.kube/config)
az aks get-credentials --resource-group mcis-rg --name mcis-aks

# 列出 / 切換 cluster
az aks list -o table
kubectl config use-context mcis-aks

# Azure Container Registry(若不用 ghcr.io)
az acr login --name mcisacr
docker tag mcis-backend:dev mcisacr.azurecr.io/mcis-backend:dev
docker push mcisacr.azurecr.io/mcis-backend:dev

# Key Vault secret(配合 External Secrets Operator)
az keyvault secret set --vault-name mcis-kv --name db-password --value <value>
```

### 自建 K8s(k3s — 輕量 / 單節點起步)
```bash
# 安裝 k3s(server)
curl -sfL https://get.k3s.io | sh -

# 取 kubeconfig
sudo cat /etc/rancher/k3s/k3s.yaml > ~/.kube/config-k3s
export KUBECONFIG=~/.kube/config-k3s

# 加 worker node
curl -sfL https://get.k3s.io | K3S_URL=https://<server-ip>:6443 K3S_TOKEN=<token> sh -
```

### Gemini API(runtime 預設)
```bash
# 取 API key:https://aistudio.google.com/apikey
export GEMINI_API_KEY="<your-key>"

# 直接呼叫(2.5 Flash — 速度快、免費額度高)
curl "https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=$GEMINI_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{
    "contents": [{
      "parts":[{"text": "用三句話介紹台南赤崁樓的歷史脈絡"}]
    }]
  }'

# 從程式呼叫:
#   Python:pip 裝 google-genai
#   Kotlin:用 OkHttp 直打 REST,或用社群 SDK
```

### Ollama(本地 LLM,fallback)
```bash
# 安裝
curl -fsSL https://ollama.com/install.sh | sh

# 拉模型(7-8B 模型約 4-5GB,16GB RAM 可跑)
ollama pull llama3.1:8b              # Meta Llama 3.1 8B
ollama pull qwen2.5:7b               # 阿里 Qwen 2.5 7B(中文較強)
ollama pull deepseek-r1:7b           # DeepSeek R1 蒸餾版

# 列已下載模型
ollama list

# CLI 互動測試
ollama run qwen2.5:7b "用三句話介紹台南赤崁樓的歷史脈絡"

# 啟動 server(預設 http://localhost:11434)
ollama serve

# 從程式呼叫(OpenAI 相容 API)
curl http://localhost:11434/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "qwen2.5:7b",
    "messages": [{"role":"user","content":"你好"}]
  }'
```

### 觀測性 stack 安裝(一次裝齊)
```bash
# Prometheus + Grafana + Alertmanager
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm upgrade --install monitoring prometheus-community/kube-prometheus-stack \
  -n monitoring --create-namespace

# Loki(集中 log)
helm repo add grafana https://grafana.github.io/helm-charts
helm upgrade --install loki grafana/loki-stack \
  -n monitoring --set promtail.enabled=true

# GlitchTip(自架 Sentry 相容)
helm repo add glitchtip https://glitchtip.github.io/helm-charts
helm upgrade --install glitchtip glitchtip/glitchtip -n monitoring

# Port-forward 看 Grafana(預設 admin / prom-operator)
kubectl -n monitoring port-forward svc/monitoring-grafana 3000:80
# 開瀏覽器 http://localhost:3000
```

### GitHub Actions(本地驗證)
```bash
# 用 act 在本地跑 workflow(節省雲端 minutes)
act -W .github/workflows/backend-ci.yml -j build
act pull_request -W .github/workflows/admin-ci.yml
```

---

## 11. 待補 / TODO(Prototype 階段)

> 以下是還沒決定、需要在進入實作前釐清的部分:

- [ ] 行動端最終選定:**原生 Android** vs **Kotlin Multiplatform**(後者代表要同時支援 iOS,需評估開發成本)
- [ ] K8s 部署具體選型:**自建(k3s / kubeadm)為主、AKS 為備援**;何時切到 AKS 的條件待定
- [ ] Gemini runtime 模型選定:**2.5 Flash**(快、免費額度高)還是 **2.5 Pro**(品質較好、額度低)— 視 narrative 品質測試結果決定
- [ ] Ollama fallback 模型選定:Qwen 2.5 7B(中文強)/ Llama 3.1 8B / DeepSeek — 視 demo 機 RAM 決定
- [ ] 第三方分享渠道實際範圍(IG / FB / LINE / Threads…)

---

> **使用者請審閱本文件後,改名為 `CLAUDE.md` 即生效。** 改名後 Claude Code 會在每次啟動時自動讀入。
