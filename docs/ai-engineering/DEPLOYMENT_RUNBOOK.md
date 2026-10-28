# ScanFlow AI — Deployment Runbook

Status: Planning-only checklist; not authorization to modify infrastructure.

## Environments
Development uses synthetic fixtures. Staging is isolated and reproducible. Research pilot requires ethics, privacy and security authorization. Clinical deployment remains blocked pending independent approvals.

## Preflight
1. Record release ID, owner, exact commit, immutable artifact digest, target and blast radius.
2. Obtain explicit change authorization and maintenance window.
3. Confirm dependency versions, scans, licenses, capacity and rollback compatibility.
4. Verify backup and restore evidence and that other hosted services remain isolated.
5. Confirm outstanding incident and safety risks have an approved disposition.

## Staged release
Validate configuration without printing secrets. Deploy immutable artifact to isolated staging. Run service health plus synthetic end-to-end checks. Verify access control, error paths, fallback, logs and monitoring. Obtain human go/no-go before authorized research rollout. Observe metrics and rollback on defined triggers.

## Rollback record
Release ID; environment; prior artifact; config; restore evidence; trigger; owner; verified rollback steps; post-rollback checks.

## Operations
Assign incident, alert, patch, certificate, backup, capacity, model-drift and cost owners. No unmeasured SLOs. Consult existing DOCKER.md and PRODUCTION_NVIDIA.md for current research configuration; this document does not prove deployment health.
