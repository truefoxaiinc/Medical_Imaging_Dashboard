# Medical Imaging Dashboard — Docker deployment

This repository contains a **research/demo application**, not a validated medical device. Use **de-identified images only** and keep the service behind authenticated HTTPS access.

## Services

- `dashboard`: Streamlit on container port 8501, bound to server loopback on `DASHBOARD_PORT` (default `18501`).
- `ollama`: local Ollama server accessible only on the Compose network.
- Persistent named volumes cache model weights and Ollama data.

No domain, Apache virtual host, existing Docker container, or host firewall rule is modified by these files.

## Prerequisites

Docker Engine with Compose v2, enough disk/RAM for PyTorch and Hugging Face weights, and an available loopback port. Initial model downloads require internet access. CPU inference may be slow.

## Start

```bash
git clone https://github.com/truefoxaiinc/Medical_Imaging_Dashboard.git
cd Medical_Imaging_Dashboard
cp .env.example .env
mkdir -p .streamlit
cp .streamlit/secrets.toml.example .streamlit/secrets.toml
chmod 600 .streamlit/secrets.toml
# Edit .streamlit/secrets.toml and change the username/password BEFORE starting.
docker compose config --quiet
docker compose build dashboard
docker compose up -d
docker compose exec ollama ollama pull llama3
docker compose ps
curl -fsS http://127.0.0.1:18501/_stcore/health
```

If you use a nondefault `DASHBOARD_PORT`, adjust the curl command. Visit through an SSH tunnel:

```bash
ssh -L 18501:127.0.0.1:18501 your-user@your-server
```

Then open `http://127.0.0.1:18501` in your local browser. Use a Virtualmin/Apache TLS reverse proxy to expose the service only after confirming security and access controls.

`integrated_app.py` is the documented default entrypoint. It uses the `llama3` model name by default, so the pull command above matches it. First upload triggers BiomedCLIP model downloads. The alternative `app.py`, `app_chest_all_conditions.py`, and `app_multibody_router.py` entrypoints have different Ollama APIs/default models; switching the entrypoint requires matching configuration/code adjustments, not just changing the Docker CMD.

## Commands

```bash
docker compose logs -f dashboard
docker compose logs -f ollama
docker compose restart dashboard
docker compose down
```

`docker compose down` preserves named volumes (do **not** add `-v` unless you intend to delete cached models and Ollama data).

## Important limitations

- The `integrated_app.py` code uses LangChain's `Ollama` integration, which normally reads `OLLAMA_HOST`. Verify connectivity with a trial upload.
- Model dependencies have minimum rather than pinned versions, so builds are not yet reproducible.
- A Streamlit health response confirms only that the web service is running, **not** that image inference, RAG or LLM work.
- Existing model thresholds are hardcoded in source and are **not** proof of clinical validation. Do not use AI outputs as diagnostic findings.
- A `medical.pem` key file was found in the public repository. Treat it as exposed; revoke/rotate it if used, and remove it from Git history following a coordinated security response. The Docker build excludes it.
- Do not upload identifiable patient records until a dedicated security/privacy and clinical validation review is complete.
