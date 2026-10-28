# Architecture Guardrails

## Current versus target
Current: Streamlit research dashboard with BiomedCLIP/OpenCLIP, threshold script, RAG and Ollama paths. Full runtime audit is pending. Do not treat planned services as implemented.

Target (subject to ADR and validation): Next.js/TypeScript + OHIF; FastAPI; PostgreSQL; Celery/Redis; Orthanc/DICOMweb; versioned PyTorch/MONAI multi-label inference; curated RAG/pgvector; schema-constrained LLM or template; OIDC/RBAC; encrypted object storage; audit events; monitoring.

## Bounded contexts
1. Identity & tenant/facility authorization.
2. Imaging ingestion and DICOM normalization.
3. Study and job lifecycle.
4. Model inference and model/threshold provenance.
5. Knowledge retrieval and report drafting.
6. Radiologist review and report signing.
7. Interoperability and export.
8. Audit, observability and platform operations.

Each context owns its data contracts. Never directly couple UI to model internals or bypass API authorization. Model inference does not own report signing; LLM does not infer disease evidence from RAG.

## Interface invariants
- Study identity: immutable study/series/instance identifiers, controlled PHI access.
- Job states: received -> validated -> queued -> processing -> draft_ready -> in_review -> signed, with failed/unsupported/rejected transitions.
- Inference result: model_id, weight_hash, preprocessing_version, label_schema_version, score_definition, thresholds_version, raw scores, status, timestamps, failure/abstention reason.
- Draft report: source finding IDs, retrieved document IDs, generation model/version, schema-valid content, draft status; never auto-signed.
- Signed report: authenticated reviewer ID, signed_at, immutable event and version linkage.
- Errors are explicit; unsupported, failure and no-positive-findings are different states.

## Architecture change control
Any new dependency, service, schema migration, API breaking change, ML model change, storage layout or cloud architecture requires an ADR recording alternatives, decision, consequences, rollback and approvers.

## Quality and deployment
- Unit and integration tests for every changed contract; patient-level evaluation for model changes.
- DICOM golden fixtures and safe synthetic data.
- Reproducible builds, pinned dependencies, signed artifacts, secret scans.
- Development/staging/research pilot/clinical environments isolated; never use production PHI in developer tooling.
- Clinical use requires independent evidence and applicable approvals, not just passing CI.
