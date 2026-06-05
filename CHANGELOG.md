# Changelog

## [3.14.0] - 2026-06-05

### Added
- **CronJob** — per-cron tuning knobs, all optional with backward-compatible defaults:
  - `backoffLimit` (default `6`) — retries before a Job is marked Failed; set `0` for a single Pod per run
  - `ttlSecondsAfterFinished` — opt-in auto-deletion of finished Jobs (and their Pods)
  - `concurrencyPolicy` (default `Forbid`), `failedJobsHistoryLimit` (default `10`) and `successfulJobsHistoryLimit` (default `3`) are now read from each cron spec instead of being hardcoded (matches what the README already documented)

### Fixed
- **CronJob** — with `restartPolicy: Never` the default `backoffLimit` (6) makes a failing Job spawn up to 7 Pods; combined with `failedJobsHistoryLimit` (10) a continuously failing cron piled up dozens of `Error` Pods that could not be tuned from values. Now controllable per-cron.
- **Cache** — `cache-deployment.yaml` did not set `revisionHistoryLimit`, so it fell back to the Kubernetes default of 10 and accumulated ~10 stale (0-replica) ReplicaSets per service. Pinned to `1` to match the main Deployment template.

### Notes
- Fully backward compatible: with no new keys set, rendered output is byte-for-byte unchanged. Numeric keys use `dig` (not `default`) so `0` is honored (e.g. `backoffLimit: 0`, which `default` would wrongly turn back into `6`).

## [3.13.0] - 2026-02-24

### Added
- **Unit tests** — 9 test suites, 46 tests via helm-unittest
  - deployment, service, httproute, worker, cronjob, pdb, hpa, configmap, cache
- Test coverage for: probe modes, strategies, tolerations, resources, image pull secrets, gateway API routes with URLRewrite filters, HPA/PDB configs, cache Redis stack

### Fixed
- Nothing (test-only release)

### Notes
- Discovered: `nodeSelector` missing from `deployment.yaml` (present in worker/cronjob/job) — tracked as known gap
- Warning: `tolerations: {}` (map) in values.yaml conflicts with list override — cosmetic, no functional impact

## [3.12.1] - 2026-02-23

### Added
- 6 bugfixes (PDB apiVersion, filename typo, jaeger unification, worker serviceAccount, examples)
- 4 named templates in `_helpers.tpl` (envFrom, secretVolumes, secretVolumeMounts, imagePullSecrets)
- 8 new features (worker: configfiles/sharedVolumes/tolerations/cache/extensions; cronjob: imagePullSecrets/resources/configfiles)
- CONTRIBUTING.md, enhanced README, NOTES.txt
- HTTPRoute template for Gateway API
