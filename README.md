# Validation-Weighted Deep Ensemble for GERD and Polyp Classification

Code accompanying the manuscript **"Architecture-Dependent Image Enhancement and Validation-Weighted Deep Ensemble Learning for Endoscopic Image Classification"**, submitted to Jurnal RESTI (Computer Vision and Pattern Recognition section).

## Overview

This repository contains the Google Colab notebook used to run the full experiment reported in the manuscript: a 3 x 4 factorial comparison of three backbone architectures across four image enhancement modes, followed by a validation-weighted ensemble that fuses the best configuration selected per backbone.

## Dataset

- **Source:** GastroEndoNet v3 (Bitto et al., 2025), Data in Brief, https://doi.org/10.1016/j.dib.2025.111572
- **Data record:** https://doi.org/10.17632/ffyn828yf4.3
- **Classes and counts:** GERD (974), GERD Normal (1,103), Polyp (779), Polyp Normal (1,150); 4,006 primary images total
- **Split:** 2,802 train / 601 validation / 601 test, fixed and non overlapping by file path and MD5 hash

The dataset is not redistributed in this repository. Download it from the sources above and place the extracted ZIP according to the path set in the notebook's configuration cell.

## Experimental design

Three backbones, ResNet50, EfficientNet-B0, and ConvNeXt-Tiny, are each trained separately on four image representations: raw, sharpened, CLAHE, and gamma corrected. This produces 12 independently trained single-view models under an identical split and training protocol.

**Training settings:** image size 224 x 224, batch size 32, up to 15 epochs, seed 42, AdamW optimizer with learning rate 3e-4 and weight decay 1e-4, label smoothing 0.05, early stopping patience 4.

## Proposed ensemble

One configuration per backbone is selected using validation macro-F1 only. The selected probability vectors are then combined through a validation-weighted multi-enhancement ensemble:

| Component | Weight |
|---|---|
| ResNet50 + sharpen | 0.30 |
| EfficientNet-B0 + CLAHE | 0.25 |
| ConvNeXt-Tiny + CLAHE | 0.45 |

Component selection and fusion weight optimization use only the validation set. The held-out test set is used once, for final evaluation.

## Results

| Metric | Value |
|---|---|
| Validation macro-F1 | 0.9039 |
| Test accuracy | 0.8918 |
| Test macro-F1 | 0.8935 |
| ROC-AUC | 0.9785 |
| PR-AUC | 0.9458 |
| Log loss | 0.3373 |

McNemar exact test against the best single-view configuration: p = 0.0192.

## How to run

1. Open `gerd-polyp-validation-weighted-ensemble.ipynb` in Google Colab.
2. Mount Google Drive and place the original image ZIP at the path set in the configuration cell (`DRIVE_ZIP_PATH`).
3. Run all cells in order from top to bottom.
4. Set `FAST_RUN = True` for a quick pipeline check on a small subset, or `FAST_RUN = False` to reproduce the manuscript results.

**Environment used for the saved results:** Python 3.12, PyTorch, Google Colab with an NVIDIA T4 GPU. Timing columns may vary across hardware and library versions; classification metrics should be stable.

## Repository contents

- `gerd-polyp-validation-weighted-ensemble.ipynb`: full experiment notebook
- `README.md`: this file
- `LICENSE`: license terms for this code

## Citation

If you use this code, please cite the manuscript above and the dataset:

> Bitto, A. K., Bijoy, M. H. I., Shakil, K. H., et al. (2025). GastroEndoNet: Comprehensive endoscopy image dataset for GERD and polyp detection. Data in Brief, 60, 111572.
