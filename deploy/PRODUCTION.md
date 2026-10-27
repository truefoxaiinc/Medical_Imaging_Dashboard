# medical.truefoxaiinc.com — isolated NVIDIA deployment

This is an operations runbook, not a completed deployment. The repository is a research/demo application; do not use for clinical diagnosis or upload identifiable patient data.

## Preflight (no changes to existing workloads)

```bash
nvidia-smi
docker info --format '{{json .Runtimes}}'
docker compose version
docker ps --format 'table {{.Names}}\t{{.Ports}}'
sudo ss -ltnp | grep -E ':(18501|443|80)\b' || true
sudo apache2ctl -S
```

Stop if port 18501 is occupied or GPU runtime is unavailable. Docker Compose's `gpus: all` requires a working NVIDIA Container Toolkit; installing it changes host Docker configuration and needs separate approval. Verify GPU access with a compatible CUDA test image first. GPU availability in the container does not establish that PyTorch was installed with CUDA support.

## Staging (only the medical-imaging project)

Clone main after this PR is merged. Run commands from the repository directory as the deployment user:

```bash
cp .env.example .env
mkdir -p deploy .streamlit
cp deploy/secrets.toml.example deploy/secrets.toml
# Production override replaces the base secrets mount with deploy/secrets.toml.
# Both bind mounts remain present after Compose merges volume entries, so avoid
# conflicting mount targets: use the base .streamlit/secrets.toml instead.
cp deploy/secrets.toml .streamlit/secrets.toml
chmod 644 .streamlit/secrets.toml
# Edit credentials, then ensure UID 10001 inside the container can read them.
nano .streamlit/secrets.toml
docker compose -p medical-imaging -f compose.yaml -f compose.production.yaml config
```

**IMPORTANT:** Inspect the rendered Compose mounts. The production override is intended to replace the base secrets mount; Docker Compose volume merging uses container target as the uniqueness key. If both mounts remain, do not start the service. The production secret must be readable by container UID 10001 without making it publicly accessible; a dedicated deployment group/ACL is preferable to broad file permissions.

```bash
docker compose -p medical-imaging -f compose.yaml -f compose.production.yaml build dashboard
docker compose -p medical-imaging -f compose.yaml -f compose.production.yaml up -d
docker compose -p medical-imaging -f compose.yaml -f compose.production.yaml exec ollama ollama pull llama3
docker compose -p medical-imaging -f compose.yaml -f compose.production.yaml ps
curl -fsS http://127.0.0.1:18501/_stcore/health
docker compose -p medical-imaging -f compose.yaml -f compose.production.yaml exec dashboard python -c 'import torch; print(torch.cuda.is_available())'
```

If torch CUDA is false, stop and prepare a compatible CUDA-enabled PyTorch build; the current base Dockerfile does not guarantee CUDA. Check actual GPU memory (RTX 3060) before running BiomedCLIP and llama3 together. Validate Streamlit login and a de-identified end-to-end inference test.

## Virtualmin / Apache

1. Point the DNS A record for `medical.truefoxaiinc.com` at the intended server public IP.
2. Create a **new dedicated** Virtualmin virtual server for this domain, with SSL certificate. Do not edit existing domains.
3. Once the app passes staging checks, add the directives from `deploy/apache-medical.conf.example` to **that domain's SSL vhost only**; do not paste a new global vhost or overwrite Virtualmin-managed configuration.
4. Confirm `mod_proxy`, `mod_proxy_http`, `mod_headers` and WebSocket proxy support, then `apache2ctl configtest` before a graceful reload. Confirm WebSocket connectivity and HTTPS.
5. Put the domain behind an additional access control layer (VPN/IP allowlist or identity provider). Streamlit demo auth alone is not adequate for patient data.

## Rollback

Remove proxy directives only from the dedicated medical domain and validate Apache before reloading. Stop only this Compose project:

```bash
docker compose -p medical-imaging -f compose.yaml -f compose.production.yaml down
```

Do not use `down -v`, `docker system prune`, restart Docker, or modify other projects.

## Security issues

A `medical.pem` key is present in the public repository history. Treat it as compromised, revoke/rotate if active, and coordinate history cleanup. Hardcoded clinical thresholds and unpinned Python dependencies require review. Production readiness requires functional, CUDA, security, and privacy validation.
