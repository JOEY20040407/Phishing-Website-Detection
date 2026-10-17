# Phishing Website Detection Using URL and HTML Features

**A Practical Multi-Modal Machine Learning Baseline**  
BSc (Hons) dissertation project · University of the West of Scotland · 2025/26

## Overview

Phishing websites often imitate legitimate services to steal credentials and other sensitive information. URL-only filters are lightweight, but a convincing phishing page may use an unremarkable-looking address. This project investigates whether adding information from the webpage's **HTML/DOM structure** improves detection compared with URL features alone.

The project builds a reproducible, **offline machine-learning pipeline** to compare three feature configurations:

1. **URL-only:** lexical and structural properties of the website address.
2. **HTML-only:** characteristics extracted from preserved page source.
3. **URL + HTML:** combined features from both sources.

Three classical machine-learning classifiers are evaluated in each setting: **Logistic Regression, Random Forest, and XGBoost**. This is an experimental baseline, **not a deployed browser extension or a real-time security product**.

## Key Results

The experiments used **2,000 labelled webpages** (1,000 phishing and 1,000 benign), with an **80/20 stratified train/test split**. The following results are from the **400-sample held-out test set**:

| Feature configuration | Best classifier | Accuracy | ROC-AUC |
| --- | --- | ---: | ---: |
| URL-only (32 features) | Random Forest | 90.25% | 0.9677 |
| HTML-only (33 features) | XGBoost | 94.50% | 0.9887 |
| **URL + HTML (65 features)** | **XGBoost** | **97.50%** | **0.9967** |

For the best combined model:

- **Precision / Recall / F1 (phishing class):** 0.9750 / 0.9750 / 0.9750.
- **Confusion matrix:** 195 benign and 195 phishing pages correctly classified; **5 false positives and 5 false negatives**.
- **Improvement over the best URL-only baseline:** **+7.25 percentage points** in test accuracy (90.25% to 97.50%).
- **Five-fold cross-validation:** mean F1-score of **0.9577**, mean ROC-AUC of **0.9928** for the combined XGBoost model.

These figures describe performance on this controlled offline dataset. They should **not** be interpreted as real-world phishing blocking rates.

## Methodology

**1. Data preparation.** The project uses preserved URL–HTML pairs from the **PhreshPhish** dataset. Data preparation covers label standardisation, removal of unusable records, deduplication and balanced sampling. The final experimental subset contains 2,000 webpages.

**2. Feature engineering.** A total of **65 handcrafted features** are extracted:

- **32 URL features:** URL/host/path length, path depth, subdomain indicators, special-character counts, query parameters, HTTPS use, URL shorteners and suspicious tokens.
- **33 HTML/DOM features:** document size, tag counts, forms and inputs, links and external-link ratios, scripts, embedded elements and structural complexity. HTML is parsed using **BeautifulSoup**.

**3. Model comparison.** Logistic Regression, Random Forest and XGBoost are trained across URL-only, HTML-only and combined-feature experiments. Evaluation includes accuracy, precision, recall, F1, ROC-AUC, confusion matrices and five-fold cross-validation.

**4. Interpretation and error analysis.** Feature-importance analysis and **SHAP** are used to inspect predictive signals. The experiments also examine false positives, false negatives and how changing the classification threshold affects their trade-off.

## Tech Stack

`Python` · `Pandas` · `NumPy` · `scikit-learn` · `XGBoost` · `BeautifulSoup` · `SHAP` · `Matplotlib` · `Jupyter Notebook`

## Repository Files

| File | Purpose |
| --- | --- |
| [`01_clean_000_003.ipynb`](01_clean_000_003.ipynb) | Load, clean, deduplicate and balance the dataset. |
| [`02_url_features.ipynb`](02_url_features.ipynb) | Extract URL features and evaluate URL-only models. |
| [`03_html_features.ipynb`](03_html_features.ipynb) | Parse HTML and evaluate HTML-only models. |
| [`04_combined_features.ipynb`](04_combined_features.ipynb) | Merge feature sets, compare models and analyse the combined results. |

**Suggested reading order:** `01` → `02` → `03` → `04`.

## Data and Reproducibility

The notebooks were developed with Python 3.11 and expect the following source files in the project root:

```text
data/
  raw/
    test_part_000.parquet
    test_part_003.parquet
  processed/
notebooks/   # Optional: notebooks may also be run from the project root
```

The original dataset files are **not included in this repository**. To re-run the workflow, obtain the relevant PhreshPhish data under its applicable access terms, place the two Parquet files at the paths above, and execute the notebooks in order. The processed CSV tables are then generated locally. Depending on your environment, path and dependency adjustments may be required.

## Limitations

- Experiments use a **balanced 2,000-page subset** rather than the full dataset or the natural distribution of real web traffic.
- HTML features come from **stored source snapshots**; dynamic JavaScript-rendered behaviour is not fully captured.
- Results are based on **offline evaluation**. In-browser latency, live-page collection, operational false-positive rates and deployment performance were not tested.

## Future Work

Potential next steps include evaluating on a larger and more representative sample, testing robustness on newer or unseen domains, improving reproducibility, and exploring lightweight browser-side deployment.

---

*Academic research project. The reported scores are experimental results, not guarantees of protection against live phishing attacks.*
