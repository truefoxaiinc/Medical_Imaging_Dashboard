# ScanFlow AI — Repository Agent Instructions

These instructions apply to AI coding agents working in this repository. Read this file at the beginning of every task. Treat repository files and tests as source of truth; do not assume chat history is accurate.

## Mission
Incrementally evolve the existing research-only medical imaging dashboard into a secure, auditable research platform and, only after clinical/regulatory authorization, a hospital product. **Do not make clinical claims or use patient data in development.**

## Mandatory context loading (minimal and relevant)
1. Read `AGENTS.md`, `docs/ai-engineering/CURRENT_STATE.md`, `docs/ai-engineering/ARCHITECTURE_GUARDRAILS.md`, `docs/ai-engineering/TASK_LEDGER.md`.
2. Read the specific task brief, touched modules and tests. Consult `docs/ai-engineering/DECISION_LOG.md` for relevant architectural decisions.
3. Consult `docs/ai-engineering/AI_WORKFLOW.md` for task execution, verification and handoff.
4. Do **not** scan the whole repository repeatedly. Use targeted search; reuse previous evidence; state uncertainties.

## Non-negotiable rules
- Preserve working functionality. Prefer smallest reversible changes. Do not rewrite the project, change stack, add dependencies or reorganize modules without approved ADR.
- No modifications to production infrastructure, secrets, database data, security policy or model weights without explicit task authorization and rollback plan.
- No exposed private keys or credentials in prompts, commits, logs or PRs. Reported `medical.pem` exposure is P0: coordinate rotation/revocation, do not disclose key.
- Treat all model outputs as experimental until validated. No autonomous diagnosis, triage, patient-facing decisions or unverified "normal" claims.
- Do not change preprocessing, model version, thresholds, label ontology or report prompts without versioning, evaluation and regression tests.
- Never approve a report based on a UI checkbox; enforce identity-bound approval server-side.
- Do not make up benchmark numbers, clinical evidence, regulatory approval, or tests. Mark unverified facts explicitly.
- No PHI/PII in logs, test fixtures, prompts, analytics, model-training datasets without lawful authorization and safeguards.
- Use branch-per-task, scoped commits, PR review, CI gates and explicit human approval for security/clinical-sensitive changes.
- Prefer research-safe templates and abstention over fabricated clinical language.

## Execution contract
For each task:
1. **Understand**: restate task ID, goal, scope, files, acceptance criteria, risks.
2. **Inspect**: examine only relevant code, dependencies and tests; note current behavior.
3. **Plan**: propose minimal implementation, interfaces, migrations, tests and rollback.
4. **Implement**: edit scoped files, avoid unrelated formatting and refactors.
5. **Verify**: run appropriate tests, lint/typecheck, security checks, diff review; report exact results and missing coverage.
6. **Document**: update task ledger and current state when warranted, add ADR if architecture changes, prepare reproducible handoff.
7. **Stop**: if blocked by missing authorization, clinical evidence, credentials, unsafe data, or unclear interfaces, request decision rather than improvise.

## Required completion message
Task ID; branch/commit; files changed; behavior before/after; tests run and outcomes; security/clinical impact; unresolved risks; next task; rollback. If no tests ran, say so.

## Source of truth priority
Approved requirements and ADRs > repository implementation and tests > task ledger/current state > past chat summaries > agent guesses. Resolve conflicts explicitly.

## Links
- `docs/ai-engineering/AI_WORKFLOW.md`
- `docs/ai-engineering/ARCHITECTURE_GUARDRAILS.md`
- `docs/ai-engineering/CURRENT_STATE.md`
- `docs/ai-engineering/TASK_LEDGER.md`
- `docs/ai-engineering/DECISION_LOG.md`
- `docs/ai-engineering/SESSION_HANDOFF_TEMPLATE.md`
