# Pregnancy-Wise EHG Recording-Period Classification

This repository contains the Google Colab implementation associated with the manuscript:

> **Pregnancy-Wise Leakage-Controlled Multi-Representation Fusion for Classifying Early- and Later-Recorded Electrohysterographic Recordings Using Wavelet Scalograms**

The study investigates pregnancy-wise leakage-controlled discrimination between **early-recorded** and **later-recorded** electrohysterographic (EHG) recordings in the Induced Cesarean Electrohysterogram DataSet (ICEHG DS). The task is recording-period classification; it is **not** preterm-birth prediction, delivery-outcome prediction, gestational-age estimation, or clinical diagnosis.

## Study overview

- Dataset: ICEHG DS, 126 records from 91 pregnancies (62 early-recorded and 64 later-recorded).
- Input representations: S1, S2, S3, and temporally aligned RGB wavelet scalograms.
- Model: representation-specific CNN–Transformer networks with probability-level fusion.
- Validation: five-outer/three-inner pregnancy-wise nested GroupKFold.
- Leakage control: every record and derived segment from the same pregnancy remains in the same partition.
- Adaptive decisions: ensemble weights, Platt-calibration parameters, and classification thresholds are derived exclusively from inner out-of-fold record-level predictions.
- Evaluation level: segment probabilities are averaged within each EHG record before final analysis.

## Primary result

The proposed weighted RGB+S1+S2+S3 ensemble achieved a pooled raw ROC-AUC of 0.811 (95% pregnancy-wise bootstrap CI: 0.738–0.879). The associated accuracy, sensitivity, specificity, F1-score, and balanced accuracy were 0.706, 0.677, 0.734, 0.694, and 0.706, respectively.

These values describe within-dataset recording-period discrimination under pregnancy-wise held-out evaluation and must not be interpreted as clinical predictive performance.

## Repository contents

- `ICEHG_A2_OldPipeline_Revised_2026.ipynb`: Colab workflow for nested model training, record-level prediction aggregation, representation fusion, calibration, statistical evaluation, architecture ablation, Grad-CAM generation, and computational profiling.
- `requirements.txt`: principal Python dependencies.
- `CITATION.cff`: citation metadata for GitHub and archival releases.
- `.zenodo.json`: metadata prepared for Zenodo archiving.
- `.gitignore`: excludes datasets, scalograms, trained models, prediction outputs, caches, and local environment files.

## Data availability

ICEHG DS is publicly available from PhysioNet:

- https://physionet.org/content/icehg-ds/
- https://doi.org/10.13026/vrvd-mf28

The PhysioNet dataset is not redistributed in this repository. Derived scalogram arrays, trained-model files, prediction files, and other large analysis outputs are also excluded. The notebook expects the following precomputed files in the configured Colab/Google Drive source directory:

- `S1_scalograms_120s.npy`
- `S2_scalograms_120s.npy`
- `S3_scalograms_120s.npy`
- `RGB_scalograms_120s.npy`
- the corresponding segment-metadata file used by the workflow

Users must obtain the source data under the PhysioNet terms and configure the notebook paths for their own Google Drive environment.

## Running the notebook

1. Open the notebook in Google Colab.
2. Select a GPU runtime. The reported experiments used an NVIDIA A100-SXM4-40GB GPU.
3. Place the required precomputed scalogram arrays and segment metadata in a Google Drive directory.
4. Update `SOURCE_DIR` and `OUT_ROOT` in the configuration cell to match your own Drive paths.
5. Run the notebook cells sequentially.
6. Keep `FORCE_RETRAIN = False` when resuming an interrupted run so completed tasks can be reused where supported by the workflow.

The reference environment used TensorFlow 2.20.0, a TensorFlow build reporting CUDA 12.5.1 and cuDNN 9, and 32-bit floating-point computation without mixed precision.

## Computational profile

The complete four-model ensemble comprised 6,289,524 parameters and had a combined stored `.keras` size of 73.47 MB. Across 80 training runs, the mean and median completed epoch counts were 16.51 and 16. On the reference A100 GPU, mean and median record-level ensemble inference times were 454.39 ms and 454.29 ms, excluding model-loading time. These measurements are hardware-specific benchmarks; total training wall-clock time was not recorded.

## Reproducibility notes

- The repository does not contain participant-level data.
- Google Drive paths in the notebook are examples from the original Colab environment and must be changed locally.
- The reported outer-fold performance is not recomputed or altered by the computational-profiling section.
- Exact reproducibility also depends on the source scalograms, metadata reconstruction, software stack, hardware, and deterministic behavior of the underlying deep-learning operations.

## License

The source code in this repository is released under the MIT License. The ICEHG DS remains subject to the terms specified by PhysioNet and its dataset authors.

## Citation

Citation metadata are provided in `CITATION.cff`. A version-specific Zenodo DOI will be added after the first archived release is created.

