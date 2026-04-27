# threadwalk

> **外國旅客在台灣初期探索文化的體驗設計**(MCIS Project)— 期末專案
> 重新設計「文化被理解的方式」:解決資訊存在卻無法被連結與理解的問題。
> MVP 範圍:**台南單城市試點**(NFR-18),驗證後再擴張全台。

---

## 📚 文件導覽

> 全部文件集中在 [`references/`](./references/) 與 [`CLAUDE.md`](./CLAUDE.md)。
> **新加入專案先讀** §1 → §2 → §3,其他依角色取用。

### §1 需求與設計

| 文件 | 內容 | 何時要看 |
|---|---|---|
| [`references/requirements.md`](./references/requirements.md) | 完整需求規格:Actors、UC1–UC18、FR-01–FR-23、NFR-01–NFR-20、口試回饋對照 | 不確定「要做什麼」時 |
| [`references/architecture.md`](./references/architecture.md) | 系統架構:6 張分層圖(總覽 / Client / 14 個服務 / 資料層 / 外部整合 / 端到端流程)+ 部署選型建議 | 不確定「服務怎麼分」時 |
| [`references/diagram.md`](./references/diagram.md) | Activity Diagram(5 張)+ Domain Class Diagram + System Sequence Diagram(6 張) | 寫某 use case 之前 |

### §2 開發流程與排程

| 文件 | 內容 | 何時要看 |
|---|---|---|
| [`references/schedule.md`](./references/schedule.md) | **6 週 agile sprint 進度表**:Backlog(13 Epic / 62 Story)、各 sprint goal、容量配置、Demo Script、Risk Register | 不確定「現在該做什麼」時 |
| [`references/agile-guide.md`](./references/agile-guide.md) | **Agile / Scrum 入門指南**:角色、儀式流程、Story Points、DoR/DoD、GitHub Project 操作、FAQ | 第一次跑 agile / 卡在某個儀式 |

### §3 開發準則

| 文件 | 內容 | 何時要看 |
|---|---|---|
| [`CLAUDE.md`](./CLAUDE.md) | 編碼規範(Kotlin / Android / React / Python)、測試 / Git / CI / CD / 部署 / 安全 / 命令速查 / 與 AI 協作原則 | **每天會用** — 寫程式前 / push 前 / 部署前 |

### §4 進行中的工作

- **GitHub Project**:[MCIS Sprint Board](https://github.com/users/killin0415/projects/1) — 看本週承諾、卡點、進度
- **Issues**:[killin0415/threadwalk/issues](https://github.com/killin0415/threadwalk/issues) — 62 張 backlog ticket(對應 `schedule.md` §5)

---

## 🛠 技術棧速查

> 完整選型理由與替代方案見 [`CLAUDE.md`](./CLAUDE.md) §1.1 & §2。**核心原則:免費 / 自架 / 開源優先。**

| 層 | 技術 |
|---|---|
| **Backend** | Kotlin + Spring Boot(JDK 21)+ Gradle Kotlin DSL,PostgreSQL + Redis,Flyway,SpringDoc OpenAPI |
| **AI Services** | Python 3.11+,uv 環境管理,Gemini API(runtime)+ 訂閱版 LLM(build-time 內容)+ Ollama(fallback) |
| **Mobile** | 原生 Android(Kotlin + Jetpack Compose)— **KMP 為待定選項** |
| **Kiosk** | 瀏覽器 SPA(Next.js / Vite) |
| **Admin CMS** | Vite + React + TypeScript + react-admin / Refine,TanStack Query + Zustand |
| **CI/CD** | GitHub Actions,Docker(multi-stage)+ Docker Hub,Kubernetes(自建 k3s 為主、AKS 備援)+ Kustomize / Helm |
| **觀測性** | Prometheus + Grafana + Loki + GlitchTip(Sentry 開源版,全部自架) |
| **地圖** | OSM + Leaflet/MapLibre + 台灣 PTX/TDX(免費) |

---

## 🚀 Quick Start

### 起本地開發環境

```bash
# 1. 起依賴(PG + Redis)
docker compose -f docker/docker-compose.dev.yml up -d

# 2. Backend
cd backend && ./gradlew bootRun
# Swagger UI: http://localhost:8080/swagger-ui.html

# 3. Admin CMS
cd admin && pnpm install && pnpm dev

# 4. AI Services
cd ai-services && uv sync && uv run python -m narrative.main

# 5. Android(用 Gradle CLI,不依賴 Android Studio)
cd android && ./gradlew installDebug
```

> 完整命令速查見 [`CLAUDE.md`](./CLAUDE.md) §10。

---

## 📁 專案結構(預期)

```
threadwalk/
├── backend/          # Kotlin Spring Boot
├── ai-services/      # Python LLM / 分類 / 敘事
├── android/          # 原生 Android (Kotlin + Compose)
├── kiosk/            # Kiosk Web (Next.js / Vite)
├── admin/            # Admin CMS (Vite + React + TS)
├── deploy/           # K8s Kustomize manifests
│   ├── base/
│   └── overlays/{dev,staging,prod}
├── docker/           # 共用 Dockerfile / compose
├── .github/workflows/ # GitHub Actions CI/CD
├── references/       # ★ 需求 / 架構 / 排程 / 流程文件
├── .claude/          # Claude Code 設定 + memory(隨 git 同步)
│   └── memory/       # 專案級 memory(多裝置共享)
├── CLAUDE.md         # ★ 開發準則(必讀)
└── README.md         # 本文件
```

> 尚未建立的資料夾,在第一次需要時補上。

---

## 👥 團隊

- **設計組(2 人)**:D1(Lead Designer / PO)+ D2(Content / UX Researcher)
- **系統組(4–6 人)**:S1(Backend Lead / SM / Tech Lead)+ S2 ~ S6(Backend / Android / AI / Admin / DevOps)

詳細分工見 [`schedule.md`](./references/schedule.md) §3。

---

## 📜 授權

見 [`LICENSE`](./LICENSE)。

---

## 🔗 相關連結

- **Repo**:https://github.com/killin0415/threadwalk
- **Project Board**:https://github.com/users/killin0415/projects/1
- **Issues**:https://github.com/killin0415/threadwalk/issues
