# BASE-001 — Read-only engineering baseline

Date: 2026-10-28
Repository: truefoxaiinc/Medical_Imaging_Dashboard
Base branch: main
Base commit: `9088aba59aef6492ea41de35eb9929fb8da3f359`
Scope: documentation only; no application, dependency, infrastructure, security policy or model modifications.
Status: Documentation prepared for review; runtime baseline remains unverified.

## Evidence and entrypoints
- `Dockerfile` launches `streamlit run integrated_app.py` on port 8501 using Python 3.11-slim.
- `compose.yaml` defines `dashboard` and `ollama`; dashboard binds `127.0.0.1:${DASHBOARD_PORT:-18501}:8501`. The dashboard mounts local Streamlit secrets read-only.
- `compose.gpu.yaml` and `compose.production.yaml` request NVIDIA GPU access; no host GPU or runtime readiness was checked.
- `DOCKER.md` identifies `app.py`, `app_chest_all_conditions.py`, and `app_multibody_router.py` as alternative entrypoints with differing Ollama contracts.
- `run.py` is a standalone research pipeline, with a machine-specific dataset path and a separate LLM/model initialization path.
- `validate_thresholds.py` is a standalone threshold evaluation script with a machine-specific path and first-1000-rows selection; it is not independent clinical validation.
- `inference.py` provides a separate TorchXRayVision wrapper; no assertion is made that the default dashboard uses it.

## Dependency and build reproducibility
- `requirements.txt` uses minimum version specifiers (`>=`) rather than a lockfile with exact resolved versions and hashes.
- `Dockerfile` uses the mutable `python:3.11-slim` image tag; `compose.yaml` uses `ollama/ollama:latest`.
- The current manifest includes Streamlit, PyTorch/TorchVision, TorchXRayVision, OpenCLIP, Transformers, LangChain, pydicom, scikit-learn, and supporting libraries.
- Do not pin or upgrade dependencies under BASE-001; a separate authorized ADR/task must assess compatibility and lockfile policy.

## Test and CI discovery
- No root `tests/` directory was found (GitHub contents request returned 404).
- No root `.github/` directory was found (GitHub contents request returned 404).
- This is limited discovery, not proof that tests or automation are absent elsewhere.
- No Python tests, lint, dependency installation, Docker builds, image inference, model downloads, CI jobs or deployment checks were executed.
- Documentation verification: repository control files and targeted manifest/entrypoint files were read via GitHub; no runtime test results are claimed.

## Security and clinical restrictions
- GitHub root metadata lists `medical.pem` on the public default branch. Its content was **not read**. Treat SEC-001 as a separate urgent DevSecOps incident, including credential-use assessment, rotation/revocation as needed, and historical exposure handling. No remediation is claimed here.
- `.gitignore` excludes `.env` and `.streamlit/secrets.toml`, but does not list a generic `*.pem` rule; changing ignore rules is outside this documentation task.
- `README.md` uses language about clinical validation and approval that needs separate SAFE-001 clinical/security review. Do not interpret it as evidence of validated performance or authorization.
- No patient images, PHI, secret contents or credentials were accessed or included.

## Reproducible follow-up (authorized isolated environment only)
1. Checkout the exact base commit above and inspect `git status --short`.
2. Run `python -m compileall -q integrated_app.py app.py app_chest_all_conditions.py app_multibody_router.py inference.py run.py validate_thresholds.py` for syntax compilation only; this is **not** a functional test.
3. Run `docker compose config --quiet` only in an environment with safe placeholder secrets configuration; do not print interpolated configuration.
4. In a separately approved isolated test environment, install frozen dependencies and run synthetic-data unit/integration tests after a test harness is defined.
5. Record exact commands, tool versions, commit, exit codes and sanitized logs. Do not run inference on patient data.

## Acceptance / remaining gaps
- [x] Document entrypoints, major dependency and container manifests.
- [x] Record initial test/CI discovery and explicit verification limitations.
- [x] Record security and clinical blockers without accessing sensitive material.
- [ ] Execute runtime/test baseline (not authorized or available in this documentation-only task).
- [ ] Establish an approved pinned dependency manifest and test harness (separate implementation task).

## Handoff
Task: BASE-001. Branch: `docs/base-001-reproducibility-baseline`.
Files intended to change: this report, `TASK_LEDGER.md`, `CURRENT_STATE.md`.
Before/after: no runtime behavior change; documentation and checkpoints only.
ADR: none required for documentation; ADR-000 through ADR-003 remain proposed.
Risk: SEC-001 open; SAFE-001, AI-001 and AI-002 unresolved. Do not merge this documentation PR as incident closure.
Rollback: revert documentation commit or close draft PR; no deployments or data migrations.
Next: DevSecOps SEC-001 response; then approved isolated test-baseline task.
