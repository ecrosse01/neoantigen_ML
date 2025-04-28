# Predicting TCR–Neoantigen Binders Using Machine Learning

---

## Project Overview

This project applies machine learning (ML) to single-cell multi-omics data to **identify true TCR–neoantigen binding events** from a highly noisy background.

Using feature engineering and supervised learning (XGBoost classifier), the model amplifies true binding signals while suppressing background noise — helping prioritize true TCR–pMHC interactions for downstream validation.

**Problem**: Noisy dextramer binding data, weak or absent labels, high dimensionality  
**Goal**: Find true binder pairs with minimal ground truth available  
**Approach**: Unsupervised and supervised ML pipelines (Isolation Forest, XGBoost)

---

## Motivation

Identifying true TCR–pMHC binding pairs is critical for neoantigen discovery and immunotherapy development.  
However, single-cell dextramer datasets are **noisy**, due to:

- Background dextramer "stickiness"
- Multiple dextramer signals per cell
- Sparse true binders among abundant noise

This project demonstrates how **machine learning + biological feature engineering** can **improve binder identification** in real-world noisy data.

---

## Methods

- **Data Integration**:
  - Dextramer counts (neoantigen signal)
  - CITE-Seq hashing (Hash + = Dextramer - / Hash - = Dextramer + populations respectively)
  - Gene expression
  - TCR sequencing

- **Feature Engineering**:
  - Dextramer normalized fold-change (`dex_norm`)
  - Dextramer signal enrichment within Dex⁺ cells (`dex_enrich`)
  - Dextramer rank and competition margin
  - Clonotype frequency and Dex⁺ enrichment

- **Modeling**:
  - **Isolation Forest** (unsupervised anomaly detection)
  - **XGBoost** classifier trained on **validated binders** — supervised

- **Interpretability**:
  - SHAP feature attribution analysis
  - Rank comparison against simple normalized signal ranking

---

## Key Results

- **Known true binders** (e.g., `SRSF2_31 ↔ clonotype16`) ranked **#1** by the trained model
- **8.9× score margin** between top hit and second-best under XGBoost model  
  (vs. **1.6× margin** using normalized dextramer signal alone)
- SHAP analysis shows **clonotype enrichment** and **dex_rank** as dominant predictive features

---

## Repository Structure

```plaintext
notebooks/
  Multimodal_integration_processing_steps.ipynb 
  Modular_pipeline.ipynb

archived_notebooks/
  (older exploratory notebooks)

models/
  xgb_cmv_binder_classifier.pkl (XGBoost supervised)
  xgb_binder_model.pkl (XGBoost semi-supervised)
  isolation_forest_model.pkl (Isolation forest unsupervised)

data/
  (user-provided processed .h5ad files, not included)
  feature_matrix_X_a.parquet (Feature matrix for patient sample SRSF2_2)
  feature_matrix_X_b.parquet (Feature matrix for patient sample SRSF2_9)
  feature_matrix_X_c.parquet (Feature matrix for patient sample SRSF2_10)

README.md
requirements.txt
.gitignore

```

## How to Run

1. **Clone this repository:**

    ```bash
    git clone https://github.com/ecrosse01/neoantigen_ML.git
    cd neoantigen_ML
    ```

2. **Install dependencies:**

    ```bash
    pip install -r requirements.txt
    ```

3. **Prepare the data:**

Download the Data

The raw sequencing data is available at **NCBI GEO:GSE268157**:  
**[Download Link](https://www.ncbi.nlm.nih.gov/geo/query/acc.cgi?acc=GSE268157)**

#### Download the following sample files:

| Sample ID    | Description                             |
|--------------|-----------------------------------------|
| GSM8286611   | WJK-2719_SRSF2_2_RNA (SRSF2 Mutant)      |
| GSM8286612   | WJK-2719_SRSF2_2_CITE (SRSF2 Mutant)     |
| GSM8286613   | WJK-2719_SRSF2_2_TCR_VDJ (SRSF2 Mutant)  |
| GSM8286626   | WJK-2859_SRSF2_9_RNA (SRSF2 Mutant)      |
| GSM8286627   | WJK-2859_SRSF2_9_CITE (SRSF2 Mutant)     |
| GSM8286628   | WJK-2859_SRSF2_9_TCR_VDJ (SRSF2 Mutant)  |
| GSM8286629   | WJK-2864_SRSF2_10_RNA (SRSF2 Mutant)     |
| GSM8286630   | WJK-2864_SRSF2_10_CITE (SRSF2 Mutant)    |
| GSM8286631   | WJK-2864_SRSF2_10_TCR_VDJ (SRSF2 Mutant) |

- **SRSF2_2** = CMML Patient 1, 1 year post-transplant 
- **SRSF2_9** = MDS patient 2, 15 months post-transplant
- **SRSF2_10** = MDS patient 2, pre-transplant

Once downloaded, place the data in the `data/` directory:
Make sure they follow the expected structure for the feature extraction scripts.

4. **Run the notebooks in order:**

    - Open and run `notebooks/Multimodal_integration_processing_steps.ipynb` to process the input data.
    - Then run `notebooks/Modular_pipeline.ipynb` to engineer features, train models, and rank predicted binders.

After completing these steps, you will generate binding predictions, SHAP model explanations, and ranked binder candidate lists.

