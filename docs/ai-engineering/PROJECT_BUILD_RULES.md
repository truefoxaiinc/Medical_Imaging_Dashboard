# ScanFlow AI — Project Build Rules and Definition of Done

**Status:** Proposed. Use with ENGINEERING_CONSTITUTION.md and AGENTS.md.

## A. Before starting any implementation
1. Confirm assigned task ID, priority, dependencies, acceptance criteria and reviewer.
2. Read only required project documents and related code; check existing functionality first.
3. Check whether feature already exists; reuse tested modules instead of rewriting.
4. Confirm allowed files, interfaces, database/model impacts, tests and rollback.
5. For high-risk changes, create/approve ADR before code.

## B. Implementation sequence
1. Create task branch from latest approved base.
2. Write/update tests that reproduce bug or define intended behavior.
3. Implement smallest vertical slice; avoid unrelated formatting/refactors.
4. Add typed schemas, explicit failures, logging without PHI and configuration validation.
5. Run unit tests; integration tests; typecheck/lint; security scans; model/DICOM tests where relevant.
6. Inspect diff and check backward compatibility, secrets, data migrations and error paths.
7. Update task ledger/current state and PR evidence; request appropriate reviewers.
8. Merge only after required human approvals and green CI.

## C. Layer-specific standards
### Frontend
- Accessible responsive Next.js UI; reusable components; loading/empty/error states.
- No raw patient data in browser analytics; never trust client-side authorization.
- Viewer must respect study access and prevent unrestricted DICOMweb exposure.
- Report actions require server-side role and state checks.

### Backend
- FastAPI versioned OpenAPI contracts, Pydantic validation, authz at every resource boundary.
- PostgreSQL migrations reversible where possible; safe backfills and constraints.
- Transactions for report version/signature; idempotency keys for jobs and exports.
- Clear domain boundaries; avoid business logic in routers.

### Imaging
- Validate DICOM input, SOP class, transfer syntax, metadata and pixel transforms.
- No silent normalization that changes clinical meaning; golden-image tests.
- Research de-identification checks include burned-in annotations.

### AI/ML
- Pin model and dependency versions; record artifact hashes.
- Train/evaluate only on approved data; patient-level splits, external holdout, calibration and subgroup metrics.
- No model change without evaluation and signoff.
- Explicit abstention, OOD and failure paths.

### RAG/LLM
- Curated versioned corpus; traceable references; constrained JSON output.
- No unsupported findings, no fabricated citations, no automatic clinical signoff.
- Deterministic template fallback when model unavailable or output unsafe.

### Cloud/MLOps
- Reproducible images, SBOM, scans, model registry, resource quotas, metrics and alerts.
- Separate environment secrets and data; no production changes without change window and rollback.
- Test GPU memory/latency/concurrency, retry/queue recovery, backup/restore.

## D. Required quality gates
| Gate | Requirement | Block when |
|---|---|---|
| Scope | Task ID and bounded diff | unrelated changes |
| Architecture | Approved ADR when needed | unapproved contract/service change |
| Security | secret/SAST/SCA/container scans | confirmed critical exposure |
| Tests | relevant unit/integration checks | failures or missing required tests |
| ML | versioned model and evaluation | unvalidated scoring/threshold change |
| Clinical | research-safe wording and reviewer gate | unsupported clinical claim |
| Data | migration, backup and rollback | unsafe irreversible data change |
| Operations | deployment plan, observability, smoke tests | uncontrolled release |
| Review | human signoff for high-risk change | missing approvals |

## E. Pull request template
```md
## Task
ID:
Problem:
Baseline evidence:
## Change
Files:
Behavior before/after:
Architecture / ADR:
## Verification
Commands and exact results:
Security scans:
ML/DICOM validation:
## Risk
PHI/security/clinical impact:
Migration and rollback:
Known limitations:
## Handoff
Current state update:
Next task:
```

## F. Definition of done
Done means **verified**: acceptance criteria pass, tests run, diff reviewed, approvals obtained, documentation/checkpoints updated and rollback known. An AI-generated answer or successful code write alone is not done.

## G. Agent anti-drift checklist
- Did I read the right source of truth?
- Am I modifying only assigned scope?
- Is this behavior already implemented?
- Are all clinical and security invariants preserved?
- Have I run and reported tests accurately?
- Can another engineer reproduce and continue?
