# Person C (Max) Task List

Source of truth: `ReplicaLab_Comprehensive_Task_Division.md`

Role: **Backend and Infra Lead** — FastAPI server, WebSocket handling, Docker, HF Space deployment, logging, replay endpoints.

---

## Epic E01. Foundations and Project Setup

- [x] **FND 01** | Create repo structure and base folders from agreed layout | 0.5h | ✅ Completed by Ayush
- [ ] **FND 02** | Add Python project config and dependencies placeholder (`pyproject.toml`) | 0.5h | Depends: FND 01
- [ ] **FND 03** | Initialize React plus Vite frontend shell (`frontend/package.json`) | 0.5h | Depends: FND 01
- [ ] **FND 05** | Add ignore rules for Python, Node, logs, notebooks, and build artifacts (`.gitignore` + `.dockerignore`) | 0.25h | Depends: FND 01
- [ ] **FND 07** | Define branch naming, PR template, and issue template | 0.5h | Depends: FND 01
- [x] **FND 10** | Create output directory structure (`logs/`, `replays/`, `plots/`) and add to gitignore | 0.25h | ✅ Completed by Ayush
- [ ] **FND 11** | Create `server/requirements.txt` pinning FastAPI, uvicorn, websockets, and runtime deps | 0.25h | Depends: FND 02
- [ ] **FND 12** | Create `frontend/vite.config.ts` with API and WebSocket proxy support | 0.5h | Depends: FND 03

---

## Epic E02. Domain Models

- [ ] **MOD 07** | Add state serialization helper for replay logs (`replicalab/utils/logging.py`) | 0.5h | Depends: MOD 04
- [ ] **MOD 10** | Publish schema examples for frontend and notebook clients (API docs) | 0.5h | Depends: MOD 01–MOD 04

---

## Epic E05. Judge Engine and Reward

- [ ] **JDG 07** | Log reward breakdown to CSV or JSONL per episode (`replicalab/utils/logging.py`) | 0.5h | Depends: JDG 05, MOD 07

---

## Epic E06. Environment Core Loop

- [ ] **ENV 09** | Write episode logs on completion (`replicalab/utils/logging.py`) | 0.5h | Depends: ENV 06, JDG 07

---

## Epic E07. API, Server, Docker, and Deployment

- [ ] **API 01** | Create FastAPI app shell and health endpoint (`GET /health`) | 0.5h | Depends: ENV 01
- [ ] **API 13** | Add CORS middleware configuration for frontend origins in dev and production | 0.25h | Depends: API 01
- [ ] **API 02** | Add `POST /reset` endpoint | 0.75h | Depends: ENV 02
- [ ] **API 03** | Add `POST /step` endpoint | 0.75h | Depends: ENV 06
- [ ] **API 14** | Add REST session management for isolated per-user environment state | 0.75h | Depends: API 02, API 03
- [ ] **API 04** | Add `GET /scenarios` endpoint | 0.5h | Depends: SCN 03–SCN 05
- [ ] **API 05** | Add `GET /replay/{episode_id}` endpoint | 0.75h | Depends: ENV 09
- [ ] **API 06** | Add WebSocket session handler | 1.25h | Depends: ENV 06
- [ ] **API 07** | Add idle timeout and graceful disconnect cleanup | 0.75h | Depends: API 06, ENV 08
- [ ] **API 18** | Include judge audit payload in REST, replay, and WebSocket responses for terminal episodes | 0.5h | Depends: API 03, API 05, API 06, ENV 11
- [ ] **API 08** | Build Dockerfile with Python app startup on port 7860 | 0.75h | Depends: API 01–API 07
- [ ] **API 16** | Configure Docker to build frontend and serve static assets from FastAPI | 0.75h | Depends: API 08, UI 10
- [ ] **API 09** | Add Hugging Face Space metadata and deploy instructions | 0.5h | Depends: API 08
- [ ] **API 15** | Create HF Space `README.md` with YAML frontmatter (`sdk: docker`, `app_port: 7860`) | 0.25h | Depends: API 08
- [ ] **API 17** | Document secrets and API key management for Scientist LLM access | 0.5h | Depends: API 09
- [ ] **API 10** | Deploy live Space and verify health, reset, and step | 1h | Depends: API 09
- [ ] **API 19** | Expose and verify OpenEnv built-in `/web` fallback route locally and on HF Space | 0.5h | Depends: FND 09, API 08, API 10

---

## Epic E08. RL Training Pipeline (Support)

- [ ] **TRN 11** | Document environment URL, secrets, and connection troubleshooting | 0.25h | Depends: TRN 03

---

## Epic E09. Frontend and UI (Integration)

- [ ] **UI 11** | Serve frontend with backend or configure proxy during dev | 0.5h | Depends: UI 07, API 01

---

## Epic E10. Logging and Observability

- [ ] **OBS 01** | Standardize episode log schema for transcript, state snapshots, and scores | 0.5h | Depends: ENV 09
- [ ] **OBS 02** | Add local log levels and readable console formatting | 0.5h | Depends: API 01
- [ ] **OBS 03** | Add episode ID generation and file naming conventions | 0.25h | Depends: OBS 01
- [ ] **OBS 07** | Add simple local script to run one episode and dump logs | 0.5h | Depends: ENV 06, OBS 01
- [ ] **OBS 09** | Extend episode summary schema with `judge_notes`, `agreement`, `invalid_action_count`, `invalid_action_rate` | 0.5h | Depends: OBS 01, JDG 11, ENV 11

---

## Epic E11. Testing

- [ ] **TST 06** | Add health + reset + step endpoint tests (`tests/test_server.py`) | 0.75h | Depends: API 01–API 03
- [ ] **TST 07** | Add WebSocket connection and invalid payload tests | 0.75h | Depends: API 06
- [ ] **TST 11** | Add contract tests for judge audit payloads and invalid action metrics | 0.75h | Depends: API 18, OBS 09

---

## Totals

| Metric | Value |
|--------|-------|
| Total tasks (excluding completed) | 33 |
| Completed by Ayush | 2 |
| Total estimated hours | ~18.25h |
