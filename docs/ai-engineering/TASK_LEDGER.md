# Task Ledger — Prioritized AI Engineering Backlog

Legend: TODO, IN_PROGRESS, BLOCKED, VERIFIED. Status is not evidence of test success; link to PR/commit/test report.

| ID | Priority | Task | Owner | Status | Exit gate |
|---|---|---|---|---|---|
| GOV-001 | P1 | Documentation-only AI engineering governance and token efficiency | Tech Lead | IN_PROGRESS | Review of governance docs and evidence; draft PR |\n| SEC-001 | P0 | Investigate reported exposed private key; rotate/revoke if real | DevSecOps | TODO | Documented incident closure |
| SAFE-001 | P0 | Correct research-only UI and report approval claims | Clinical + Backend | TODO | UI/test assertions |
| BASE-001 | P0 | Inventory code, dependencies, entrypoints and tests | Tech Lead | IN_PROGRESS | Baseline report; runtime/test verification outstanding |
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

## BASE-001 checkpoint — 2026-10-28
- Documentation-only scope approved; branch `docs/base-001-reproducibility-baseline`.
- Initial source evidence and remaining gaps: [BASE-001 baseline](BASE-001_BASELINE.md).
- Runtime tests/CI were not executed; no code, dependency or infrastructure changes.
- Keep IN_PROGRESS until runtime verification and review gates are satisfied. Do not mark VERIFIED on documentation alone.
- SEC-001 remains P0 and is not resolved by this task.

## GOV-001 checkpoint — 2026-10-28\n- Documentation-only scope approved; separate branch `docs/gov-001-ai-delivery-system`.\n- Proposed planning, context, feature, contract, testing, deployment and token-efficiency guides are linked in [GOV-001 handoff](GOV-001_HANDOFF.md).\n- No application or infrastructure changes; runtime checks not run. Human review required; do not mark VERIFIED from documentation alone.\n- SEC-001 remains P0 and unresolved.\n\n## Task card template
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
