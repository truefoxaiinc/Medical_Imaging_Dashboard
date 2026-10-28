# ScanFlow AI — Token-Efficient Agent Workflow

Goal: Reduce redundant context and tool usage without weakening verification.

## Rules
1. Read mandatory control documents once per new session, then use AI_CONTEXT_INDEX.md.
2. Reuse CURRENT_STATE.md and baseline evidence if relevant SHA and files are unchanged.
3. Inspect directly affected modules and tests first; expand only for dependencies and risk.
4. Do not paste full repositories, datasets, secrets or repeated policies into prompts.
5. Use one task ID, one bounded plan and one active implementation scope per agent.
6. Check FEATURE_REGISTRY.md before adding functionality; reuse verified components.
7. Batch independent metadata reads; avoid repeated broad searches and expensive model downloads.
8. Prefer minimal reversible diffs and targeted tests before affected integration.
9. Record meaningful checkpoints once, with links and exact evidence.
10. Diagnose failures before retrying; do not refactor unrelated code to make a check pass.
11. Separate observed facts, assumptions and proposals; never invent metrics or tests.
12. Stop when permissions, safe data, architecture decisions or test prerequisites are missing.

## Compact agent output
TASK/STATUS; BASELINE EVIDENCE; PLAN/FILES; CHECKS (PASS/FAIL/NOT RUN); RISKS/BLOCKERS; COMMIT/PR; NEXT OWNER/ACTION.

Optimization never means skipping security, imaging, clinical, authorization or rollback gates. No fixed token budget overrides safety.
