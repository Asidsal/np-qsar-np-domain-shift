# QSAR Generalization Failure in Natural Product Chemical Space

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.10](https://img.shields.io/badge/python-3.10-blue.svg)](https://www.python.org/downloads/release/python-3100/)
[![RDKit](https://img.shields.io/badge/RDKit-2025.09-green.svg)](https://www.rdkit.org/)

## Overview

This repository contains the complete computational pipeline for:

> **Structural and Activity Distribution Shifts Contribute Distinctly to QSAR Generalization Failure in Natural Product Chemical Space: A Systematic Benchmarking Study**
>
> Abubakar Siddiq Salihu, Wan Mohd Nuzul Hakimi Wan Salleh
>
> 
### Key Finding

QSAR models trained on synthetic ChEMBL data show a 62% relative MCC degradation under NP holdout conditions. This degradation arises from two distinct contributing mechanisms:

1. **Structural domain shift** - NP compounds are structurally distant from synthetic training data, universally reducing model confidence (Spearman r = 0.41-0.70 between Tanimoto similarity and prediction confidence)
2. **Activity distribution mismatch** - COCONUT-confirmed NPs in ChEMBL cluster below the activity threshold (median pChEMBL 5.63-5.77 vs 6.22-6.48 for synthetic), degrading MCC independently of structural factors

Standard NP holdout benchmarking conflates these two mechanisms. Both must be characterized separately for performance metrics to be interpretable.

---

## Repository Structure

```
np-qsar-np-domain-shift/
├── README.md
├── LICENSE
├── requirements.txt
├── notebooks/
│   ├── 01_data_curation.ipynb       # ChEMBL + COCONUT curation pipeline
│   └── 02_modeling_pipeline.ipynb   # Full modeling and analysis pipeline
├── data/
│   ├── README.md                    # Data sources and reproduction instructions
│   ├── curation_log.txt             # All curation decisions documented
│   ├── dataset_summary.csv          # Compound counts per target and group
│   └── deduplication_report.csv     # Cross-set deduplication statistics
└── results/
    └── README.md                    # Expected outputs after running Notebook 02
```

---

## Targets

| Target | ChEMBL ID | Protein Family | Synthetic n | NP n |
|--------|-----------|---------------|-------------|------|
| Acetylcholinesterase (AChE) | CHEMBL220 | Serine hydrolase | 4,248 | 412 |
| 5-Lipoxygenase (5-LOX) | CHEMBL215 | Iron dioxygenase | 1,724 | 401 |
| Cyclooxygenase-2 (COX-2) | CHEMBL230 | Fatty acid cyclooxygenase | 3,513 | 171 |

---

## Models

| Model | Library | Key Settings |
|-------|---------|-------------|
| Random Forest | scikit-learn | 200 trees, balanced class weights |
| XGBoost | xgboost | 200 estimators, lr=0.05 |
| SVM | scikit-learn | RBF kernel, C=1.0, balanced |
| Feedforward NN | PyTorch | 512-256-128, batch norm, dropout, early stopping |

---

## Requirements

### Data requirements (download separately)

- **ChEMBL 36 SQLite** (~23 GB extracted): [https://ftp.ebi.ac.uk/pub/databases/chembl/ChEMBLdb/releases/chembl_36/](https://ftp.ebi.ac.uk/pub/databases/chembl/ChEMBLdb/releases/chembl_36/)
- **COCONUT 2.0 lite CSV** (~191 MB): [https://coconut.naturalproducts.net/download](https://coconut.naturalproducts.net/download) (select `coconut_csv_lite` - May 2026 release)

### Python packages

```bash
pip install -r requirements.txt
```

See `requirements.txt` for exact version pins.

---

## How to Run

### Step 1: Data curation (local machine, Jupyter)

Notebook 01 runs locally against your ChEMBL SQLite and COCONUT CSV files. It is computationally lightweight (15-30 minutes) but requires the large source files.

1. Clone this repository
2. Download ChEMBL 36 SQLite and COCONUT 2.0 lite CSV (see links above)
3. Open `notebooks/01_data_curation.ipynb` in Jupyter
4. Edit the three file paths in `CONFIG` (Cell 01):
   - `chembl_db`: path to your extracted `chembl_36.db`
   - `coconut_csv`: path to your COCONUT lite CSV
   - `output_dir`: where to save curated CSVs
5. Run all cells in order
6. Upload the output directory to Google Drive: `My Drive/np_qsar_project/data/`

### Step 2: Modeling pipeline (Google Colab, CPU runtime)

Notebook 02 runs on Google Colab free tier (CPU). Runtime: approximately 2-4 hours.

1. Upload `notebooks/02_modeling_pipeline.ipynb` to Google Colab
2. Ensure curated CSVs from Step 1 are in `My Drive/np_qsar_project/data/`
3. Verify `CONFIG['data_dir']` and `CONFIG['output_dir']` match your Drive paths (Cell 01)
4. Runtime > Change runtime type > CPU
5. Run all cells in order
6. All figures and results tables are saved to `My Drive/np_qsar_project/results/`

### Expected outputs

After successful completion of Notebook 02:

**Figures:** Figure1_UMAP_chemical_space.png, Figure2_Tanimoto_distributions.png, Figure3_performance_heatmap.png, Figure4_domain_shift_correlation.png, Figure5_SHAP_comparison.png, FigureS1_pChEMBL_distributions.png, FigureS2_Tanimoto_by_criterion.png

**Tables:** Table1_full_results.csv, Table2_mcc_summary_pivot.csv, Table3_correlation_results.csv, multiseed_results.csv, sensitivity_coconut_only.csv, rebalance_results.csv, tanimoto_by_criterion.csv, reproducibility_report.txt

---

## Curated Data and Results

Curated datasets, trained models, and all generated figures are deposited on this repo:

> **DOI:** [to be assigned upon acceptance]

The deposit includes all 9 curated CSV files (synthetic train, NP test, combined per target) that are not included in this repository due to size and licensing constraints. ChEMBL data is available under CC BY-SA 3.0. COCONUT 2.0 data is available under CC0.

---

## Reproducibility

All random seeds are fixed at 42. Multi-seed stability was confirmed for Random Forest across five seeds (42, 7, 13, 99, 2024); MCC standard deviations were ≤ 0.038 across all targets. Full environment details including package versions are saved to `reproducibility_report.txt` by Notebook 02.

RDKit version: 2025.09.6 (local, Notebook 01) and Colab-installed version (Notebook 02). Minor descriptor count differences between environments (208 vs 217 RDKit 2D descriptors) do not affect results as Morgan fingerprints are the primary descriptor throughout.

---

## Citation

If you use this code or data, please cite:

```bibtex
@article{salihu2026qsar,
  title     = {Structural and Activity Distribution Shifts Contribute Distinctly 
               to {QSAR} Generalization Failure in Natural Product Chemical Space: 
               A Systematic Benchmarking Study},
}
```

---

## Corresponding Authors

**Abubakar Siddiq Salihu**
- Department of Pure and Industrial Chemistry, Umaru Musa Yar'adua University, Katsina, Nigeria
- ORCID: [0000-0002-4425-7524](https://orcid.org/0000-0002-4425-7524)
- Email: abusiddiq.salihu@umyu.edu.ng

---

## License

Code released under the [MIT License](LICENSE).

Data sources: ChEMBL 36 (CC BY-SA 3.0, [https://www.ebi.ac.uk/chembl/](https://www.ebi.ac.uk/chembl/)) and COCONUT 2.0 (CC0, [https://coconut.naturalproducts.net/](https://coconut.naturalproducts.net/)).
