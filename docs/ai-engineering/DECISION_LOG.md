# Architecture Decision Log

## ADR-000 — AI-assisted engineering discipline (Proposed, 2026-10-28)
**Context:** Multiple AI coding sessions risk losing project context, duplicating code, drifting architecture and making unsupported medical claims.  
**Decision:** Use AGENTS.md plus living current-state/task ledger, task-scoped plans, ADRs, tests and mandatory handoffs.  
**Consequences:** More explicit documentation, but lower rework and more traceable changes.  
**Approval:** Pending human tech lead review.

## ADR-001 — Separate classifier from report generator (Proposed)
**Context:** BiomedCLIP image scoring and LLM narrative generation have different evidence requirements.  
**Decision:** Separate versioned image inference, calibration, retrieval, LLM drafting and human approval services.  
**Alternatives:** Monolithic Streamlit-only pipeline; direct multimodal diagnosis.  
**Reason:** Independent validation, safer failure handling and traceability.  
**Approval:** Pending AI/Clinical Lead.

## ADR-002 — Incremental hospital architecture (Proposed)
**Decision:** Keep Streamlit research demo; incrementally add FastAPI/PostgreSQL/queue, OHIF/Orthanc and Next.js after requirements and compatibility tests.  
**Alternatives:** Immediate rewrite; microservices from day one.  
**Reason:** Preserve working functionality and minimize migration risk.  
**Approval:** Pending Platform/Tech Lead.

## ADR-003 — Model selection by evidence (Proposed)
**Decision:** Compare task-specific multilabel classifiers and medically oriented LLMs on representative licensed data, external holdout, clinician review, latency and deployment fit. No predeclared most-accurate model.  
**Approval:** Pending AI/Clinical Lead.

For every approved ADR, record owner, date, status, exact API/schema impacts, alternatives, risks, rollback and links to tests/PR.
