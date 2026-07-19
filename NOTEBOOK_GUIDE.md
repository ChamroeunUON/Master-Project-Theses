# DR Model Evaluation Notebooks Guide

This document describes the 4 comprehensive Jupyter notebooks for evaluating DR classification models.

## Overview

All notebooks are **self-contained evaluation tools** that load real predictions from CSV/JSON files. They do NOT perform model training or require PyTorch.

## File Locations

All notebooks are located in: `/sessions/modest-relaxed-shannon/mnt/DrMulti_Binary/`

- **Notebook_13_TwoPhase_Binary_Evaluation.ipynb** - Two-phase binary classification analysis
- **Notebook_14_TwoPhase_Multiclass_Evaluation.ipynb** - Two-phase 5-class DR grading
- **Notebook_15_Ensemble_Binary_Evaluation.ipynb** - K-fold ensemble binary classification
- **Notebook_16_Ensemble_Multiclass_Evaluation.ipynb** - K-fold ensemble 5-class DR grading

## Data Dependencies

All notebooks use the following data files (automatically loaded):

### APTOS Comprehensive Evaluation
- `evaluation/aptos_comprehensive/test_predictions_full.csv` (531 samples)
  - Columns: image_id, dr_grade, binary_label, prob_twophase_noTTA, prob_twophase_TTA, prob_fold_1-5_tta, prob_ensemble_noTTA, prob_ensemble_TTA
- `evaluation/aptos_comprehensive/comprehensive_eval_v2.json`
  - Binary metrics, ordinal thresholds, confidence intervals
- `evaluation/aptos_comprehensive/threshold_sweep_twophase.csv` (201 thresholds)
- `evaluation/aptos_comprehensive/threshold_sweep_ensemble.csv` (201 thresholds)

### K-Fold Comprehensive Evaluation
- `evaluation/kfold_comprehensive/test_predictions_full.csv` (1021 samples)
  - Multi-source: APTOS, IDRiD, Messidor-2
- `models/kfold/kfold_results.csv` (5 folds)
  - Per-fold metrics: AUC, accuracy, sensitivity, specificity, etc.

### IDRiD Cross-Dataset
- `evaluation/idrid_comprehensive/idrid_test_predictions.csv` (306 samples)

## Notebook Descriptions

### Notebook 13: Two-Phase Model — Binary Classification Evaluation

**Purpose:** Evaluate two-phase DR model on binary disease/no-disease classification.

**Key Sections:**
1. Load APTOS test predictions (531 samples)
2. Load comprehensive metrics JSON with CIs
3. ROC curves for no-TTA and TTA variants
4. Precision-recall curves
5. Confusion matrices (3 variants: default threshold, TTA, TTA+optimal)
6. Threshold sweep analysis (sensitivity, specificity, F1, Youden's J)
7. Metrics summary table
8. Subgroup analysis by DR grade (0-4)
9. Bootstrap 95% confidence intervals (2000 samples)
10. Calibration plot and Brier score
11. Error analysis (FP/FN breakdown)

**Outputs:** 8 high-quality PNG figures in `figures/` directory

**Key Metrics:**
- AUC-ROC (no TTA, TTA, TTA+OptThr)
- Accuracy, Sensitivity, Specificity, Precision, F1
- Cohen Kappa, Brier Score
- Bootstrap CIs for AUC, sensitivity, specificity

---

### Notebook 14: Two-Phase Model — Multiclass/Ordinal Evaluation

**Purpose:** Evaluate two-phase model on 5-class DR severity grading (Grades 0-4).

**Key Sections:**
1. Load APTOS predictions with actual DR grades
2. Load ordinal configuration (thresholds from JSON)
3. Apply ordinal thresholds: prob → Grade (0-4)
4. 5×5 confusion matrix (heatmap)
5. Per-class metrics (precision, recall, F1 per grade)
6. Quadratic weighted kappa (QWK) computation
7. Grade distribution (true vs predicted)
8. Probability distributions by true grade (violin/box plots)
9. Per-grade accuracy bar chart
10. Misclassification analysis (which grades confused)
11. Ordinal error distribution (off-by-N analysis)

**Outputs:** 7 PNG figures

**Key Metrics:**
- 5-Class Accuracy: ~59.7%
- Quadratic Weighted Kappa (QWK): ~0.7147
- Per-grade accuracy breakdown
- Error distributions (exact, off-by-1, off-by-2, etc.)

---

### Notebook 15: K-Fold Ensemble — Binary Classification Evaluation

**Purpose:** Comprehensive evaluation of 5-fold ensemble model for binary classification.

**Key Sections:**
1. Load APTOS ensemble predictions (531 samples)
2. Load per-fold metrics from kfold_results.csv
3. Load comprehensive evaluation JSON
4. Per-fold performance chart (AUC, accuracy, sensitivity, specificity)
5. ROC curves (ensemble variants + per-fold comparison)
6. Precision-recall curves
7. Confusion matrices (no-TTA, TTA, TTA+optimal)
8. Threshold sweep analysis
9. Metrics summary table (ensemble + per-fold)
10. Fold agreement analysis (all 5 agree? variance as uncertainty)
11. Multi-source evaluation (APTOS, IDRiD, Messidor-2 from kfold_comprehensive)
12. Bootstrap confidence intervals
13. Calibration plot

**Outputs:** 9 PNG figures

**Key Features:**
- Per-fold performance visualization
- Fold variance as uncertainty measure
- Cross-dataset performance breakdown
- Ensemble uncertainty quantification

---

### Notebook 16: K-Fold Ensemble — Multiclass/Ordinal Evaluation

**Purpose:** Evaluate ensemble model on 5-class DR grading with multi-source analysis.

**Key Sections:**
1. Load APTOS ensemble predictions
2. Load ordinal configurations (ensemble_tta thresholds)
3. Apply ordinal thresholds to ensemble probabilities
4. 5×5 confusion matrix
5. Per-class metrics (precision, recall, F1)
6. Quadratic weighted kappa
7. Grade distribution comparison
8. Probability distributions by grade
9. Per-grade accuracy
10. Multi-source ordinal evaluation (APTOS, IDRiD, Messidor-2)
11. IDRiD cross-dataset evaluation (separate thresholds if available)
12. Misclassification analysis
13. Ordinal error distribution
14. **Ensemble vs Two-Phase comparison** (side-by-side)

**Outputs:** 10 PNG figures

**Key Metrics:**
- Ensemble 5-Class Accuracy: ~71.75%
- Ensemble QWK: ~0.8456
- Per-dataset performance breakdown
- Performance improvement over two-phase model

---

## Notebook Features

### Consistent Elements Across All Notebooks

- **Setup Cell:** Imports, style configuration, figure directory creation
- **Data Loading:** Graceful error handling with try/except
- **Visualizations:**
  - Seaborn style (whitegrid)
  - Consistent color scheme:
    - Two-Phase: Blue (#2196F3)
    - Ensemble: Orange (#FF9800)
    - Per-grade: Colormap (Set1, Set2)
  - High-resolution PNG output (150 dpi)
  - Proper axis labels and legends
- **Markdown Cells:** Clear explanations between code sections
- **Relative Paths:** BASE_DIR = '.' for portability

### Data Handling

All notebooks:
- Load data from relative paths (evaluate from project root)
- Handle missing files gracefully
- Print data summaries and counts
- Validate data integrity

### Output Organization

All figures saved to `figures/` directory with naming convention:
- `13_*` for Notebook 13 (two-phase binary)
- `14_*` for Notebook 14 (two-phase multiclass)
- `15_*` for Notebook 15 (ensemble binary)
- `16_*` for Notebook 16 (ensemble multiclass)

Example outputs:
- `13_roc_curves.png` - ROC curves
- `14_confusion_matrix_5class.png` - 5×5 confusion matrix
- `15_per_fold_performance.png` - Fold comparison
- `16_ensemble_vs_twophase.png` - Model comparison

## Usage Instructions

### Running the Notebooks

1. Navigate to project directory:
   ```bash
   cd /sessions/modest-relaxed-shannon/mnt/DrMulti_Binary/
   ```

2. Launch Jupyter:
   ```bash
   jupyter notebook
   ```

3. Open desired notebook and run all cells (Kernel → Restart & Run All)

### Requirements

- Python 3.7+
- pandas, numpy
- matplotlib, seaborn
- scikit-learn
- json (standard library)

### Output

Each notebook generates:
- **Console output:** Metrics tables, summaries, statistics
- **Figures:** 7-10 PNG files in `figures/` directory
- **All saved with 150 dpi for publication quality**

## Key Insights from Each Model

### Two-Phase Model (Binary)
- **AUC-ROC:** 0.9992 (excellent)
- **Accuracy:** 98.87%
- **Balanced performance:** Sensitivity 0.9886, Specificity 0.9889
- Optimal threshold: 0.1085

### Two-Phase Model (Multiclass)
- **5-Class Accuracy:** 59.7%
- **QWK:** 0.7147 (substantial agreement)
- **Best performance:** Grade 0 (100%), Grade 4 (81%)
- **Challenging:** Grade 2 accuracy only 47%

### Ensemble Model (Binary)
- **AUC-ROC:** 0.9989 (excellent)
- **Accuracy:** 98.87%
- **Consistency:** All 5 folds > 0.998 AUC
- **Fold variance:** 0.0001 std (highly consistent)

### Ensemble Model (Multiclass)
- **5-Class Accuracy:** 71.75% ✓ (12% improvement over two-phase)
- **QWK:** 0.8456 ✓ (substantial improvement)
- **Cross-dataset robustness:** Evaluated on APTOS, IDRiD, Messidor-2
- **Multi-source advantage:** Benefits from diverse training sources

## Summary

These 4 notebooks provide **production-quality evaluation** of DR classification models with:

✓ Comprehensive binary and multiclass evaluation
✓ Threshold optimization and analysis
✓ Bootstrap confidence intervals
✓ Per-fold analysis and uncertainty quantification
✓ Multi-source cross-dataset evaluation
✓ Model comparison (ensemble vs two-phase)
✓ Publication-quality visualizations
✓ Reproducible and self-contained analysis

**Total cells:** 97 code + markdown cells
**Total figures:** 34 PNG outputs
**Data coverage:** 1,858 total test samples across multiple datasets
