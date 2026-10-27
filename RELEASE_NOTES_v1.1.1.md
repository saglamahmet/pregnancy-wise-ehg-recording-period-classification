# v1.1.1 — Exact pregnancy-wise fold assignments and reproducibility patch

This version is a documentation and reproducibility update to the original reviewer-rebuild release v1.1.0. **No models were retrained and no reported results were changed.**

## Added

- Original record-identified held-out outer-fold assignments for 126 ICEHG DS records / 91 pregnancies (`reproducibility/v1.1.1/outer_fold_assignments.csv`).
- Explicit assignments of every record to model fit, early stopping, untouched inner evaluation or held-out outer testing, in every one of the 5 × 3 inner folds (`inner_fold_assignments.csv`).
- Record-level assignments for final outer-model fitting and early stopping, separate from the inner OOF process (`final_outer_fit_earlystop_assignments.csv`).
- Matching original fold-composition/three-way audit tables, provenance details and a SHA-256 manifest.

## Verification

- Outer assignments matched the originally saved study fold mapping (126/126 records).
- The same assignments matched the original record-level held-out OOF prediction table (126/126 records).
- All five outer-fold composition audits and all 15 inner-fold fit/early-stop/evaluation audit rows matched the originally saved CLEAN outputs **field for field**.
- Every partition kept pregnancy identities disjoint; no pregnancy-wise overlap was detected.
- The source and export hashes are documented in `reproducibility/v1.1.1/`.

## Environment provenance

- The reference environment now pins PyWavelets to **1.8.0** consistently in `requirements.txt`, `pip_freeze.txt`, and `environment_versions.json` on this release.
- The historical v1.1.0 archive contained a conflicting pip-freeze entry reporting 1.10.0. That archived historical discrepancy is retained; the v1.1.1 metadata correction is documented rather than presented as an untouched original pip-freeze snapshot.

## Unchanged scientific scope and results

- The authoritative notebook remains `ICEHG_FINAL_REVIEWER_REBUILD_CLEAN_v3_1_PLATT_FALLBACK.ipynb`.
- Mean held-out outer-fold raw ROC-AUC of the weighted four-stream ensemble: **0.804 ± 0.062** (95% bootstrap CI: 0.725–0.880). Equal-weight fusion: **0.807**.
- The task is early-versus-later recording-period classification within ICEHG DS; this is not validated preterm-birth prediction or a clinical diagnostic tool.
- Raw ICEHG recordings, scalogram arrays, trained-model checkpoints, and full prediction probabilities are not redistributed.

**Archive note:** This document describes the intended v1.1.1 GitHub release. A new Zenodo version-specific DOI is issued only once the release is actually published and ingested.
