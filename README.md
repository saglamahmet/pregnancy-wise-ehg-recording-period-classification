# Pregnancy-Wise EHG Recording-Period Classification

This repository accompanies the manuscript **“Pregnancy-Wise Leakage-Controlled Multi-Representation Fusion for Classifying Early- and Later-Recorded Electrohysterographic Recordings Using Wavelet Scalograms.”**

**Prediction target:** distinguish early- versus later-recorded EHG observations in ICEHG DS. This is **not** preterm-birth prediction, labor prediction, or a clinically validated diagnostic tool.

## Versions

- **v1.1.1 (published reproducibility patch; version-specific DOI: [10.5281/zenodo.23710237](https://doi.org/10.5281/zenodo.23710237)):** exact original outer folds, all 15 inner fit/early-stop/evaluation assignments, and final outer fit/early-stop assignments are publicly available in [`reproducibility/v1.1.1/`](reproducibility/v1.1.1/); no new model training or performance claims. The same CLEAN notebook remains authoritative.
- **v1.1.0 (reviewer-rebuild):** `ICEHG_FINAL_REVIEWER_REBUILD_CLEAN_v3_1_PLATT_FALLBACK.ipynb` — authoritative code for the revised analyses.
- **v1.0.0 (historical):** `ICEHG_A2_OldPipeline_Revised_2026.ipynb` — preserved for version provenance; **its reported values should not be used for the revised manuscript**.

## Dataset and preprocessing

- **Source:** PhysioNet ICEHG DS v1.0.1, https://physionet.org/content/icehg-ds/ and https://doi.org/10.13026/vrvd-mf28.
- 126 records from 91 pregnancies (62 early, 64 later); 35 pregnancies have both recordings, 56 have one.
- Three bipolar EHG channels S1/S2/S3 sampled at 20 Hz; 0.08–5 Hz filter; synchronized 120-second segments with 60-second stride.
- **Primary CWT:** real Morlet (`morl`), integer scales 1–128, PyWavelets **`method="conv"`**, squared coefficient magnitudes, per-channel/segment min–max mapping to `uint8`, bilinear resize to 224×224, followed by model input scaling to `float32/255`.
- Models: one CNN–SE–Transformer for each S1/S2/S3 and RGB representation. Predictions aggregate from segment to record, then combine at the probability level.
- **Validation:** five outer and three inner pregnancy-disjoint folds. Fit, early-stopping, and untouched inner-evaluation subsets are pregnancy-disjoint; only untouched inner out-of-fold predictions inform fusion weights, calibration, and threshold selection.

## Corrected primary findings

The primary discrimination metric is the **unweighted mean of five held-out outer-fold raw ROC-AUC values**, not ROC-AUC calculated after pooling held-out predictions across folds.

| Metric | Revised estimate |
| --- | ---: |
| Weighted RGB+S1+S2+S3 mean outer-fold ROC-AUC ± SD | **0.804 ± 0.062** |
| 95% fold-stratified pregnancy-cluster bootstrap interval (20,000 replicates) | **0.725–0.880** |
| Secondary pooled raw ROC-AUC | 0.714 |
| Equal-weight four-stream mean outer-fold ROC-AUC | 0.807 |
| Highest standalone stream (S1) mean outer-fold ROC-AUC | 0.806 |
| Accuracy / balanced accuracy | 0.635 / 0.639 |
| Sensitivity / specificity | 0.919 / 0.359 |
| Raw → Platt-calibrated Brier score | 0.227 → 0.222 |
| Raw → Platt-calibrated ECE | 0.136 → 0.120 |

Only one of five prespecified paired tests was statistically significant after Holm adjustment: **weighted four-stream fusion vs standalone hybrid RGB**, difference in mean outer-fold AUC **+0.091** (95% nominal CI 0.026–0.158; Holm-adjusted p = 0.0355). Neither a standalone hybrid-architecture advantage nor superiority of optimized over equal weighting was established.

Supplementary analyses address paired pregnancies, metadata/acquisition confounders, conventional Welch spectral classifiers, CWT frequency and representation sensitivity, repeated random seeds, architecture ablations, and Grad-CAM.

## How to reproduce

1. Launch the **v1.1.0 notebook** in Google Colab with a suitable GPU and a writable Google Drive mount.
2. In configuration cell **2**, set `PROJECT_ROOT` to your output location and `DATA_ROOT` to a raw ICEHG DS directory. The supplied example paths are specific to the study's original Drive and are **not universal locations**.
3. The notebook verifies/downloads the publicly available PhysioNet ICEHG DS v1.0.1 where needed, reconstructs pregnancy identifiers, filters EHG signals, and regenerates scalograms from the source. **The v1.1.0 pipeline does not require the v1.0.0 precomputed `.npy` scalogram inputs.**
4. Execute cells in order. Numerous CNN training configurations, seeds, and sensitivity analyses can be computationally expensive.
5. For a new independent execution, use **a new empty output directory** or explicitly enable rebuild/retrain flags. `FORCE_RETRAIN=False` and cache/resume flags allow completed outputs to be reused within the same project root; do **not** confuse reuse of existing checkpoints with independent replication.
6. Check `07_tables/FINAL_key_results.csv`, `07_tables/FINAL_reproducibility_audit.csv`, output manifests, and accompanying CSV tables. Figures are in `08_figures` and additional checks in named analysis subfolders.

The code isolates **primary** results from exploratory sensitivity variants; no variant or random seed should be selected after inspection of outer-test performance.

## Software environment

A recorded environment snapshot for the revised run lists Python **3.13.16**, TensorFlow **2.21.0**, NumPy **2.1.3**, pandas **2.2.3**, scikit-learn **1.6.1**, WFDB **4.3.1**, and PyWavelets **1.8.0**, with a CUDA 12.5.1/cuDNN 9 TensorFlow build. The current `main` branch reference files `environment_versions.json`, `pip_freeze.txt`, and `requirements.txt` consistently specify **PyWavelets 1.8.0**. The original v1.1.0 archived snapshot contained a conflicting `pip_freeze.txt` entry (1.10.0); that historical archive remains immutable and should not be rewritten as a clean original capture. The v1.1.1 update explicitly documents the metadata correction. Exact bitwise reproduction cannot be claimed solely from environment manifests. GPU behavior and package upgrades may change numerical outputs. See `environment_versions.json`, `pip_freeze.txt`, and `requirements.txt`.

## Availability and citation

The original ICEHG dataset is hosted by PhysioNet and is not redistributed here. The notebook, aggregated result tables, reference environment files, and source metadata are public in this repository. Exact fold assignments (including pregnancy grouping keys and record IDs) are available in `reproducibility/v1.1.1/`. Derived scalograms, model weights/checkpoints, and record-level prediction probabilities are not included in this GitHub source snapshot; see the manuscript's Data Availability Statement for access arrangements.

See CITATION.cff for the software authors, version information, and version-specific DOI. The v1.1.0 archive documents the original corrected CLEAN reviewer-rebuild, while v1.1.1 adds the exact pregnancy-wise fold assignments and related reproducibility documentation (DOI: https://doi.org/10.5281/zenodo.23710237). The v1.1.1 update did not involve model retraining or changes to the reported numerical results.

## License

The code is provided under the repository MIT license. The ICEHG DS retains the terms of its original PhysioNet repository and data authors.
