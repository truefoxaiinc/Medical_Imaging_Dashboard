# ScanFlow AI — Enterprise Master Project Blueprint
**Version:** 1.0 | **Date:** 2026-10-28 | **Owners:** Product Manager, Clinical Lead, Principal AI Engineer, Platform Lead, Cloud/MLOps Lead, Security Lead, QA/Regulatory Lead  
**Status:** Proposed implementation plan, not executed or clinically approved.  
**Repository:** truefoxaiinc/Medical_Imaging_Dashboard  
**Scope:** Start with adult frontal chest radiograph research workflow; expand only through separately validated intended uses.

> SAFETY GATE: Research use only until the appropriate regulatory, clinical and quality gates are met. No autonomous diagnosis, triage, treatment, or clinical deployment on the basis of this plan.

## 0. Executive decision record

**Recommendation:** Do not replace BiomedCLIP with an LLM and assume diagnostic accuracy improves. Build **two independently evaluated model services**: (1) a dedicated multi-label chest X-ray classifier with explicit calibration and abstention, and (2) a constrained, source-grounded radiology report-drafting model. Maintain the current Streamlit app as a research sandbox; implement hospital product as separate API and web UI modules. Preserve working code, migrate incrementally, and maintain rollback.

**Shortlist to benchmark, not a predeclared winner:**
- Image classification: supervised DenseNet121 (TorchXRayVision research baseline), MONAI/PyTorch fine-tuned DenseNet/EfficientNet/ViT, MedSigLIP embeddings plus task-specific classifier, and CXR-specialized foundation-model baselines (e.g. ELIXR/CXR Foundation legacy, if licensing and availability permit). Keep BiomedCLIP as zero-shot baseline.
- Image-aware report drafts: **MedGemma 1.5 4B multimodal**, MedGemma 1 27B multimodal (hardware permitting), plus a structured report template baseline. Evaluate medically adapted alternatives with verified weights/license.
- Text-only report drafting from **validated structured findings**: MedGemma 27B text, a strong current general instruct model, and a deterministic templating baseline. **No text model may invent findings or override the classifier or reviewer.**
- Serving: Ollama is a local inference runtime, **not a model quality tier**. Compare Hugging Face Transformers, vLLM (when architecture supported), llama.cpp/Ollama (when supported), and Triton for imaging model serving. Select based on compatibility, reproducibility, latency, concurrency and hardware—not presumed accuracy.

**2026 model caution:** Google MedGemma 1.5 is a 4B multimodal model; earlier MedGemma includes 27B text and multimodal variants. Verify exact variant and release at procurement time. Google explicitly states these models require task-specific adaptation/validation and are not intended for direct clinical diagnosis. MedSigLIP is recommended by Google for medical-image embedding/classification research without generation.

## 1. Product charter

**Problem:** Radiology research teams need secure ingestion, reproducible AI experiments, auditable review and interoperable workflows.  
**MVP:** De-identified chest X-ray upload/PACS research ingestion, quality gate, multi-label experimental findings, transparent score provenance, RAG references, constrained report draft, authenticated human review, export and audit.  
**Out of scope for MVP:** CT/MRI, pediatrics, mammography, urgent autonomous triage, patient-facing advice, automatic report signoff, real-world clinical decision-making.  
**Personas:** Radiologist reviewer, research scientist, hospital IT/PACS admin, facility admin, compliance auditor, support engineer.  
**Product success:** Measurable reliability, safe handling, reproducibility, review efficiency and clinical evidence—not demo aesthetics.

## 2. Existing code audit and migration boundaries

Inspected GitHub source on 2026-10-28: `README.md`, `integrated_app.py`, `validate_thresholds.py`. Other modules and deployment environment require full inspection and test execution.

**Confirmed/reported issues requiring tickets:**
1. Reported exposed `medical.pem` key: verify real use, rotate affected key, remove and investigate history; never paste key contents.
2. Shared Streamlit login and non-persistent approval evidence: add authenticated identity, RBAC, review state machine, immutable audit.
3. OpenCLIP inference output contract and cross-label softmax: version-pin and reproduce; implement task-appropriate multi-label scores.
4. Hardcoded pathology thresholds, `df.head(1000)`, substring labels and Windows paths: replace with reproducible patient/site-level evaluation.
5. Simplified DICOM pixel processing: verify rescale/LUT/window/photometric/multi-frame/transfer syntax, quality and metadata controls.
6. RAG/LLM duplicate pathways, Ollama availability failures and unsupported diagnostic wording: unify generation service and explicit fallbacks.
7. Multiple entry points and potential demo placeholders: inventory, designate canonical production/research entrypoints, deprecate carefully.

**Rule:** Existing code must not be deleted or mass-refactored without a dependency map, test coverage, migration plan and rollback. See companion `docs/SCANFLOW_HOSPITAL_PRODUCT_AUDIT_ROADMAP.md` in PR #4 (merge separately).

## 3. Model-selection protocol — AI Lead ownership

### 3.1 Separate tasks
| Task | Input | Output | Model type | Clinical concern |
|---|---|---|---|---|
| Exam/view eligibility | DICOM + image | supported/unsupported | metadata rules + quality classifier | wrong exam |
| Image quality | X-ray | quality / abstain | QA model + rules | false reassurance |
| Pathology scoring | eligible X-ray | per-label raw score | multi-label supervised model | false negatives |
| Score calibration | raw score + version | calibrated estimate when supported | validation-derived calibration | unreliable probabilities |
| Retrieval | confirmed model/reviewer findings | references + provenance | embedding retrieval | irrelevant evidence |
| Report drafting | structured findings + sources | JSON draft | constrained text LLM/template | hallucination |
| Review | draft + original image | signed/returned/rejected | authenticated human | wrong signoff |

### 3.2 Candidate comparison
| Candidate | Role | Strength | Limitations / evaluation |
|---|---|---|---|
| BiomedCLIP | zero-shot baseline | medical image-text alignment | similarity is not calibrated multilabel probability |
| TorchXRayVision DenseNet121 | supervised research baseline | reproducible CXR task baseline | label mapping, training cohort and domain shift |
| MONAI fine-tuned DenseNet/ViT | primary supervised candidate | task-specific multi-label objective | requires licensed representative data and validation |
| MedSigLIP + classifier | foundation embedding candidate | medical visual features, fine-tuning | must train downstream head and externally validate |
| CXR Foundation / ELIXR | secondary baseline | CXR-specific features | Google calls CXR Foundation legacy; verify access |
| MedGemma 1.5 4B multimodal | image-aware report research | medical image + text generation | not approved diagnostic system, hallucinations |
| MedGemma 27B multimodal | higher-capacity research comparison | multimodal reasoning | substantial memory/latency, no automatic superiority |
| MedGemma 27B text | text-only grounded drafting | medical language | cannot inspect image itself |
| General instruct LLM | text drafting control | deployment flexibility | clinical factuality not guaranteed |
| Deterministic templates | safety baseline | auditable and low hallucination | limited natural-language flexibility |

### 3.3 Evaluation datasets and governance
- Define label ontology and target population before selection; document uncertain labels and view position.
- Consider CheXpert, MIMIC-CXR, NIH ChestXray14, PadChest and appropriately licensed local hospital datasets. **Verify licenses, credentialed access, ethics and permitted commercial use; do not assume public means commercially reusable.**
- Patient-level split; separate validation and untouched test; external site/device holdout; detect duplicates and leakage.
- Record dataset manifest, DICOM transformations, source labels, adjudication, demographics where lawful, acquisition site, model weights hash, commit and seed.
- Clinical expert adjudication for reference standard and disagreement; avoid treating weak report-mined labels as perfect ground truth.
- Prespecify sensitivity/specificity operating points per pathology; evaluate AUROC/AUPRC, sensitivity, specificity, PPV, NPV, calibration (ECE/Brier/reliability), confidence intervals, subgroup performance, and abstention.
- Evaluate AP/PA, portable devices, image quality, implants, comorbidities, population and prevalence shift, OOD, multi-positive cases.
- Use prospective silent/shadow deployment before any clinical workflow impact.
- **Winner selection:** Meets safety thresholds on external holdout, acceptable operational burden, supported license, deterministic deployability and clinician review. No model is designated most accurate in advance.

### 3.4 Report model evaluation
- Inputs restricted to validated structured findings and curated references; separate optional image-aware exploratory pathway.
- Force JSON schema: observations, evidence_refs, limitations, draft_text, unsupported_claims, model_version; reject invalid outputs.
- Measure factual consistency, omission, unsupported additions, negation, laterality, severity, uncertainty, citation correctness, edit distance, radiologist preference and inter-reader agreement.
- Human evaluation blinded to candidate; safety-critical error rate prioritized over fluent wording.
- Add prompt-injection tests for retrieved sources; redact PHI in logs; no internet inference without signed data agreements.
- Deterministic template is mandatory fallback when LLM fails or confidence insufficient.

## 4. Hardware and serving plan

**Current server information to verify live:** Ubuntu VPS, 8 vCPU, ~24 GB RAM, NVIDIA RTX 3060 LHR (VRAM not verified). Do not assume the GPU has enough VRAM for 27B models.

| Tier | Deployment | Notes |
|---|---|---|
| Development | CPU/RTX 3060 + quantized 4B where supported | Benchmark throughput, VRAM and correctness; no patient data |
| Research pilot | Dedicated GPU with measured headroom | Separate inference worker and API; encrypted storage |
| Hospital on-prem | Isolated GPU node, PACS gateway and HA data services | Backup, access control, monitoring, support |
| Multi-hospital SaaS | Dedicated tenant boundaries, regional residency controls | Regulatory and data-processing approval before PHI |

Benchmarks: model load time, cold/warm latency p50/p95/p99, images/min, tokens/sec, GPU utilization/VRAM, concurrency, queue wait, timeout, crash recovery, model drift, cost per study and operational uptime. Run realistic batch/concurrency tests; no invented capacity guarantees.

Serving decision: Transformers for compatibility/research; vLLM for supported high-throughput generation; Triton/ONNX Runtime for validated image classifier serving; Ollama/llama.cpp for simple local deployments when model support exists. Pin weights, tokenizers, quantization and runtime; quantify quantization effects on report factuality.

## 5. Reference architecture

```text
Hospital PACS/RIS/HIS -- DICOM/DICOMweb/HL7/FHIR --> Orthanc Integration Gateway
  -> ingestion + de-identification policy + DICOM validation + quality gate
  -> PostgreSQL study metadata + encrypted object storage
  -> Celery/Redis processing queue -> versioned image inference (MONAI/PyTorch/Triton)
  -> per-label findings + calibrated scores + uncertainty/abstention
  -> source-grounded RAG (versioned corpus + pgvector)
  -> schema-constrained LLM draft (MedGemma / vetted alternative / template)
  -> FastAPI authorization + audit + report state machine
  -> Next.js worklist + OHIF/Cornerstone image viewer
  -> authenticated radiologist review -> signed/versioned export -> authorized hospital systems
```

No RAG document can establish an image finding. No LLM can independently sign or promote a draft to clinical diagnosis.

### Stack choices
- Frontend: Next.js, TypeScript, Tailwind, shadcn/ui, OHIF/Cornerstone; WCAG accessibility and keyboard workflow.
- Backend: FastAPI, Pydantic, SQLAlchemy/Alembic, PostgreSQL; strict API versioning and idempotency.
- PACS: Orthanc and DICOMweb; HL7/FHIR only as integration requirements justify.
- Queue: Celery/Redis; DLQ/retry policy, deduplication, status machine and cancellation.
- ML: PyTorch, MONAI, TorchXRayVision research baseline, MLflow, DVC, pytest, MONAI transforms.
- RAG: curated medical corpus, embeddings, pgvector (or FAISS after scale testing), citations and source freshness.
- Serving: Transformers/vLLM/Ollama/Triton as independently benchmarked adapters.
- Identity: Keycloak/OIDC, MFA, facility-scoped RBAC; signed reviewer actions.
- Storage: S3-compatible encrypted object storage, retention and restore; separate PHI from telemetry.
- Observability: OpenTelemetry, Prometheus, Grafana, Loki, Sentry/self-hosted equivalent after PHI review.
- Security: Gitleaks, Semgrep, Trivy, SBOM/Syft, dependency scanning, secret manager, TLS, signed artifacts.
- Deployment: Docker Compose initially, Kubernetes only when HA/scale warrants; Terraform/Ansible and GitHub Actions with protected environments.

## 6. API and database contracts

**API namespaces:** `/api/v1/auth`, `/facilities`, `/studies`, `/series`, `/instances`, `/jobs`, `/models`, `/inference-runs`, `/findings`, `/references`, `/reports`, `/reviews`, `/audit-events`, `/integrations`, `/health`.  
**Entities:** organizations, facilities, users, roles, studies, series, instances, processing_jobs, model_versions, inference_runs, finding_scores, threshold_sets, report_versions, review_signatures, reference_documents, retrieval_events, audit_events, integration_endpoints, data_retention_policies.  
**Workflow:** received -> validated -> queued -> processing -> draft_ready -> in_review -> signed OR rejected/failed/unsupported. Signatures are identity-bound and auditable; never just a checkbox.  
**Error contract:** explicit unsupported study, invalid DICOM, failed model, unavailable LLM, incomplete retrieval, timed-out job, insufficient confidence; never silently return "normal".

## 7. Security, privacy and compliance gates

- Incident response: investigate reported `medical.pem` exposure, rotate/revoke, preserve evidence without publishing secrets, clean repository/history with coordination.
- Threat model: PHI leakage, unauthorized access, wrong-patient matching, prompt injection, model tampering, poisoned corpus, insecure DICOM gateway, ransomware, tenant crossover.
- Controls: least privilege, MFA/SSO, immutable audit, TLS/mTLS where needed, encryption at rest, key management, backups, tested restore, patch cadence, network isolation, logging minimization.
- India: assess CDSCO medical-device-software classification/licensing and applicable DPDP obligations with qualified specialists; assess ISO 13485, ISO 14971, IEC 62304, IEC 62366-1, IEC 81001-5-1 and ISO 27001 relevance.
- Quality documents: intended use, requirements traceability, software architecture, risk register/FMEA, verification and validation plan, clinical evaluation plan, usability study, cybersecurity plan, change control, incident reporting.
- Before any hospital trial: ethics/IRB review where applicable, legal basis/data agreements, research protocol, on-prem data handling, clinical oversight, silent-mode safeguards.

## 8. Team, RACI and project management

| Workstream | Accountable | Responsible | Approver |
|---|---|---|---|
| Scope, roadmap, budget | Product Manager | PM + Tech Lead | Sponsor/Clinical Lead |
| Clinical intended use, labeling | Clinical Lead | Radiologists + Data Science | Regulatory/Clinical Lead |
| Classifier selection and validation | Principal AI Engineer | ML Engineers + Clinical Data | Clinical Lead |
| DICOM/PACS interoperability | Platform Lead | Imaging Engineer | Hospital IT |
| APIs, database, workflows | Backend Lead | Backend Engineers | Tech Lead |
| UI, viewer, usability | Frontend Lead | Frontend/UX | Clinical Lead |
| GPU serving, MLOps, CI/CD | Cloud/MLOps Lead | ML Platform + SRE | Security Lead |
| Security and privacy | Security Lead | DevSecOps + Compliance | Security/Legal |
| QA, verification, release | QA Lead | Automation + Validation | Quality/Regulatory |
| Pilot rollout and support | Delivery Manager | SRE + Support | Hospital Sponsor |

**Minimum realistic team:** PM (1), clinical radiologist advisor (fractional or more), AI lead (1), ML engineer (1–2), backend (1–2), frontend (1), imaging/PACS engineer (1), MLOps/DevSecOps (1–2), QA (1), regulatory/quality advisor (fractional). Multiple roles may be shared in early research, but clinical and release approvals need independent review.

**Operating cadence:** weekly backlog grooming and risk review, 2-week sprints, fortnightly architecture decision review, monthly model evaluation board, security incident escalation immediately, milestone signoffs with evidence.  
**Definition of ready:** requirements, safety risk, owner, acceptance criteria, dataset/test fixtures, dependencies, rollback.  
**Definition of done:** code review, unit/integration/security tests, documented metrics, no PHI in logs, deployment/rollback evidence, model version traceability, clinical signoff when applicable.

## 9. Prioritized implementation epics and acceptance gates

### EPIC 0 — Emergency security + truth-in-labeling (P0, weeks 1–2 estimate)
- [ ] SEC-01 investigate and rotate any real exposed private key.
- [ ] SEC-02 add secret/dependency/container scanning CI and protected branches.
- [ ] SAFE-01 remove unsupported "validated clinical" and "approved" claims.
- [ ] TEST-01 snapshot existing behavior, smoke tests, pin dependencies.
**Exit:** incident response documented, no misleading research UI claims, regression tests green.

### EPIC 1 — Model correctness (P0/P1, weeks 2–6)
- [ ] AI-01 reproduce OpenCLIP API output contract with pinned version.
- [ ] AI-02 remove cross-label softmax for multilabel scoring.
- [ ] AI-03 build configurable dataset manifest and patient-level split.
- [ ] AI-04 implement baseline candidates and label mapping.
- [ ] AI-05 produce calibration/threshold config with confidence intervals.
- [ ] AI-06 benchmark inference, CPU fallback, OOD and abstention.
**Exit:** repeatable experiment reports and untouched external evaluation plan; no unsupported accuracy claims.

### EPIC 2 — DICOM, RAG and reports (P1, weeks 4–9)
- [ ] IMG-01 modality LUT, rescale, photometric, VOI and malformed input tests.
- [ ] RAG-01 curated references, versioned retrieval, provenance tests.
- [ ] LLM-01 evaluate MedGemma vs template vs alternative on blinded report benchmark.
- [ ] REP-01 schema-constrained drafts and server-enforced reviewer signature.
**Exit:** reliable de-identified end-to-end workflow with zero automatic signoff.

### EPIC 3 — Hospital workflow MVP (P2, weeks 7–14)
- [ ] API-01 FastAPI, PostgreSQL, Alembic, OIDC/RBAC, study/job APIs.
- [ ] IMG-02 Orthanc gateway and OHIF viewer.
- [ ] JOB-01 queue, retry/DLQ, idempotency and status.
- [ ] UX-01 worklist, image review, report versioning, audit.
- [ ] OPS-01 telemetry, backup/restore, operational runbooks.
**Exit:** controlled research deployment and documented interoperability tests.

### EPIC 4 — Clinical research pilot (months 4–8+)
- [ ] PILOT-01 intended-use and protocol approval.
- [ ] PILOT-02 authorized multi-site dataset and prospective shadow study.
- [ ] PILOT-03 external sensitivity/specificity, calibration and subgroup analysis.
- [ ] PILOT-04 radiologist human factors, edit burden and error investigation.
- [ ] SEC-03 penetration test and recovery exercise.
**Exit:** prespecified pilot endpoints met, clinical safety committee review.

### EPIC 5 — Commercial medical product (timeline depends on evidence)
- [ ] REG-01 device classification, regulatory submissions/licenses as applicable.
- [ ] QMS-01 requirements traceability, risk controls, V&V, change control.
- [ ] BIZ-01 hospital contracts, SLA, support, training, privacy agreements.
- [ ] OPS-02 post-deployment monitoring, drift response, rollback and incident reporting.
**Exit:** authorized intended use and documented release approval.

## 10. Budget and estimation model

Estimate separately: engineering person-months, radiologist annotation/review hours, de-identification and secure data storage, GPUs, model training and serving, PACS integration, regulatory/quality advisors, penetration testing, monitoring, on-prem hardware, maintenance, support.  
**Cost model:** monthly fixed platform + per-study GPU inference + per-report generation + storage/retention + support + security/compliance overhead.  
**No unsupported price or GPU throughput claims.** Run hardware benchmark and vendor quotes before financial commitments.

## 11. Testing and validation matrix

- Unit: OpenCLIP interface, labels, threshold loading, JSON schemas, RBAC, state transitions.
- Integration: DICOM upload -> Orthanc -> queue -> model -> RAG -> draft -> reviewer -> export.
- Data: patient split leakage, duplicate studies, uncertain labels, missing/corrupt tags, burned-in PHI.
- ML: AUROC/AUPRC, calibration, CI, site/device subgroup, OOD, false negatives, multi-label cases.
- LLM: hallucination, negation/laterality, source grounding, refusal, schema invalidity, prompt injection, outage fallback.
- Security: SAST, SCA, secret scan, container scan, authz bypass, IDOR, SSRF, PHI in logs.
- Ops: concurrent jobs, GPU exhaustion, retry/idempotency, failover, restore, upgrade and rollback.
- Human factors: report signing errors, alarm fatigue, overreliance, time to review, clinician feedback.

## 12. Governance artifacts to create

`docs/product/PRD.md`, `docs/architecture/ADR-001-model-separation.md`, `docs/architecture/system-design.md`, `docs/ai/model-selection-matrix.md`, `docs/ai/evaluation-protocol.md`, `docs/ai/model-cards/`, `docs/clinical/intended-use.md`, `docs/clinical/validation-plan.md`, `docs/security/threat-model.md`, `docs/security/incident-response.md`, `docs/quality/risk-register.md`, `docs/quality/requirements-traceability.md`, `docs/ops/deployment-runbook.md`, `docs/ops/backup-restore.md`, `docs/management/RAID-log.md`, `docs/management/release-checklist.md`.

## 13. PR sequencing and change management

This document is **planning-only**. Do not merge it as proof of a completed audit or clinical approval. Follow with separate small PRs:
1. Security incident and safe research labeling.
2. OpenCLIP contract tests and multilabel scoring.
3. Dataset/threshold evaluation reproducibility.
4. DICOM preprocessing and conformance tests.
5. Model comparison harness and benchmark results.
6. Constrained MedGemma/template report evaluation.
7. FastAPI data model, queue and authenticated reviewer workflow.
8. Orthanc/OHIF and hospital integration.
9. MLOps, observability, backups and deployment hardening.
10. Clinical/regulatory evidence artifacts and pilot controls.

## 14. Source references (accessed 2026-10-28)

- Google DeepMind MedGemma overview: https://deepmind.google/models/gemma/medgemma/
- Google MedGemma 1.5 model card: https://developers.google.com/health-ai-developer-foundations/medgemma/model-card
- Google MedGemma 1 model card: https://developers.google.com/health-ai-developer-foundations/medgemma/model-card-v1
- Google CXR Foundation model card (legacy, MedSigLIP recommended): https://developers.google.com/health-ai-developer-foundations/cxr-foundation/model-card
- Google MedGemma research announcement: https://research.google/blog/next-generation-medical-image-interpretation-with-medgemma-15-and-medical-speech-to-text-with-medasr/
- MedGemma paper (Nature Medicine, October 2026): https://www.nature.com/articles/s41591-026-04626-w
- OHIF: https://ohif.org/
- Orthanc: https://www.orthanc-server.com/
- MONAI: https://monai.io/
- TorchXRayVision: https://github.com/mlmed/torchxrayvision
- DICOM standard: https://www.dicomstandard.org/
- HL7 FHIR: https://hl7.org/fhir/
- CDSCO: https://cdsco.gov.in/

**Final decision gate:** The most accurate model cannot be selected from names, parameter counts or generic published benchmarks. It must be selected using the exact intended-use dataset, independent external evaluation, clinical review, safety thresholds, license assessment and deployment benchmarks.
