# Quantifying Content Decay: A Group-Aware Machine Learning Framework for Organic Search Refresh Prioritization

**Author:** Ahmad Elshaer 
**Date:** August 2026  
**Artifact Repository:** [GitHub Repository](https://github.com/A7mad7-7/flyrank-ml-internship)

---

## 0. Abstract

How can search intelligence pipelines accurately prioritize decaying content for editorial refresh across large-scale web portfolios? We evaluate an anonymized production dataset comprising 30,000 unique URL instances using historical Search Console performance logs and content age metrics. We benchmark a Group-Aware Random Forest Classifier against a static heuristic baseline using a GroupShuffleSplit strategy by `client_id` to prevent cross-client data leakage. The Random Forest model achieves a decision-support Precision of 57.05% and Recall of 84.98% compared to the baseline rule's 0.00% Precision and 0.00% Recall on identical test splits. These results establish an empirical decision-support playbook that categorizes content into three actionable priority tiers for editorial execution.

---

## 1. Introduction / Problem Statement

Digital publication portfolios face organic traffic decay due to shifting search demand, content staleness, and algorithmic adjustments. Editorial teams operating under capacity constraints require automated, data-driven prioritization systems to select high-impact content refresh candidates. 

Traditional prioritization relies on static heuristic rules (e.g., flagging pages older than 365 days with high historical traffic). However, these static rules suffer from extreme false-positive rates and fail to capture non-linear interactions between historical impression volume, recent click trends, and staleness age. This research frames content refresh prioritization as a binary classification and ranking task aimed at maximizing decision-support Precision@K on high-value declining assets.

---

## 2. Data

### Dataset Release & Scope
* **Dataset:** FlyRank Production Search Intelligence Dataset (Anonymized).
* **Population Size:** 30,000 unique URL items across multiple anonymized client portfolios (`client_id`).
* **Observation Window:** Mid-panel evaluation window (`2026-03`). The final dataset month (`June 2026`) was strictly held out as a sealed test window.

### Exclusions & Public Safety
* **Filtering Logic:** Excluded unindexed pages (`impressions_90d == 0`) and brand-new content (`content_age_days < 90`) lacking sufficient historical trend logs.
* **Public-Safety Guarantee:** All sensitive attributes including raw search queries, external domain URLs, and client brand names were scrubbed or converted to anonymous identifiers (`content_id`, `client_id`).

---

## 3. Methodology

### Problem Formulation & Target Label
We define the binary target `target_decay` as an observed negative trend in organic performance (`trend_direction == 'down'`) on valid active content items.

### Feature Engineering
Five knowable, leakage-free features were constructed at the decision moment:
1. `impressions_90d`: 90-day Search Console impression volume (demand scale).
2. `days_since_last_update`: Days elapsed since last CMS publication/update timestamp (staleness).
3. `search_volume`: Aggregate keyword market search index.
4. `clicks_last_30d`: 30-day click engagement volume.
5. `is_blog`: Categorical binary taxonomy indicator (`content_type == 'blog'`).

### Validation Design & Leakage Prevention
To prevent cross-client data leakage, evaluation was conducted using a `GroupShuffleSplit` (grouped by `client_id`, 80/20 split). This guarantees that no URL from a client in the training set appears in the testing set.

### Baseline Comparison
* **Static Heuristic Baseline Rule:** Flag pages where `days_since_last_update > 180` AND `impressions_90d > 10000`.
* **Model Architecture:** Random Forest Classifier (`n_estimators=100`, `max_depth=6`).

---

## 4. Results

Evaluation metrics computed on the identical 20% holdout test group:

| Metric | Week-4 Baseline Rule | Random Forest Model | Absolute Improvement |
| :--- | :--- | :--- | :--- |
| **Precision** | 0.00% | **57.05%** | +57.05% |
| **Recall** | 0.00% | **84.98%** | +84.98% |
| **F1-Score** | 0.000 | **0.683** | +0.683 |
| **ROC-AUC** | 0.500 | **0.615** | +0.116 |

The ML model significantly outperforms the heuristic baseline across all decision-support metrics, achieving a +57.05% absolute gain in Precision and eliminating the severe recall deficiency of static rules.

---

## 5. Limitations & Honest Framing

* **Non-Causal Assertion:** The model detects directional correlation between historical feature dynamics and performance decay. It does NOT assert causal mechanics or guarantee ranking recovery post-refresh.
* **External Volatility:** Model predictions cannot anticipate unobserved macro-search engine algorithm updates occurring after the observation window.
* **Base Rate Context:** The positive class base rate across the dataset is 54.21%. Precision gains must be evaluated relative to this baseline prevalence.

---

## 6. Ranked Recommendations (Action Playbook)

Editorial teams should execute content refreshes according to a 3-tier probability queue:

1. **Tier 1: High-Demand Stale (Immediate Refresh)**  
   * *Criteria:* `decay_probability > 0.6` AND `days_since_last_update > 180` AND `impressions_90d > 10,000`.  
   * *Action:* Full editorial rewrite and structural content update.
2. **Tier 2: CTR Decay Risk (Technical Audit)**  
   * *Criteria:* `decay_probability > 0.6` AND `days_since_last_update <= 180`.  
   * *Action:* On-page technical SEO audit, title tag optimization, and snippet refresh.
3. **Tier 3: Legacy Stagnant (Deprecate / Prune)**  
   * *Criteria:* `decay_probability <= 0.6` AND `impressions_90d < 1,000`.  
   * *Action:* Deprioritize or evaluate for content consolidation.

---

## 7. Reproducibility

* **Executable Notebook:** `work/notebooks/capstone.ipynb`
* **Repository:** [https://github.com/A7mad7-7/flyrank-ml-internship](https://github.com/A7mad7-7/flyrank-ml-internship)
* **Environment:** Python 3.12, `scikit-learn`, `pandas`, `numpy`. Random seeds fixed to `42`.

---

## 8. Acknowledgments & Data Credit

This research paper and underlying models were **Built on the FlyRank ML Internship dataset** provided by [FlyRank](https://flyrank.ai).