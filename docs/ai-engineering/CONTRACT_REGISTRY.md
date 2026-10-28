# ScanFlow AI — Contract Registry

Status: Proposed interfaces only. These are not deployed APIs. Owner: Tech Lead.

| Boundary | Owner | Required fields / behavior | Approval |
|---|---|---|---|
| Imaging ingest | Imaging | Study identity, supported formats, transform provenance, explicit unsupported state | IMG-001 and ADR |
| Job lifecycle | Backend | Job ID, idempotency, state transitions, retries, failure reason | API-001 and ADR |
| Inference | AI | Model ID, weight hash, preprocessing/label/threshold versions, score definition, abstention | AI-001/002 and ADR |
| Retrieval | RAG | Corpus version, source IDs, access controls | RAG-001 |
| Drafting | LLM/reporting | Evidence IDs, model version, schema, draft-only status | LLM-001 |
| Review/signing | Backend/security | Authenticated reviewer, authorization, report version, audit event | REP-001 |
| Interoperability | Imaging | DICOMweb version, access boundaries, error cases | PACS-001 |

## Contract-change gate
Name provider/consumer and version. Link approved ADR. Define success, failure, unsupported and abstention behavior. Require synthetic contract tests, backward compatibility, privacy review and rollback. LLM drafts cannot create findings or sign reports. Proposed boundaries do not authorize stack changes.
