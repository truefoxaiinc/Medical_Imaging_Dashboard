# ScanFlow AI — AI Context Index

Status: Guidance, not a replacement for AGENTS.md.

## Mandatory session bootstrap
Read AGENTS.md, CURRENT_STATE.md, ARCHITECTURE_GUARDRAILS.md, TASK_LEDGER.md, AI_WORKFLOW.md once per new session. Consult ENGINEERING_CONSTITUTION.md and PROJECT_BUILD_RULES.md for relevant policy and quality gates. Do not reread unchanged content repeatedly.

## Task-aware context routing
| Work | Relevant additional context |
|---|---|
| Planning | PROJECT_MASTER_PLAN.md, FEATURE_REGISTRY.md |
| Architecture | DECISION_LOG.md, CONTRACT_REGISTRY.md, impacted code/contracts |
| Inference | requirements.txt, relevant inference module and tests |
| Imaging | targeted loader, synthetic DICOM fixtures and tests |
| Retrieval/reporting | rag.py, llm.py, actual caller and tests |
| Deployment | DEPLOYMENT_RUNBOOK.md, DOCKER.md, relevant Compose files |
| QA | TEST_STRATEGY.md, task acceptance criteria and changed-file diff |
| Security | Approved incident brief and safe metadata; never credential contents |

## Evidence freshness
Capture base SHA, task ID, changed files and source references. Reuse baseline when unaffected. If SHA changes, inspect only affected files and direct consumers. A missing root directory does not prove no tests exist elsewhere.

## Minimal task snapshot
Task ID; base SHA; owner; goal; acceptance criteria; impacted paths; approved ADRs; verified baseline; blockers; next safe action.

## Stop conditions
Missing approval, unsafe data, clinical behavior change, unresolved contract, or untestable high-risk change: stop and request a decision.
