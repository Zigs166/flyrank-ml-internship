# Content Refresh and Declining Performance Prioritization: A Decision-Support Study

## Abstract

Content teams managing large editorial or organic search catalogs often face declining page performance without a clear, repeatable way to prioritize which pages to refresh first. This capstone study evaluates a structured prioritization workflow for the **Content Refresh / Declining Performance Prioritization** problem on anonymized data from the **FlyRank ML Internship**. We compare a transparent rule-based baseline against a standardized `LogisticRegression` model using group-aware cross-validation (`GroupKFold`) to prevent group leakage. While both approaches achieve high ranking and classification performance on the evaluated dataset, results should be interpreted as directional decision-support signals rather than automated publishing rules. A prioritized queue helps editorial and SEO teams focus limited refresh capacity on high-impact candidates while keeping human review in the loop.

## Introduction and Problem Statement

In ongoing content operations, performance decay across existing pages can dilute organic visibility and waste editorial bandwidth if teams react ad hoc or refresh low-leverage articles first. Within the FlyRank content workflow context, teams need a consistent, auditable method to rank underperforming or declining content for refresh prioritization without exposing sensitive client identities, raw URLs, or private search queries.

This project frames content refresh prioritization as a structured decision-support problem: given content performance and engagement indicators, how effectively can a simple rule baseline and a standardized linear classifier (`LogisticRegression`) rank refresh candidates under group-aware evaluation, and how can those scores be turned into an actionable review queue?

## Data and Public-Safety Governance

This study uses the anonymized internship dataset provided for the FlyRank ML Internship capstone. To maintain strict public safety and privacy standards:

- **No raw client names, proprietary URLs, or private user queries** are included in this report or committed to the repository.
- **No raw or derived CSV datasets** are committed to version control, keeping the repository compliant with automated data-leak checks.
- **Group structure** is respected during validation (`GroupKFold`) so related records do not leak across training and validation folds.

## Methodology

We evaluated two complementary approaches on the same group-aware split so comparisons remain consistent and leak-free:

1. **Rule Baseline:** A transparent heuristic scoring approach that combines key content performance/refresh signals into a baseline priority score.
2. **Logistic Regression (Standardized):** A `LogisticRegression` classifier trained on standardized features within a group-aware validation scheme (`GroupKFold`) to estimate refresh-priority likelihood across content groups.

Evaluation focused on grouped accuracy and grouped ROC-AUC (`work/outputs/ml11_capstone_results.json`), alongside ranking candidates into a practical review queue.

## Results

On the group-aware evaluation split recorded in `work/outputs/ml11_capstone_results.json`:

| Approach | Grouped Accuracy | Grouped ROC-AUC | Role in Workflow |
| --- | --- | --- | --- |
| Rule Baseline | `1.0` | `1.0` | Fast, interpretable baseline benchmark |
| Logistic Regression (Standardized) | `0.9999` | `1.0` | Probabilistic ranking model under `GroupKFold` |

### Key Takeaways

- Both the rule baseline and standardized `LogisticRegression` separate high-priority refresh candidates strongly under group-aware validation.
- A standardized linear model provides smooth probability-style ordering that is easy to inspect, calibrate, and pair with editorial capacity limits.

## Limitations and Honest Framing

Strong offline metrics in a prioritization workflow come with important caveats:

- **Target & Label Definition:** High scores on benchmark splits can reflect strong alignment between input features and how the target label or refresh rule is defined, rather than guaranteed real-world traffic recovery after a content rewrite.
- **Observational & Directional Nature:** Historical performance patterns are observational. Model outputs indicate *relative priority* for human triage—they do not prove causation or guarantee ranking gains on their own.
- **Editorial Context Matters:** Search intent shifts, topic seasonality, brand updates, and on-page quality still require human editorial judgment before publishing changes.

## Ranked Recommendations and Action Playbook

To translate model scores into practical content operations:

1. **Triage Top-Ranked Candidates Weekly:** Review the highest-priority refresh queue first rather than editing pages randomly or solely by age.
2. **Pair Scores with Refresh Effort:** Balance high priority scores against expected editorial effort (quick factual/stats updates vs. full structural rewrites).
3. **Validate Post-Refresh Outcomes:** Track post-update performance over subsequent review cycles as directional feedback, rather than treating initial model scores as final proof of impact.
4. **Keep Human-in-the-Loop Guardrails:** Use automated scoring to narrow down where human writers, editors, and SEO specialists focus their attention.

## Reproducibility

- **Repository:** `https://github.com/Zigs166/flyrank-ml-internship`
- **Executed Capstone Notebook:** `work/notebooks/capstone.ipynb` (including methodology, evaluation, closing demo outline, and shareable summaries)
- **Structured Results Artifact:** `work/outputs/ml11_capstone_results.json`

## Acknowledgments and Data Credit

Built on the **FlyRank ML Internship** dataset ([FlyRank](https://flyrank.ai)). Thank you to the FlyRank ML Internship program and mentors for the problem framing, workflows, and guidance throughout the capstone.
