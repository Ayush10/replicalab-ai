# Deployment Guide (Person C — Max)

---

## Local Development

```bash
# Create and activate virtualenv
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate

# Install server deps
pip install -r server/requirements.txt

# Install replicalab package
pip install -e . --no-deps

# Run the server
uvicorn server.app:app --host 0.0.0.0 --port 7860 --reload
```

Server is live at `http://localhost:7860`.

Quick smoke test:
```bash
curl http://localhost:7860/health
# {"status":"ok","env":"stub"}

curl -X POST http://localhost:7860/reset \
  -H "Content-Type: application/json" \
  -d '{"seed": 42, "scenario": "cell_biology", "difficulty": "easy"}'
```

---

## Docker (Local)

```bash
docker build -t replicalab .
docker run -p 7860:7860 replicalab
```

With secrets (LLM API key for the Scientist):
```bash
docker run -p 7860:7860 \
  -e ANTHROPIC_API_KEY=sk-ant-... \
  replicalab
```

---

## Hugging Face Spaces Deployment (API 10)

### One-time setup

1. Create a HF Space at https://huggingface.co/new-space
   - SDK: **Docker**
   - Space name: `replicalab` (or agreed team name)
   - Visibility: Public (required by hackathon rules)

2. Add the Space as a remote:
   ```bash
   git remote add space https://huggingface.co/spaces/<HF_USERNAME>/replicalab
   ```

3. Push:
   ```bash
   git push space master
   ```
   HF Spaces detects the `Dockerfile` and builds automatically.
   The `README.md` frontmatter (`sdk: docker`, `app_port: 7860`) controls the Space config.

4. Live URL will be: `https://<HF_USERNAME>-replicalab.hf.space`

### Setting secrets (API keys) in HF Space

Go to **Space Settings → Repository secrets** and add:

| Secret name | Value |
|-------------|-------|
| `ANTHROPIC_API_KEY` | Your Anthropic API key |

The server can then read them via `os.environ.get("ANTHROPIC_API_KEY")`.

**Never commit API keys to the repo.**

### Verify the live deployment

```bash
SPACE_URL="https://<HF_USERNAME>-replicalab.hf.space"

# Health check
curl $SPACE_URL/health

# Reset endpoint
curl -X POST $SPACE_URL/reset \
  -H "Content-Type: application/json" \
  -d '{"seed": 42, "scenario": "cell_biology", "difficulty": "easy"}'

# WebSocket (using wscat or Python)
wscat -c wss://<HF_USERNAME>-replicalab.hf.space/ws
# then send: {"type":"reset","seed":42,"scenario":"cell_biology","difficulty":"easy"}
```

---

## Environment URLs Reference

| Service | Local | HF Space |
|---------|-------|---------|
| FastAPI app | `http://localhost:7860` | `https://<space>.hf.space` |
| Health | `http://localhost:7860/health` | `https://<space>.hf.space/health` |
| WebSocket | `ws://localhost:7860/ws` | `wss://<space>.hf.space/ws` |
| Scenarios | `http://localhost:7860/scenarios` | `https://<space>.hf.space/scenarios` |

---

## Telling Person B (Ayush) when ready

Once API 06 (WebSocket) is confirmed working locally:
- Ping Ayush with the local WebSocket URL: `ws://localhost:7860/ws`

Once API 10 (HF Space) is live:
- Ping Ayush with the Space URL and confirm `/health` returns 200
- Notebook env URL: `wss://<space>.hf.space/ws`

---

## Troubleshooting

| Issue | Fix |
|-------|-----|
| `ReplicaLabEnv not found` warning at startup | Normal — using stub until Person A ships `replicalab/env/replicalab_env.py` |
| HF Space build fails | Check Docker logs in Space build tab; most likely a missing dep in `server/requirements.txt` |
| CORS error from frontend | Add the frontend origin to `allow_origins` in `server/app.py` |
| WebSocket closes immediately | Check idle timeout (300s); ping periodically to keep alive |
| Session not found (REST) | REST sessions expire after 5 min idle; call `/reset` again to create a new one |
