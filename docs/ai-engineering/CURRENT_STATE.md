# Current Project State — Living Checkpoint

**Updated:** 2026-10-28
**Branch context:** documentation-only BASE-001 branch `docs/base-001-reproducibility-baseline`, based on `9088aba59aef6492ea41de35eb9929fb8da3f359`.
**Status:** Research prototype. Hospital product is planned, not implemented.

## Known from GitHub source inspection
- `README.md` describes research MVP with Streamlit, BiomedCLIP, RAG and Ollama; its clinical-validation/approval wording requires SAFE-001 review.
- `Dockerfile` launches `integrated_app.py`; `compose.yaml` defines dashboard and Ollama; alternative Streamlit entrypoints exist.
- `requirements.txt` uses minimum version ranges; base images include mutable tags. Runtime/test baseline not yet executed.
- Root `tests/` and `.github/` paths returned 404 in targeted GitHub checks; this does not prove no tests exist elsewhere.
- `validate_thresholds.py` uses machine-specific dataset path and first-1000-rows selection.
- GitHub root metadata confirms `medical.pem` is present in the public repository; its contents were not read. SEC-001 remains open and urgent.
- See [BASE-001 baseline](BASE-001_BASELINE.md) for source inventory, limitations, verification and handoff.

## GOV-001 documentation checkpoint
- A separate documentation branch adds [master plan](PROJECT_MASTER_PLAN.md), [context index](AI_CONTEXT_INDEX.md), [feature registry](FEATURE_REGISTRY.md), [contract registry](CONTRACT_REGISTRY.md), [test strategy](TEST_STRATEGY.md), [deployment runbook](DEPLOYMENT_RUNBOOK.md), [token efficiency](TOKEN_EFFICIENCY.md), and [handoff](GOV-001_HANDOFF.md).
- These are proposed guidance, not approved ADRs or evidence of runtime deployment. Existing policies and application code remain unchanged.
- Documentation checks and PR review are required; runtime tests not run.

## Decisions
- Preserve existing working research demo.
- New hospital platform should be modular and API-based, subject to approved ADR.
- Model choice requires independent validation; Ollama is a runtime, not a measure of clinical accuracy.
- Research-only UI and documentation until clinical/regulatory authorization.
- BASE-001 is documentation only; no architecture, dependencies, security policy or runtime changed.

## Unknowns to inspect
- Exact runtime dependency resolution, working inference contract, test/CI status and deployment configuration.
- Secret exposure impact and revocation status (DevSecOps only).
- GPU VRAM, inference latency, Ollama model availability, datasets/licenses.
- Regulatory intended use, hospital integration requirements and clinical validation evidence.

## Next authorized tasks
1. P0 DevSecOps SEC-001 containment/rotation assessment with explicit authorization.
2. Complete BASE-001 runtime/test baseline in an approved isolated environment.
3. Reproduce OpenCLIP inference contract and multilabel scoring defects under AI-001/AI-002.
4. Review misleading validation/approval claims under SAFE-001 with clinical/security owners.
5. Build model evaluation protocol and DICOM golden tests.

## Update protocol
Change this file after meaningful verified milestones. Include date, branch/commit, tests, blockers and next action. Never claim deployment or clinical validation without evidence.
