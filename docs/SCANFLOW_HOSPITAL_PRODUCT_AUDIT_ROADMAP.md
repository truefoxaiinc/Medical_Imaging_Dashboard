# ScanFlow AI — Technical Audit, Improvement Backlog & Hospital Product Roadmap

**Date:** 2026-10-28  
**Status:** Planning and static source review; NOT a clinical validation or production certification  
**Scope:** Existing Medical_Imaging_Dashboard; preserve working functionality and implement incrementally.  
**Intended initial scope:** Research-only adult frontal chest radiographs. No autonomous diagnosis.

> [!WARNING]
> This application is currently a research prototype. Do not use its outputs for diagnosis, patient triage, treatment, or automated clinical decisions. Hospital clinical use requires independent validation, safety engineering, regulatory assessment, and authorized deployment.

## 1. Evidence and audit boundaries

Directly inspected on GitHub main: `README.md`, `integrated_app.py`, `validate_thresholds.py` (2026-10-28). The broader prior audit also reports `inference.py`, `rag.py`, `llm.py`, `run.py`, `medical.pem` and deployment files, but those specific findings require separate source verification. No model execution, dataset evaluation, penetration test, DICOM conformance test, dependency scan, or live-server inspection was performed. Do not call findings clinically validated.

### Confirmed source findings
- `README.md` describes research/demo scope but also uses "clinically validated" thresholds and "final clinical approval" language; this is inconsistent without validation and authenticated approval evidence.
- `integrated_app.py` uses shared Streamlit credentials from secrets rather than per-user authenticated reviewer identity.
- `integrated_app.py` initializes BiomedCLIP/OpenCLIP, uses hardcoded per-pathology thresholds, and integrates local Ollama.
- `validate_thresholds.py` hardcodes a Windows dataset path, uses the first CSV file, creates labels with substring matching, and evaluates `df.head(1000)`. This is not a reproducible representative patient-level evaluation.
- Earlier audit identifies OpenCLIP output unpacking, across-label softmax, simplified DICOM transforms, report approval integrity, and prompt inconsistencies; reproduce each finding with version-pinned tests before changes.

### Critical security incident to verify immediately
A public `medical.pem` private-key exposure was reported. Do not print or copy its contents into issues, logs, PRs, or chat. Determine ownership, whether the key is real and in use, and the blast radius. If sensitive, rotate/revoke at the affected service first; remove tracked file; investigate Git history/forks/caches; rewrite history only with coordinated response. Add secret scanning and ignore patterns. Deleting a file does not make an exposed key safe.

## 2. P0 — Security and safety blockers

- [ ] **SEC-001** Inventory `medical.pem` and all exposed secrets without disclosing contents; document incident, affected assets, and rotation evidence.
- [ ] **SEC-002** Rotate/revoke exposed credentials, verify replacement works, and remove key from active tree; plan coordinated history cleanup.
- [ ] **SEC-003** Add Gitleaks, Trivy, Semgrep, dependency audit, Dependabot and secret-prevention CI; fail builds for confirmed critical findings.
- [ ] **SEC-004** Replace shared login with OIDC identity, individual accounts, RBAC (admin/reviewer/researcher), session security and MFA policy.
- [ ] **SEC-005** Restrict network exposure; TLS, least-privilege containers, non-root, secure secrets injection, audit access, backups and restore drills.
- [ ] **SAFE-001** Remove "clinically validated", "normal", "radiologist-approved" and "final diagnostic" claims until supported; label experimental scores and drafts clearly.
- [ ] **SAFE-002** Explicit statuses: unsupported exam, failed preprocessing, model unavailable, uncertain, no finding above research threshold, and reviewed report. Never equate a negative model output with a normal exam.
- [ ] **SAFE-003** No automatic patient-facing reports, triage, or treatment decisions; reviewer gate must be enforced server-side.

**Acceptance:** documented secret response; no active key exposure; CI checks; research-use labeling; tests ensure no report is signed without authenticated reviewer identity.

## 3. P1 — Inference correctness and evaluation

- [ ] **AI-001** Pin `open_clip_torch`, `torch`, model revision and tokenizer; test actual model return contract and embedding dimensions on CPU/GPU.
- [ ] **AI-002** Replace ambiguous `model(image,text)` unpacking with version-tested `encode_image`/`encode_text` or documented API; normalize embeddings and verify numeric stability.
- [ ] **AI-003** Remove across-pathology softmax from independent multi-label decisions; similarity scores are not calibrated probabilities.
- [ ] **AI-004** Establish a single canonical inference interface and versioned model registry; keep old BiomedCLIP as a research baseline until evaluated.
- [ ] **AI-005** Benchmark suitable multilabel chest-X-ray models (e.g. TorchXRayVision or MONAI/PyTorch implementations) with their documented preprocessing and intended label sets; verify licenses.
- [ ] **AI-006** Use patient-level separation, explicit labels/uncertain-label policy, site-level external holdout, no leakage, and versioned dataset manifests.
- [ ] **AI-007** Replace `df.head(1000)`, implicit CSV selection, substring label parsing and machine-specific paths with configurable deterministic evaluation.
- [ ] **AI-008** Report per-pathology AUROC, AUPRC, sensitivity, specificity, PPV/NPV at specified prevalence, calibration, bootstrap CIs, subgroup performance and failure cases.
- [ ] **AI-009** Choose thresholds on validation data against prespecified operating points; freeze thresholds before untouched external test; store dataset/model/threshold hashes.
- [ ] **AI-010** Evaluate OOD, image quality, portable AP, positioning, devices, implants, demographics, prevalence shift and abstention behavior.
- [ ] **AI-011** Regression tests: normal/positive/ambiguous synthetic fixtures, multi-positive case, empty findings, model errors, CPU fallback and deterministic scoring.
- [ ] **AI-012** Record model version, preprocessing version, score definition, threshold version and runtime metadata per inference.

**Acceptance:** reproducible offline evaluation artifacts and unit/integration tests. No clinical claims without prospective and external evidence.

## 4. P1 — DICOM and imaging reliability

- [ ] **IMG-001** Validate file magic/MIME, size limits, decompression limits, malformed DICOM, pixel array shapes, unsupported SOP classes and multi-frame studies.
- [ ] **IMG-002** Test rescale slope/intercept, modality LUT, VOI/windowing, MONOCHROME1/2, photometric interpretation, pixel padding, transfer syntaxes and orientation.
- [ ] **IMG-003** Define permitted chest radiograph views, reject unsupported modalities, and avoid silently converting incompatible studies.
- [ ] **IMG-004** Preserve study/series/instance identifiers securely; design de-identification for research, including burned-in annotations and DICOM tags.
- [ ] **IMG-005** Add golden-image and DICOM conformance regression fixtures; validate pixel transformations against reference viewers.

## 5. P1 — RAG, LLM, reports and review integrity

- [ ] **RAG-001** Select one canonical RAG implementation; distinguish FAISS vs in-memory similarity accurately.
- [ ] **RAG-002** Version knowledge documents with provenance, publication date, approved source, section/page references and retrieval tests.
- [ ] **LLM-001** Centralize prompt and Ollama client, pin model tag/digest, validate availability, timeout/retry, and graceful fallback.
- [ ] **LLM-002** Constrain generation to structured model findings and retrieved references; reject unsupported assertions and prompt injection in retrieved text.
- [ ] **LLM-003** Never represent general reference text as evidence of pathology in the patient's image.
- [ ] **REP-001** Implement study/report versions, draft vs reviewed vs signed states, authenticated reviewer, signed timestamp, edit history and immutable audit events.
- [ ] **REP-002** Enforce review on backend; do not infer approval from a checkbox or user-entered name.
- [ ] **REP-003** Support reproducible report export, explicit AI assistance disclosure, report provenance and authorized export only.

## 6. P2 — Hospital-ready platform architecture

**Suggested topology:** Hospital PACS/RIS/HIS -> Orthanc DICOM/DICOMweb gateway -> ingestion/validation -> Celery/Redis queue -> versioned PyTorch/MONAI inference -> findings + RAG/LLM draft -> FastAPI -> PostgreSQL and encrypted object storage -> Next.js worklist + OHIF viewer -> authenticated radiologist review -> authorized export/integration.

| Component | Preferred starting tool | Decision criteria |
|---|---|---|
| Web dashboard | Next.js, TypeScript, Tailwind, shadcn/ui | Accessible worklist, enterprise UX |
| Medical viewer | OHIF + Cornerstone | DICOMweb, overlays, image controls |
| PACS bridge | Orthanc | DICOM C-STORE/C-FIND/C-MOVE and DICOMweb |
| API | FastAPI + Pydantic | OpenAPI, RBAC, structured validation |
| Database | PostgreSQL | Studies, reports, users, audit metadata |
| Queue | Celery + Redis | Idempotent background inference |
| ML | PyTorch, MONAI; optional TorchXRayVision | Model/task fit, validation, licensing |
| MLOps | MLflow + DVC | Provenance and reproducibility |
| Retrieval | pgvector initially; FAISS if justified | Versioned references and retrieval tests |
| LLM | Ollama initially | On-prem local drafting; capacity benchmark |
| Identity | Keycloak/OIDC | SSO, MFA, role separation |
| Storage | Encrypted S3-compatible store | Retention, backup, access controls |
| Monitoring | Prometheus, Grafana, Loki, OpenTelemetry | Latency, failure, queue and model metrics |
| Security | Gitleaks, Trivy, Semgrep, Dependabot | CI enforcement |
| Deployment | Docker Compose pilot; Kubernetes only when justified | Isolation, reliability, operability |

**Important:** Audit licenses and deployment restrictions for models, datasets, libraries, and pretrained weights before commercial distribution. Avoid premature migration to Kubernetes or Triton; benchmark first. Preserve Streamlit as research sandbox while moving hospital UI to APIs.

### Required entities
`organizations`, `facilities`, `users`, `roles`, `studies`, `series`, `instances`, `processing_jobs`, `model_versions`, `inference_runs`, `finding_scores`, `threshold_sets`, `reference_documents`, `retrieval_events`, `report_versions`, `review_signatures`, `audit_events`, `integration_endpoints`, `consent_or_legal_basis_records`.

### Required hospital workflows
- DICOM study intake, worklist, prioritization, job status and retries.
- Radiologist viewer with prior study comparison, editing and versioned signing.
- Site and department scoped access, SSO, configurable retention and exports.
- PACS/DICOMweb integration and RIS/HIS interoperability (HL7/FHIR as appropriate).
- Failure-safe handling, model downtime, reconciliation, support diagnostics and backup/restore.
- PHI minimization, tenant isolation, encryption at rest/in transit, access logs, and breach-response playbooks.

## 7. Clinical, regulatory and quality program

Before selling as a diagnostic or triage medical product, establish:
- Intended use, target population, supported views/modalities, users, exclusions and claims.
- India CDSCO medical-device-software classification and applicable licensing pathway assessed by qualified regulatory counsel; country-specific review for other markets.
- Quality management (ISO 13485 as applicable), risk management (ISO 14971), software lifecycle (IEC 62304), usability (IEC 62366-1), health software security (IEC 81001-5-1), information security (ISO 27001), applicable privacy law.
- Clinical evaluation protocol, ethics/IRB approvals where required, lawful data access, external multi-site validation, prospective shadow-mode study and human factors testing.
- Hazard analysis for false negatives, false positives, wrong-patient linkage, data corruption, model drift, workflow delay and misleading LLM outputs.
- Post-market monitoring, complaint handling, corrective actions, software change control and incident reporting where applicable.

**Do not interpret this roadmap as regulatory approval.** Specific standards and licenses depend on intended use, classification, market and deployment.

## 8. Delivery phases and exit gates

### Phase 0 — Incident response and research safety (weeks 1–2 estimate)
Close SEC-001/002 and SAFE-001/002; inventory repository, dependencies, running services and secrets; create regression baseline. **Gate:** no active secret exposure; no misleading clinical claims.

### Phase 1 — Reproducible research pipeline (weeks 3–6 estimate)
Complete AI-001..012, IMG-001..005 and core RAG/LLM tests. **Gate:** pinned versions, passing test suite, frozen evaluation protocol, evidence-backed outputs.

### Phase 2 — Hospital workflow MVP (weeks 7–12 estimate)
Implement API, worklist, OHIF/Orthanc integration, persistence, RBAC, review workflow, job queue and operational telemetry. **Gate:** end-to-end de-identified study with traceable review and failure handling.

### Phase 3 — Controlled research pilot (month 4–6+ estimate)
On-prem deployment, authorized de-identified multi-site data, external evaluation, PACS integration, restore drills, clinical workflow feedback and security testing. **Gate:** predefined pilot criteria and documented safety results.

### Phase 4 — Regulated commercialization (evidence-dependent)
Complete classification, quality system, clinical evidence, licensing, procurement documentation, support SLA, contracts, incident response and maintenance commitments. **Gate:** appropriate authorization and verified operational readiness.

## 9. Commercial packaging

1. Research edition for medical colleges and research teams: explicitly non-diagnostic, controlled datasets, annual software/support license.
2. Hospital research pilot: limited scope, shadow mode, fixed integration and evaluation agreement, no patient-care reliance.
3. Clinical product only after applicable authorization: per-facility or usage-based commercial terms, deployment options, service-level commitments, audit and support.

Track cost per study, GPU utilization, turnaround time, false-positive burden, false-negative risk, reviewer edit rate, system availability, integration failures and security events.

## 10. Definition of done and implementation rules

- No wholesale rewrite; first inspect existing architecture and preserve working research demo.
- Each issue: reproducible evidence, risk, owner, proposed minimal change, test, rollback, docs update and acceptance result.
- Never merge secret-bearing changes; do not paste PHI or credentials into PR descriptions.
- Require automated unit/integration tests, security scans, review, and deployment verification before merging code.
- Separate documentation PR from later implementation PRs; this document proposes work and does not claim fixes have been implemented.
- Do not mark a clinical metric, threshold, or model "validated" without dataset/version and independent evidence.

## 11. Suggested follow-up pull requests

1. `security/secret-incident-and-ci` — rotation evidence, ignore rules, scans, safe defaults.
2. `fix/openclip-inference-contract` — pinned model interface and tests.
3. `research/multilabel-evaluation` — corrected scoring, dataset splits, metrics, thresholds.
4. `fix/dicom-processing-validation` — pixel and metadata handling with fixtures.
5. `feat/reviewer-audit-trail` — identity-bound approvals and report history.
6. `refactor/canonical-rag-llm` — grounded drafting and provenance.
7. `feat/hospital-api-worklist` — API, queue, persistence, viewer and PACS integration.
8. `docs/clinical-regulatory-readiness` — intended use, risk and pilot evidence.

## 12. Next verification commands (safe, read-only)

```bash
git status --short
git ls-files | grep -Ei '\.(pem|key|p12|pfx)$' || true
git ls-files | grep -E '(^|/)(requirements.*\.txt|pyproject.toml|Dockerfile|compose.*ya?ml|.*test.*\.py)$' || true
python -m pip freeze > /tmp/scanflow-installed-dependencies.txt
```

Never print private key contents. Run code, scans and dataset tests only in an authorized isolated environment.
