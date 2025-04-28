# Predicting TCR–Neoantigen Binders Using Machine Learning

---

## Project Overview

This project applies machine learning (ML) to single-cell multi-omics data to **identify true TCR–neoantigen binding events** from a highly noisy background.

Using careful feature engineering and supervised learning (XGBoost classifier), the model amplifies true binding signals while suppressing background noise — helping prioritize true TCR–pMHC interactions for downstream validation.

**Problem**: Noisy dextramer binding data, weak or absent labels, high dimensionality  
**Goal**: Find true binder pairs with minimal ground truth available  
**Approach**: Self-supervised and supervised ML pipelines (Isolation Forest, XGBoost)

---

## Motivation

Identifying true TCR–pMHC binding pairs is critical for neoantigen discovery and immunotherapy development.  
However, single-cell dextramer datasets are **noisy**, due to:

- Background dextramer "stickiness"
- Multiple dextramer signals per cell
- Sparse true binders among abundant noise

This project demonstrates how **machine learning + biological feature engineering** can dramatically **improve binder identification** in real-world noisy data.

---

## ⚙️ Methods

- **Data Integration**:
  - Dextramer counts
  - CITE-Seq hashing
  - Gene expression
  - TCR sequencing

- **Feature Engineering**:
  - Dextramer normalized fold-change (`dex_norm`)
  - Dextramer signal enrichment within Dex⁺ cells (`dex_enrich`)
  - Dextramer rank and competition margin
  - Clonotype frequency and Dex⁺ enrichment

- **Modeling**:
  - **Isolation Forest** (unsupervised anomaly detection)
  - **XGBoost** regression (predict `dex_norm`) — self-supervised
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

## 📂 Repository Structure

```plaintext
notebooks/
  01_data_preprocessing.ipynb
  02_feature_engineering_and_modeling.ipynb

archived_notebooks/
  (older exploratory notebooks)

models/
  xgb_binder_classifier.pkl

data/
  (user-provided processed .h5ad files, not included)

plots/
  (optional: SHAP, binder ranking visualizations)

README.md
requirements.txt
.gitignore
