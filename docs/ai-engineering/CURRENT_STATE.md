# Current Project State — Living Checkpoint

**Updated:** 2026-10-28  
**Branch context:** documentation-only setup; no runtime changes.  
**Status:** Research prototype. Hospital product is planned, not implemented.

## Known from GitHub source inspection
- `README.md` describes research MVP with Streamlit, BiomedCLIP, RAG and Ollama.
- `integrated_app.py` has Streamlit shared authentication, model initialization and hardcoded pathology thresholds.
- `validate_thresholds.py` uses machine-specific dataset path and a first-1000-rows evaluation approach.
- Prior audit reports exposed `medical.pem`, OpenCLIP scoring and approval integrity issues. Confirm and remediate; do not treat report as incident closure.

## Decisions
- Preserve existing working research demo.
- New hospital platform should be modular and API-based, subject to ADR.
- Model choice requires independent validation; Ollama is a runtime, not a measure of clinical accuracy.
- Research-only UI and documentation until clinical/regulatory authorization.

## Unknowns to inspect
- Actual running branch/commit, dependency versions, model API contract and test status.
- Full source inventory, active entrypoints, Docker environment and secrets exposure impact.
- GPU VRAM, inference latency, Ollama model availability, datasets/licenses.
- Regulatory intended use, hospital integration requirements and clinical validation evidence.

## Next authorized tasks
1. P0 investigate `medical.pem` exposure and rotate affected secrets.
2. Establish read-only inventory, test baseline and pinned dependency manifest.
3. Reproduce OpenCLIP inference contract and multilabel scoring defects.
4. Remove misleading clinical validation/approval claims in a scoped PR.
5. Build model evaluation protocol and DICOM golden tests.

## Update protocol
Change this file after meaningful verified milestones. Include date, branch/commit, tests, blockers and next action. Never claim deployment or clinical validation without evidence.
