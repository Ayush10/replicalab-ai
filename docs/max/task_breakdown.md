# Person C (Max) Task Breakdown and Execution Plan

Source of truth: `ReplicaLab_Comprehensive_Task_Division.md`

Role: **Backend and Infra Lead**
Owns: FastAPI server, WebSocket handling, Docker, HF Space deployment, logging utilities, replay endpoints, server integration.

---

## 1. Overview

Person C is the critical path for both the frontend demo (Person D depends on working API endpoints) and the entire training pipeline (Person B depends on API 06 and API 10). Ship the server early — everything else unblocks from it.

---

## 2. Files Person C Owns

| File | Purpose |
|------|---------|
| `server/app.py` | FastAPI app: REST routes, WebSocket handler, CORS, session mgmt |
| `server/Dockerfile` | Docker build serving app on port 7860 |
| `server/requirements.txt` | Pinned runtime dependencies for standalone server install |
| `replicalab/utils/logging.py` | `write_episode_log()`, `write_reward_csv()`, log schema, episode IDs |
| `frontend/vite.config.ts` | Dev proxy and build output config (shared with Person D) |
| `pyproject.toml` | Python project config and dependency declaration |
| `frontend/package.json` | React + Vite frontend shell |
| `.dockerignore` | Excludes `.git`, `node_modules`, `notebooks/`, `tests/`, `__pycache__`, `.venv`, output files |
| HF Space `README.md` | YAML frontmatter: `sdk: docker`, `app_port: 7860` |

---

## 3. What Other People Need From You (Priority Order)

### Person B (Ayush) is blocked on:

| Your Task | Unblocks | Their Tasks |
|-----------|---------|-------------|
| **API 06** — WebSocket handler | Person B training pipeline | TRN 03 (env client), TRN 13 (client.py module) |
| **API 10** — Deployed HF Space | Person B notebook | TRN 01 (notebook skeleton) |

**→ Deliver API 06 first, then API 10. These are the most team-critical deliverables.**

### Person D (frontend) is blocked on:

| Your Task | Unblocks | Their Tasks |
|-----------|---------|-------------|
| **API 01–API 06** | All UI functionality | UI 04, UI 06, UI 07, UI 08 |
| **UI 11** (yours) | Dev integration | One-command local dev |

### Person A (environment core) feeds you:

You cannot build server endpoints until Person A delivers the environment. Coordinate with Person A early on:

1. **ENV 01** — basic env class → unblocks API 01 shell
2. **ENV 02** — reset logic → unblocks API 02
3. **ENV 06** — step logic → unblocks API 03, API 06
4. **FND 09** — openenv.yaml → unblocks API 19
5. **JDG 05** — reward breakdown → unblocks JDG 07
6. **ENV 11** — judge audit in step result → unblocks API 18, OBS 09

---

## 4. Recommended Execution Order

### Phase 1: Foundations (no external dependencies)

Start immediately. No blockers from other team members.

| ID | Task | Est |
|----|------|-----|
| FND 02 | `pyproject.toml` with Python project config | 0.5h |
| FND 03 | React + Vite frontend shell | 0.5h |
| FND 05 | `.gitignore` + `.dockerignore` rules | 0.25h |
| FND 07 | Branch naming, PR template, issue template | 0.5h |
| FND 11 | `server/requirements.txt` | 0.25h |
| FND 12 | `frontend/vite.config.ts` with proxy config | 0.5h |

**Total: 2.5h — do this first, in parallel while Person A builds ENV 01.**

### Phase 2: Logging Utilities (after Person A delivers MOD 04)

| ID | Task | Est |
|----|------|-----|
| MOD 07 | State serialization helper for replay logs | 0.5h |
| OBS 01 | Standardize episode log schema | 0.5h |
| OBS 03 | Episode ID generation and file naming | 0.25h |

**Total: 1.25h**

### Phase 3: Core Server (after Person A delivers ENV 01, ENV 02, ENV 06)

This is the highest-impact phase — unblocks Person B and Person D.

| ID | Task | Est |
|----|------|-----|
| API 01 | FastAPI shell + `GET /health` | 0.5h |
| API 13 | CORS middleware | 0.25h |
| API 02 | `POST /reset` endpoint | 0.75h |
| API 03 | `POST /step` endpoint | 0.75h |
| API 14 | REST session management (isolated state per user) | 0.75h |
| API 04 | `GET /scenarios` endpoint | 0.5h |
| **API 06** | **WebSocket session handler** ← Person B unblocked here | 1.25h |
| API 07 | Idle timeout + graceful disconnect | 0.75h |
| OBS 02 | Log levels and console formatting | 0.5h |
| TST 06 | Health + reset + step tests | 0.75h |
| TST 07 | WebSocket tests | 0.75h |

**Total: 7.5h — API 06 is the milestone, ping Person B when done.**

### Phase 4: Logging and Replay (after Person A delivers JDG 05, ENV 09, and ENV 11)

| ID | Task | Est |
|----|------|-----|
| JDG 07 | Log reward breakdown to CSV/JSONL | 0.5h |
| ENV 09 | Write episode logs on completion | 0.5h |
| OBS 09 | Extend episode summary with audit fields | 0.5h |
| API 05 | `GET /replay/{episode_id}` | 0.75h |
| API 18 | Judge audit payload in REST/WS/replay responses | 0.5h |
| OBS 07 | Local script to run one episode and dump logs | 0.5h |
| TST 11 | Contract tests for audit payloads | 0.75h |
| MOD 10 | Publish schema examples for clients | 0.5h |

**Total: 5h**

### Phase 5: Docker and Deployment (after Phase 3 + 4 stable, before UI 10 from Person D)

| ID | Task | Est |
|----|------|-----|
| API 08 | Dockerfile, app on port 7860 | 0.75h |
| API 16 | Docker builds frontend + serves static assets | 0.75h |
| API 09 | HF Space metadata + deploy instructions | 0.5h |
| API 15 | HF Space `README.md` with YAML frontmatter | 0.25h |
| API 17 | Document secrets and API key management | 0.5h |
| **API 10** | **Deploy live HF Space** ← Person B notebook unblocked here | 1h |
| API 19 | Verify `/web` fallback route on HF Space | 0.5h |
| TRN 11 | Document env URL and connection troubleshooting | 0.25h |

**Total: 4.5h — API 10 is the milestone, ping Person B when done.**

### Phase 6: Integration

| ID | Task | Est |
|----|------|-----|
| UI 11 | Serve frontend with backend / configure proxy | 0.5h |

---

## 5. Summary Table

| Phase | Description | Hours | External Gate |
|-------|-------------|-------|--------------|
| 1 | Foundations | 2.5h | None — start immediately |
| 2 | Logging utilities | 1.25h | Person A: MOD 04 |
| 3 | Core server + API | 7.5h | Person A: ENV 01, ENV 02, ENV 06 |
| 4 | Logging and replay | 5h | Person A: JDG 05, ENV 09, ENV 11 |
| 5 | Docker and deployment | 4.5h | Phases 3+4 stable |
| 6 | Integration | 0.5h | Person D: UI 10 |
| **Total** | | **~21.25h** | |

---

## 6. Key Risks

| Risk | Impact | Mitigation |
|------|--------|------------|
| Person A ENV 01–06 delayed | Blocks entire server build | Use mock env stub locally; stub `reset()` and `step()` to return hardcoded data so API shape can be tested |
| HF Space Docker build fails | Blocks Person B notebook (TRN 01) | Test Docker locally first, validate on port 7860 before pushing to HF |
| WebSocket session isolation bugs | Two training clients corrupt each other's state | Build API 14 REST isolation first as a simpler reference; add per-connection env instances in API 06 |
| Secrets not configured in HF Space | Scientist LLM calls fail in deployed env | Document in API 17 early; test with a dummy key before real creds |
| Frontend build breaks Docker image | Single-container deploy fails | Test `API 16` Docker step locally before merging with CI |

---

## 7. Ports and URLs Reference

| Service | Local URL | HF Space URL |
|---------|-----------|-------------|
| FastAPI app | `http://localhost:7860` | `https://<space>.hf.space` |
| `GET /health` | `http://localhost:7860/health` | `https://<space>.hf.space/health` |
| WebSocket | `ws://localhost:7860/ws` | `wss://<space>.hf.space/ws` |
| OpenEnv `/web` fallback | `http://localhost:7860/web` | `https://<space>.hf.space/web` |
| Frontend dev server | `http://localhost:5173` | Served from FastAPI in production |

---

## 8. Environment Setup

```bash
# Python environment
python -m venv .venv
source .venv/bin/activate
pip install -r server/requirements.txt

# Frontend
cd frontend
npm install
npm run dev  # runs on :5173, proxies API to :7860

# Run server locally
uvicorn server.app:app --port 7860 --reload

# Docker build and run
docker build -f server/Dockerfile -t replicalab .
docker run -p 7860:7860 replicalab
```
