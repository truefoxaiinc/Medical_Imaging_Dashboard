# ScanFlow AI — Feature Registry

Status: Evidence-limited source inventory; not a functional test report. Owner: PM and Tech Lead.

| ID | Capability | Evidence level | Next gate |
|---|---|---|---|
| FEAT-001 | Streamlit research dashboard | Source entrypoint exists: integrated_app.py | Synthetic smoke test |
| FEAT-002 | BiomedCLIP research scoring | Source-described; correctness unresolved | AI-001 and AI-002 |
| FEAT-003 | Threshold-based classification | Source-described; not clinically validated | AI-002 and SAFE-001 |
| FEAT-004 | RAG and local LLM draft | Source-described; end-to-end unverified | RAG-001 and LLM-001 |
| FEAT-005 | Review interaction | Source-described; identity approval unverified | REP-001 and SAFE-001 |
| FEAT-006 | Docker research deployment | Config present; runtime not verified here | BASE-001 |
| FEAT-007 | DICOM conformance suite | Planned | IMG-001 |
| FEAT-008 | API and asynchronous job platform | Proposed architecture, not built | ARCH-001 and API-001 |
| FEAT-009 | PACS viewer interoperability | Proposed, not built | PACS-001 |
| FEAT-010 | Model registry and observability | Proposed, not built | MLOPS-001 |
| FEAT-011 | Hospital clinical product | Blocked, not authorized | PILOT-001 |

## Update rules
Use Proposed, Planned, In progress, Source-present/unverified, Test-verified, Deployed-with-evidence, Blocked or Retired. Before implementing, check for an existing feature and owner. Attach task ID, evidence, tests and approved contracts. Do not infer clinical readiness from UI or documentation.
