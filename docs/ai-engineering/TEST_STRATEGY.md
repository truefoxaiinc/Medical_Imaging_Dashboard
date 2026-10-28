# ScanFlow AI — Test Strategy

Status: Proposed verification framework; GOV-001 does not execute runtime tests.

## Order
1. Cheap scoped checks: Markdown references, changed-file list, diff review.
2. Python unit and regression checks for modified behavior.
3. Integration/contract tests for interfaces, authorization and failure handling.
4. DICOM golden synthetic fixtures and preprocessing fidelity tests.
5. Versioned model evaluation using licensed, patient/site-separated datasets; calibration, uncertainty, subgroup and external evaluation where appropriate.
6. RAG/LLM grounding, provenance, negation, unsupported claims and deterministic fallback tests.
7. Security scans, privacy-safe logging, load/GPU resource, resilience, backup and restore checks.
8. Staging smoke, rollback rehearsal and human release sign-off.

## Minimum evidence by change
| Change | Required verification |
|---|---|
| Documentation | References, scope and diff review; explicitly report runtime tests not run |
| Python | Targeted unit/regression and affected integration |
| API/data schema | Contract/authz/error and migration compatibility tests |
| DICOM | Golden fixtures, metadata/pixel fidelity, unsupported cases |
| Model/threshold | Frozen versions, independent evaluation, clinical review |
| Infrastructure | Isolated config/staging, resource and rollback checks |
| Clinical claims | Independent clinical and regulatory approval, not CI alone |

## Evidence template
Task; base SHA; command; tool versions; synthetic fixture provenance; PASS/FAIL/NOT RUN/BLOCKED; exit code; sanitized evidence; limitations; reviewer.

Never convert NOT RUN into PASS. Do not suppress failing checks. Clinical validity is not established by engineering tests.
