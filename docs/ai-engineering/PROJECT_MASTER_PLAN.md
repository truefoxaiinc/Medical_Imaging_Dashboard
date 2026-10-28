# ScanFlow AI — GOV-001 master plan

Status: Proposed documentation; not an implementation approval.

## Phases and gates
1. Discovery: research intended use, stakeholders, requirements, nonfunctional constraints; PM and Clinical review.
2. Baseline: inventory dependencies, tests, and reproducibility; Tech Lead review.
3. Architecture: ADR approval, contract ownership, threat model and rollback; Tech Lead and Security review.
4. Imaging/data: authorized data provenance, DICOM conformance and synthetic fixtures; Imaging and Privacy review.
5. AI/ML: versioned model, preprocessing, score semantics, evaluation and abstention; AI and Clinical review.
6. Delivery: small task-based vertical slices with regression and contract tests; Engineering review.
7. Quality: security, privacy, accessibility, integration, performance and reliability evidence; QA review.
8. Staging/research release: pinned artifacts, backup/restore, smoke checks, monitoring and rollback; MLOps and DevSecOps approval.
9. Operations: incidents, cost, capacity, patches and drift review with assigned owners.
10. Clinical pathway: blocked pending independent validation and applicable approvals.

## Milestones
M0 governance and baseline; M1 approved scope and ADRs; M2 tested research workflows; M3 approved staging; M4 authorized research pilot; M5 clinical use only after regulatory and clinical authorization. No dates or clinical performance are asserted.

## Delivery contract
Requirement -> task ID -> scoped plan -> approved interfaces -> tests -> PR -> human review -> staged release -> monitored outcome. Current status and blockers live in CURRENT_STATE.md and TASK_LEDGER.md. Do not treat a roadmap item as an implemented capability.
