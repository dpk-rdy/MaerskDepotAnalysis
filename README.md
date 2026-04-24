# Maersk Depot Segmentation and Profitability Analysis

A three-stage machine-learning pipeline applied to Maersk's global inland container depot network. The pipeline identifies structural depot segments, ranks the operational drivers of GP margin variance, and quantifies revenue uplift from targeted service expansions.

---

## Project Overview

Maersk operates a network of inland container depots offering services including EMR, container storage, stuffing, reefer handling, and container preparation. GP margins across the network span a 20 percentage-point range. This project answers two questions: **what structural factors drive that gap, and where should Maersk invest to close it?**

**Three-stage pipeline:**

| Stage | Method | Output |
|-------|--------|--------|
| 1 | K-Means clustering (k=5) + hierarchical validation | Five structural depot segments (A–E) |
| 2 | Random Forest + SHAP decomposition (global + per-segment) | Ranked GP margin drivers |
| 3 | OLS within-segment regression | Quantified migration pathway revenue uplifts |

**Key findings:**
- Two infrastructure variables (site area 34.5% SHAP, TEU capacity 33.5%) account for 68% of GP margin variance
- Segment E achieves 68.3% GP margin on $4M avg revenue — outperforming Segment A's 52% on $42.2M — via 100% rail connectivity and premium service mix
- C→A and B→A are the only two migration pathways delivering both higher revenue and higher GP margin simultaneously

---

## Team

| Name | Role |
|------|------|
| Deepak Reddy Agasthi Rajendran | Modelling lead |
| Ethan Wong | Data engineering and EDA |
| Mehdi Abbas | SHAP analysis and migration pathways |
| Ziheng Wu | Visualisation and reporting |

**Contact:** For questions about this project, contact the CMU Tepper MSBA programme office or reach out to any team member via the Carnegie Mellon University directory.

**Sponsor:** Maersk Global Network Operations. Sponsor data is subject to NDA — see [`data-access.md`](data-access.md) before attempting to reproduce results.

---

## Repository Structure

```
maersk-depot-segmentation/
│
├── README.md                   # This file
├── data-access.md              # Data availability and NDA guidance
├── requirements.txt            # Python dependencies
│
├── notebooks/
│   └── Maersk_Depot_Analysis.ipynb   # Full executed pipeline (entry point)
│
├── results/
│   ├── Depot_Segmentation_KMeans_Results.csv # All 50 depots with segment labels + metrics
│   ├── Segment_Summary.csv                   # Per-segment summary statistics
│   ├── RF_Feature_Importance.csv             # Random Forest GP margin driver rankings
│   ├── SHAP_Importance.csv                   # SHAP GP margin driver rankings
│   └── Driver_Correlations.csv               # Spearman correlation table
│
└── docs/
    └── data_dictionary.md      # Field definitions for all input and derived columns
```

---

## How to Run

### 1. Clone the repository

```bash
git clone https://github.com/<org>/maersk-depot-segmentation.git
cd maersk-depot-segmentation
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

Python 3.10 is recommended. All stochastic steps use `random_state=42` for full reproducibility.

### 3. Obtain the data

The three source CSV files required to run the notebook are **confidential and subject to NDA**. They are not included in this repository. See [`data-access.md`](data-access.md) for how to request access or use the pre-computed outputs in `results/`.

The required files must be placed in the same directory as the notebook (or a `data/` subdirectory — update the paths in Cell 4 accordingly):

```
Master_List_of_depots.csv      # 50 depots × 17 attributes (static)
Revenue_2023_2025.csv          # Monthly revenue Jan 2023 – Dec 2025
GP_2024_2025.csv               # Monthly gross profit Jan 2024 – Dec 2025
```

### 4. Run the notebook

```bash
jupyter notebook notebooks/Maersk_Depot_Analysis.ipynb
```

Run all cells in order. The notebook is self-contained: each section builds on the previous one and all outputs are generated inline. The final cell (Cell 38) exports `Depot_Segmentation_KMeans_Results.csv` and four companion CSVs to the working directory.

**Estimated runtime:** 2–4 minutes on a standard laptop (the SHAP TreeExplainer step is the slowest).

### 5. Inspect results without source data

If you do not have access to the source CSVs, all key outputs are pre-computed in the `results/` directory. The segment assignments, SHAP rankings, and migration pathway coefficients can be reviewed directly from those files.

---

## Critical Data Notes

Two preprocessing decisions are essential for correct results. Getting either wrong produces silently incorrect outputs:

**1. Reefer capability field:** `Has_Reefer` must be derived from `Breakdown_of_DAPS`, not from `Products_Solutions_Offered`. Reading from the products field produces zero reefer penetration across all 50 depots — a silent error that corrupts both clustering and driver analysis.

**2. Product field separator:** `Products_Solutions_Offered` uses a semicolon-hash (`;#`) separator, not a comma. Standard `str.split(',')` fails silently and undercounts products for every depot.

Both issues are handled correctly in Cell 8 of the notebook. Do not modify the parsing logic without understanding these constraints.

---

## Reproducibility

The full pipeline is deterministic from the raw CSVs with the following fixed seeds:

- `np.random.seed(42)` — set at notebook import
- `KMeans(random_state=42)` — segment assignment
- `PCA(random_state=42)` — visualisation only
- `RandomForestRegressor(random_state=42)` — SHAP model

All library versions are pinned in `requirements.txt`.

---

## License and IP Notice

This repository was produced as part of a sponsored capstone engagement between Carnegie Mellon University (Tepper School of Business) and Maersk. The code and methodology are the intellectual property of the student team, subject to the terms of the CMU–Maersk capstone agreement and any applicable NDA.

**The source data files are proprietary to Maersk and must not be uploaded, shared, or reproduced without explicit written permission from Maersk Global Network Operations.**

See [`data-access.md`](data-access.md) for full guidance on what is and is not shareable.
