# Data Access and Confidentiality

This document explains what data was used in this project, what is and is not shareable, and how to request access if you are a Maersk employee or authorised researcher.

---

## Source Data (Confidential — Do Not Upload)

Three datasets were provided by Maersk under a Non-Disclosure Agreement as part of the CMU Tepper MSBA capstone engagement. **None of these files are included in this repository and must not be added to it under any circumstances**, including if the repository is private.

| File | Contents | Rows | Columns | Period |
|------|----------|------|---------|--------|
| `Master_List_of_depots.csv` | Site attributes: ID, name, city, country, region, area, status, operation type, site area (m²), TEU/FFE capacity, products offered, DAPS breakdown, transport connectivity, bonded yard status | 50 | 17 | Static |
| `Revenue_2023_2025.csv` | Monthly revenue per depot | 50 | 38 | Jan 2023 – Dec 2025 |
| `GP_2024_2025.csv` | Monthly gross profit per depot | 50 | 26 | Jan 2024 – Dec 2025 |

**Why they are not shareable:** These files contain commercially sensitive operational and financial data for Maersk's global inland depot network. Disclosure would reveal site-level revenue and profitability figures, capacity details, and service configurations across 28 countries — all of which constitute confidential business information under the terms of the NDA signed between CMU and Maersk.

---

## What Is Shareable

The following outputs were derived from the source data and cleared for inclusion in this repository. They contain aggregated or transformed information that does not directly expose site-level raw financials in a recoverable form.

| File | Location | Contents | Notes |
|------|----------|----------|-------|
| `Depot_Segmentation_KMeans_Results.csv` | `results/` | All 50 depots with segment labels, derived features, and computed metrics | Includes site IDs, cities, countries, and segment assignments. Does not include raw monthly revenue or GP rows. |
| `Segment_Summary.csv` | `results/` | Per-segment averages (revenue, margin, TEU, site area, product count) | Aggregated — no site-level financials |
| `RF_Feature_Importance.csv` | `results/` | Random Forest feature importance percentages | Model output only |
| `SHAP_Importance.csv` | `results/` | Global and per-segment SHAP values | Model output only |
| `Driver_Correlations.csv` | `results/` | Spearman correlation table (features vs revenue and margin) | Statistical output only |

**If you are unsure whether a derived output is shareable, err on the side of caution and consult your CMU faculty advisor or the Maersk sponsor contact before uploading.**

---

## Reproducing the Analysis

To run the full pipeline from scratch you need the three source CSVs listed above. There are two ways to obtain them:

**If you are a Maersk employee or authorised collaborator:**
Contact Maersk Global Network Operations through your internal channel. Reference the CMU Tepper MSBA Capstone 2025 project. The data was provided to the student team by the sponsor and the original data transfer should be on record.

**If you are a CMU faculty member or researcher:**
Contact the Tepper MSBA programme office. The capstone agreement includes provisions for faculty access to sponsor data for academic supervision purposes.

**If you are a third party:**
You do not have authorised access to the source data. You can still reproduce the methodology using the pre-computed outputs in `results/` and inspect the full notebook logic, which is available without the data. You can also substitute equivalent anonymised depot data in the same schema to test the pipeline.

---

## Schema for Substituted Data

If you want to test the pipeline with your own data, your input files must conform to the following schemas.

### Master_List_of_depots.csv

| Column | Type | Notes |
|--------|------|-------|
| `Site_ID` | string | Unique depot identifier (primary key across all files) |
| `Site_Name` | string | Full depot name |
| `City` | string | |
| `Country` | string | |
| `Region` | string | One of 5 Maersk regional groupings |
| `Area` | string | Area classification |
| `Site_Status` | string | `Active` or other |
| `Operation_Type` | string | Depot operation type |
| `Site_m` | float | Site area in square metres |
| `Max_TEU_Capacity` | float | Maximum TEU throughput capacity |
| `Max_FFE_Capacity` | float | Maximum FFE throughput capacity |
| `Products_Solutions_Offered` | string | Semicolon-hash (`;#`) delimited list of products — **not comma-delimited** |
| `Breakdown_of_DAPS` | string | DAPS capability breakdown — **reefer is stored here, not in Products field** |
| `Connected_service_with` | string | Transport connectivity (includes `Rail`, `Truck`) |
| `Bonded_Depot_Yard` | string | `yes` or blank/other |
| `Revised_Street_Address` | string | Address (not used in analysis) |
| `BuildingType` | string | Building type (not used in analysis) |

### Revenue_2023_2025.csv

| Column | Type | Notes |
|--------|------|-------|
| `Site_ID` | string | Matches master list |
| `Site_Name` | string | |
| `Jan_2023` … `Dec_2025` | float | Monthly revenue in local currency (USD). Missing months use the string `NULL`. |

### GP_2024_2025.csv

| Column | Type | Notes |
|--------|------|-------|
| `Site_ID` | string | Matches master list |
| `Site_Name` | string | |
| `Jan_2024` … `Dec_2025` | float | Monthly gross profit. Missing months use the string `NULL`. |

---

## NDA and IP Summary

- The source data files are proprietary to Maersk. Do not upload, share, email, or post them anywhere, including private repositories, cloud storage, or academic data repositories.
- The code, methodology, and derived outputs in this repository are the intellectual property of the student team, subject to the CMU–Maersk capstone agreement.
- The capstone report is not included in this repository. It contains site-level financial data and is subject to the same confidentiality constraints as the source data. It is available directly from the team or through the CMU Tepper MSBA programme office upon request.
- Any questions about the scope of the NDA should be directed to the CMU Tepper MSBA programme office.
