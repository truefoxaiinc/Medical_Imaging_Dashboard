# ScanFlow AI — Engineering Constitution

**Status:** Proposed mandatory policy after human approval | **Version:** 1.0 | **Date:** 2026-10-28

This document is normative for human and AI contributors once approved. It does not replace tests, code review, clinical evidence, regulatory approval, or security controls. **AI cannot be guaranteed error-free.** Our goal is to prevent unverified or unsafe changes from being merged or deployed.

## Rule levels
- **MUST:** Required; violation blocks merge/release.
- **SHOULD:** Default; deviations require written rationale and reviewer.
- **STOP & ASK:** No implementation until an authorized person explicitly approves.
- **NEVER:** Prohibited behavior; stop immediately.

## 1. Product and safety invariants
1. MUST treat current system as research-only; no diagnostic, triage, treatment or clinical approval claims without validated intended use and required authorization.
2. NEVER send patient data, private DICOM, credentials, keys or production logs to AI tools without explicit lawful approval, controls and data processing terms.
3. MUST keep unsupported study, processing failure, abstention, no-positive-model-finding and clinician-approved result distinct.
4. NEVER let an LLM create new image findings, auto-sign a report or override clinical reviewer decisions.
5. MUST enforce reviewer identity, permissions, report version and immutable audit event on the server.
6. STOP & ASK for any change to medical claims, clinical workflows, diagnostic thresholds, model outputs, intended use, data retention or patient-facing content.

## 2. Architecture invariants
7. MUST follow approved ADRs and bounded contexts; do not invent new architectural layers/services.
8. MUST preserve the current research demo until migration parity and rollback are proven.
9. MUST keep frontend, API, imaging, inference, drafting, review and integration responsibilities separate.
10. MUST route user-facing data access through authorized APIs; no frontend direct DB or unrestricted PACS access.
11. MUST keep inference asynchronous, versioned and idempotent; prevent unbounded GPU work.
12. MUST version API schemas, model/weights, preprocessing, label ontology, score semantics and thresholds.
13. MUST record decision rationale and compatibility impact before changing service boundaries or dependencies.
14. STOP & ASK before database schema changes, public API changes, infrastructure changes, model swaps, library major upgrades or repository-wide refactors.

## 3. AI agent conduct
15. MUST read AGENTS.md and relevant CURRENT_STATE, TASK_LEDGER, ARCHITECTURE_GUARDRAILS and AI_WORKFLOW before code changes.
16. MUST work on one assigned task ID with explicit acceptance criteria; no speculative features.
17. MUST inspect only relevant source/tests; avoid repeated full scans and duplicated implementations.
18. MUST describe baseline behavior and plan before editing; do not claim existing functionality without verification.
19. MUST make minimal reversible diffs and preserve backward compatibility unless explicitly approved.
20. NEVER hide errors, fabricate test results, claim clinical validation, silently weaken security or modify unrelated files.
21. MUST disclose assumptions, missing information, blockers and uncertainty.
22. MUST update checkpoints and reproducible handoff after verified work.

## 4. Code quality
23. MUST use clear module boundaries, typed contracts, explicit errors and consistent naming.
24. MUST add regression tests for changed behavior; use lint, formatting, static typing and security checks as applicable.
25. MUST handle input validation, timeouts, cancellation, retries, idempotency and structured error responses.
26. MUST avoid dead code, duplicate pipelines, circular imports, implicit global state and hardcoded credentials/paths.
27. SHOULD keep functions cohesive, minimize dependencies and document non-obvious decisions.
28. NEVER suppress failing tests or disable scanners just to pass CI without approved exception.

## 5. Medical imaging and AI/ML
29. MUST validate DICOM SOP class, photometric interpretation, transfer syntax, pixel transforms, orientation and supported exam/view.
30. MUST use licensed and authorized datasets; separate patients/sites across evaluation partitions; prevent leakage.
31. MUST distinguish similarity scores, logits, calibrated probabilities and clinical risk estimates.
32. MUST not treat zero-shot softmax across independent labels as calibrated multi-label probabilities.
33. MUST use frozen, versioned external evaluation before asserting performance; report CIs and subgroup/shift analysis.
34. MUST store per-inference provenance, quality/abstention status and reproducible model artifacts.
35. STOP & ASK before model training on patient data, new weights in production, threshold updates or changes to clinical labeling.
36. MUST evaluate hallucinations, unsupported assertions, negation, laterality and provenance in generated drafts.

## 6. Security, privacy and infrastructure
37. MUST apply least privilege, OIDC/RBAC where relevant, TLS, secrets management, audit and retention rules.
38. MUST sanitize logs and telemetry; use synthetic or authorized de-identified fixtures.
39. MUST scan secrets, dependencies, code and container images; address critical findings before merge/release.
40. MUST avoid host-wide changes to firewall, Docker networks, Virtualmin/Apache, GPU drivers or existing hosted projects.
41. MUST document migrations, backups, restore test, rollback, ownership and blast radius before deployment.
42. STOP & ASK for production access, secret rotation, firewall/DNS changes, PHI handling, destructive commands or permission escalation.
43. NEVER print or commit the reported private key; handle suspected exposure through incident response.

## 7. Git, CI and release
44. MUST use a task branch, focused commits, PR, diff review and required checks.
45. MUST require independent human review for security, medical safety, architecture and production changes.
46. MUST use protected branches and block direct pushes/force pushes to protected release branches.
47. MUST verify test outcomes, artifact hashes and deployment target; never claim deployment without evidence.
48. MUST document rollback and perform staged deployment with smoke checks.
49. NEVER merge a failing build or bypass protection as a routine workaround.
50. MUST keep research, staging and authorized clinical environments isolated.

## 8. Stop conditions
Immediately STOP & ASK if:
- A private key, token, PHI or patient image appears unexpectedly.
- A clinical claim, medical threshold, signed report, or patient-safety behavior may change.
- The requested task conflicts with an ADR or approved requirement.
- Tests cannot be run, test data is missing, or a defect cannot be reproduced reliably.
- A change could affect existing hosted services or data.
- The agent discovers scope creep or cannot explain the diff.
- A requested action would erase data, rewrite Git history, change auth or disable security checks.

## 9. Mandatory task evidence
Each PR MUST contain: task ID, problem and baseline, changed files, acceptance criteria, tests with actual output, security/clinical impact, migration/rollback, unresolved issues, reviewer approvals, and checkpoint updates.

## 10. Exceptions and enforcement
Only authorized human owners can approve exceptions; document reason, expiration, compensating controls and audit trail. Safety/security-critical exceptions must never be silently assumed. Repository policy documents alone are **not executable enforcement**: branch protection, CODEOWNERS, CI tests/scans, protected environments and access controls must be configured and verified separately.
