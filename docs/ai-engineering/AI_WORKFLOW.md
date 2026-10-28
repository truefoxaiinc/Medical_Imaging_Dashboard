# AI-Driven Engineering Workflow

## How to use coding AI professionally
Use AI as an implementation assistant, not as unreviewed architecture authority. Assign one bounded task at a time, give its acceptance criteria and constraints, inspect the diff, and require tests. Do not ask an agent to "build everything" in one run.

## Standard task loop
**0. Select task:** PM/tech lead assigns one task ID from TASK_LEDGER.  
**1. Context:** agent reads AGENTS, CURRENT_STATE, ARCHITECTURE_GUARDRAILS, task brief and only related source/tests.  
**2. Reconnaissance:** identify existing behavior, call sites, tests, dependencies and risk. No repeated full scans.  
**3. Plan:** list touched files, API contracts, data migration, expected test cases, rollback. Obtain approval for major changes.  
**4. Build:** smallest vertical slice, preserve interfaces, add tests first where feasible.  
**5. Validate:** run targeted tests, then integration/CI as appropriate; verify no PHI/secrets; inspect git diff.  
**6. Review:** AI self-review plus human review for architecture/security/clinical-sensitive work.  
**7. Checkpoint:** update TASK_LEDGER, CURRENT_STATE and ADR if relevant; record commit, tests and next step.  
**8. PR:** title with task ID, explain before/after, evidence, risks, rollback, screenshots when UI changes. No auto-merge of high-risk work.

## Prompt template for VS Code Codex
```text
You are the senior engineer assigned to ScanFlow AI task [TASK-ID].
First read AGENTS.md, docs/ai-engineering/CURRENT_STATE.md,
ARCHITECTURE_GUARDRAILS.md, TASK_LEDGER.md, and the task-specific files.
Do not repeat a full repository audit or modify unrelated files.
Goal: [one concrete outcome].
Acceptance criteria: [measurable behavior and tests].
Constraints: preserve current behavior, no PHI, no clinical claims,
no stack/architecture changes without ADR, no secret disclosure.
Inspect relevant code and existing tests; give a concise plan.
Implement only the approved scope, run targeted verification,
review the diff, update task/checkpoint documents, and report
changed files, exact tests/results, risks, commit and rollback.
Stop and ask if authorization or clinical validation is required.
```

## Roles / review gates
- Product Manager: scope, user stories, priorities, release criteria.
- Tech Lead: architecture, API boundaries, ADR approval.
- Principal AI Engineer: dataset, model, threshold, evaluation review.
- Cloud/MLOps: model registry, GPU serving, CI/CD, telemetry, reproducibility.
- DevSecOps: secrets, auth, threat model, hardening, supply chain.
- Imaging Engineer: DICOM/PACS conformance.
- Clinical Lead: intended use, validation, human factors, patient safety.
- QA/Regulatory: verification traceability, release evidence.

## Anti-drift checklist
- Are we solving the assigned task only?
- Are decisions consistent with approved ADRs?
- Are model versions and output semantics unchanged unless explicitly authorized?
- Did we verify rather than assume?
- Are tests/rollback documented?
- Did we preserve working code and avoid duplicate services?
- Is the next agent able to continue without chat history?

## Failure handling
If a command fails, capture minimal sanitized error, identify root cause, try bounded correction, rerun verification. Never repeatedly change unrelated modules or claim success without evidence. If blocked, write a handoff and stop.
