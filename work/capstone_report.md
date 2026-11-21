# Capstone Report — Content Refresh / Declining Performance Prioritization

- **Author:** Zaigham Raza (`Zigs166`)
- **Lane:** Content Refresh / Declining Performance Prioritization
- **Repo:** `https://github.com/Zigs166/flyrank-ml-internship`
- **Date:** 2026-11-21

## 0. Abstract
Editorial and SEO teams need a structured way to prioritize existing pages for content refresh when search visibility or click-through efficiency softens. Using the anonymized FlyRank ML internship dataset and pre-decision search signals, this work compares a transparent rule-based baseline against a standardized Logistic Regression model under grouped validation (`GroupKFold`) and leakage/claim audits. Across measured splits, the model provides a directional priority score that aligns with low-CTR and ranking-position opportunity signals while maintaining honest error bounds. Rather than making causal promises of traffic recovery, the ranked queue serves as a decision-support aid for human content-refresh review. It is designed to help content reviewers triage refresh candidates systematically alongside human editorial judgment.

## 1. Problem framing
- **Decision supported:** Prioritizing which existing pages to review first for a content refresh cycle.
- **Unit of analysis:** Anonymized candidate content/row records in the internship dataset (never raw client identifiers, URLs, or private queries).
- **Output:** A ranked priority list (`priority_score` and human-readable `reason_code` tags) exported for decision support.
- **Human action:** A content strategist or editor reviews top-ranked items to inspect search intent, content freshness, and snippet/format fit before planning any update.
- **Cost of a wrong call:** Wasting editorial time refreshing pages with low upside or missing genuinely underperforming pages due to noisy or incomplete signals.
- **Why data/ML helps:** Combining multiple measured pre-decision signals into a consistent ranking helps surface patterns that manual spot-checking can miss, while remaining transparent and auditable.

## 2. Data safety
- **Data used:** The anonymized internship dataset (`data/raw/content_refresh_anonymized.csv`, with Colab Secrets `HF_TOKEN` optional and CSV fallback enabled for reproducible runs) and pre-decision numeric/ratio features (`f_volume`, `f_position`, `f_clicks`, `f_ctr_proxy`, `f_inv_pos`).
- **Deliberately excluded columns & leakage controls:** Post-outcome/label-derived fields (such as `trend_direction` or `trend_pct` if present) and any identifier-like fields are excluded from model features so predictions only use information available at decision time. Pseudonymous group buckets are used strictly for grouped validation (`GroupKFold`), never as predictive features.
- **Public-safety check:** No client names, raw URLs, or private queries are included anywhere in `work/`.

## 3. Baseline
- **Transparent baseline:** A weighted rule-based priority score combining normalized low-CTR signal, inverse-position opportunity, and traffic/volume exposure.
- **Fair comparison:** Evaluated on the same held-out evaluation split and metrics as the Week-5 model so improvements or trade-offs are measured on equal footing.
- **Baseline numbers:** Recorded directly from `work/outputs/ml08_model_comparison.json` on the shared test split (`roc_auc`, `accuracy`, and `precision_at_k`).

## 4. Model / analysis
- **Method and why it fits:** A standardized `LogisticRegression` classifier/scorer (`StandardScaler` + `LogisticRegression`) paired with grouped validation (`GroupKFold`). It is interpretable, fast to run reproducibly in Colab, and well-suited for transparent directional ranking on tabular search signals.
- **Exact feature list:** `f_volume`, `f_position`, `f_clicks`, `f_ctr_proxy`, `f_inv_pos` (excluding label-derived columns, future windows, and identifier/group fields).
- **Target / proxy definition:** A binary priority indicator derived strictly from observed pre-decision CTR-proxy and ranking-position opportunity signals for controlled evaluation.

## 5. Evaluation
- **Split design:** Both a standard holdout split and a grouped split (`GroupKFold`) were evaluated so related records stay together and do not leak across train and test folds.
- **Model vs. baseline:** Compared on the exact same test set using ROC-AUC, accuracy, and top-K precision (`work/outputs/ml08_model_comparison.json`), alongside before/after grouped validation results (`work/outputs/ml09_validation_audit.json`).
- **Error analysis:** Mismatches cluster near decision thresholds and low-volume/noisy rows where CTR and position signals are ambiguous, showing why thresholds require human context rather than blind automation.

## 6. Interpretation
- **What the model found:** Standardized coefficients show that relative CTR opportunity (`f_ctr_proxy`) and ranking position (`f_inv_pos`) carry primary weight in separating higher-priority refresh candidates from lower-priority ones in the observed data.
- **Surprises and limitations:** High scores on low-volume or noisy rows can look prominent numerically even when editorial impact is uncertain; a modest or nuanced gap between rule and model scores is a valid, honest finding rather than something to force.

## 7. Recommendation
- **Ranked actions for a FlyRank editor tomorrow:** Start with top-ranked candidates in `work/outputs/ml10_action_queue.csv`, read the attached `reason_code` (`LOW_CTR_PROXY`, `RANK_POSITION_OPPORTUNITY`, `HIGH_VOLUME_SIGNAL`), and review the top queue in batches.
- **Human review & no-go boundaries:** Always check search intent, factual accuracy, and SERP format before editing; never auto-publish changes from a score alone or expose private client/query data.
- **Confidence and limits:** Treat outputs as directional decision-support signals observed in historical data, not guaranteed causal traffic lift.

## 8. Reproducibility
- **How to re-run from a fresh clone:** Open the notebooks in Colab or run them top-to-bottom (`Runtime -> Run all`) in order (`w04_baseline_score.ipynb` -> `w05_model.ipynb` -> `w06_validation_audit.ipynb` -> `w07_action_playbook.ipynb` -> `capstone.ipynb`) with fixed random seeds (`random_state=42`).
- **Environment & artifacts:** Standard Python data stack (`numpy`, `pandas`, `scikit-learn`, `matplotlib`); key outputs live in `work/outputs/` (`ml08_model_comparison.json`, `ml09_validation_audit.json`, `ml10_action_queue.csv`, `ml10_playbook_summary.json`, `ml10_top_queue.png`).

## 9. Acknowledgments & data credit
Built on the FlyRank ML Internship dataset ([https://flyrank.ai](https://flyrank.ai)).
