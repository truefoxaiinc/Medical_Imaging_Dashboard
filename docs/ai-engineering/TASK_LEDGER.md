# Task Ledger — Prioritized AI Engineering Backlog

Legend: TODO, IN_PROGRESS, BLOCKED, VERIFIED. Status is not evidence of test success; link to PR/commit/test report.

| ID | Priority | Task | Owner | Status | Exit gate |
|---|---|---|---|---|---|
| SEC-001 | P0 | Investigate reported exposed private key; rotate/revoke if real | DevSecOps | TODO | Documented incident closure |
| SAFE-001 | P0 | Correct research-only UI and report approval claims | Clinical + Backend | TODO | UI/test assertions |
| BASE-001 | P0 | Inventory code, dependencies, entrypoints and tests | Tech Lead | TODO | Baseline report |
| AI-001 | P0 | Verify OpenCLIP API contract and fix incorrect unpacking | AI Lead | TODO | Version-pinned tests |
| AI-002 | P0 | Correct multi-label score semantics and uncertainty | AI Lead | TODO | Tests and score contract |
| AI-003 | P1 | Patient-level validation and external evaluation protocol | AI + Clinical | TODO | Approved evaluation plan |
| IMG-001 | P1 | DICOM processing/conformance test suite | Imaging Lead | TODO | Golden fixtures |
| RAG-001 | P1 | Consolidate retrieval and source provenance | AI/Backend | TODO | Retrieval tests |
| LLM-001 | P1 | Schema-constrained grounded drafting and fallback | AI Lead | TODO | Safety benchmark |
| REP-001 | P1 | Identity-bound versioned report approval | Backend/Security | TODO | Authz + audit tests |
| ARCH-001 | P1 | Approve ADRs and API contracts | Tech Lead | TODO | Signed ADR |
| API-001 | P2 | FastAPI, PostgreSQL and background jobs | Backend Lead | TODO | Integration tests |
| PACS-001 | P2 | Orthanc + OHIF DICOMweb workflow | Imaging Lead | TODO | Interop tests |
| MLOPS-001 | P2 | Model registry, serving, monitoring and CI | MLOps | TODO | Benchmark/runbook |
| PILOT-001 | P3 | Approved research pilot and external validation | Clinical/PM | BLOCKED | Ethics/data/regulatory approvals |

## Task card template
```md
### TASK-ID — Title
Owner: | Priority: | Status:
Problem / evidence:
Scope / out of scope:
Dependencies / blockers:
Acceptance criteria:
Files likely touched:
Security / clinical risk:
Test plan:
Rollback:
PR / commit / test evidence:
Handoff:
```

Keep one active implementation task per agent. Do not mark VERIFIED until evidence is linked.
