# ICEHG DS: exact pregnancy-wise split identifiers (CLEAN v3.1)

This package contains the per-record split assignments behind the 126-record/91-pregnancy study.

- `outer_fold_assignments.csv` is copied from the originally saved CLEAN analysis, retaining each record's **outer held-out fold**. A given record is `outer_test` in its numbered fold and `outer_development` in the other four folds.
- `inner_fold_assignments.csv` contains the selected role of **every record** in all 5 × 3 inner partitions: `model_fit`, `early_stopping`, `untouched_inner_evaluation`, or `outer_test_held_out`.
- `final_outer_fit_earlystop_assignments.csv` records roles of every record for final outer model training and untouched testing. These are separate from inner OOF calibration roles.
- `outer_composition_audit_recomputed.csv` and `inner_threeway_audit_recomputed.csv` were compared **field by field** to the saved audit CSVs of the original experiment. All values matched (5 outer, 15 inner).
- `fold_assignment_provenance.json` records the exact source hashes, algorithm and audit results.

**Reproduction note:** The 5-fold and 3-fold `GroupKFold` splits are deterministic with shuffle disabled. For faithful reruns, use the published assignments (not new random splits) and the original notebook. A later scikit-learn runtime was used for reconstruction; the saved outer identity mapping and original 15 inner audits matched exactly.

**Not the raw data:** These tables contain public dataset record IDs, pregnancy grouping keys, label numbers, and model roles. They contain no new clinical measurements or EHG signals.

**Independent check:** Every record, pregnancy, label, and outer-fold combination agrees with the original held-out master OOF predictions (126/126).
