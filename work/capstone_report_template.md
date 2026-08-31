# Capstone Research Paper

## Title

**Predicting Positive Content Performance Signals for Search Content Prioritization**

**Author:** Muhammad Faisal
**Project:** FlyRank ML Internship Capstone
**Model:** Logistic Regression
**Dataset:** FlyRank ML Internship dataset

---

## Abstract

This study investigates whether machine learning can identify content items associated with positive performance signals and help prioritize editorial review. The analysis uses the FlyRank ML Internship dataset containing 30,000 anonymized content items across 32 pseudonymous clients. A client-aware train/test split was used to evaluate a Logistic Regression classifier against a transparent CTR baseline using observed search visibility, traffic, click-through, and engagement features. At the selected decision threshold of 0.10, the model achieved an F1 score of 0.2983 and recall of 0.9009, compared with an F1 score of 0.1703 and recall of 0.1535 for the CTR baseline. The resulting ranked recommendations are intended as directional decision support for editorial prioritization rather than as automatic decisions or causal predictions.

---

# 1. Question

## Research Question

Can a machine-learning model identify content items associated with positive performance signals and rank them for editorial review?

## Decision Supported

The analysis supports the following decision:

> Which content items should an editor prioritize for review based on observed search visibility, traffic, click-through, and engagement signals?

## Unit of Analysis

The unit of analysis is an individual content item.

## Human Action

The model output is intended to help an editor prioritize which content items deserve further investigation, monitoring, or review.

---

# 2. Data

## Dataset

The analysis uses the anonymized FlyRank ML Internship dataset.

| Property         |  Value |
| ---------------- | -----: |
| Dataset rows     | 30,000 |
| Dataset columns  |     44 |
| Unique clients   |     32 |
| Training rows    | 23,837 |
| Test rows        |  6,163 |
| Training clients |     25 |
| Test clients     |      7 |
| Client overlap   |      0 |

## Data Safety

No client names, domains, URLs, private search queries, credentials, or other client-identifying information are included in the public paper.

## Model Features

The final Logistic Regression model uses:

* `impressions_90d`
* `clicks_90d`
* `pageviews_90d`
* `sessions_90d`
* `avg_position`
* `ctr`
* `engagement_rate`
* `scroll_rate`
* `ai_traffic_pct`

## Excluded Fields

The following fields were excluded from model training:

* `trend_direction`
* `trend_pct`
* `performance_positive`
* `client_id`
* `content_id`

### Reasons for exclusion

`performance_positive` is the target variable and therefore cannot be used as an input feature.

`client_id` and `content_id` are identifiers rather than predictive variables.

`trend_direction` and `trend_pct` were excluded because they are directly related to the performance outcome and could introduce leakage.

## Client-Aware Split

The dataset was split by client rather than randomly splitting individual content items.

* Training clients: 25
* Test clients: 7
* Client overlap: **0**

This design evaluates whether the model can generalize to clients that were not present during training.

---

# 3. Methodology

## Target Definition

The target variable is:

`performance_positive`

It represents the binary positive-performance outcome defined from the dataset's performance information.

## Baseline

A transparent CTR baseline was used as a reference model.

The baseline predicts a positive outcome according to the selected CTR rule.

### Baseline Performance

| Metric    | CTR Baseline |
| --------- | -----------: |
| Accuracy  |       0.7501 |
| Precision |       0.1911 |
| Recall    |       0.1535 |
| F1        |       0.1703 |

## Machine-Learning Model

The primary model is **Logistic Regression**.

Logistic Regression was selected because it provides a simple and interpretable classification approach suitable for establishing a transparent modeling baseline.

## Decision Threshold

The selected decision threshold was:

**0.10**

Threshold analysis was performed to compare classification performance at different probability thresholds. The 0.10 threshold provided the strongest F1 score among the evaluated thresholds.

## Leakage Check

The final leakage check passed.

```text
Leaked features used by model: set()
Leakage check: PASS
```

No target-derived or identifier fields were included among the model features.

---

# 4. Results

## Model vs Baseline

Both approaches were evaluated on the same held-out test set of 6,163 content items.

| Metric    | CTR Baseline | Logistic Regression |
| --------- | -----------: | ------------------: |
| Accuracy  |       0.7501 |              0.2922 |
| Precision |       0.1911 |              0.1787 |
| Recall    |       0.1535 |          **0.9009** |
| F1        |       0.1703 |          **0.2983** |
| ROC-AUC   |            — |              0.5914 |

## Main Findings

The Logistic Regression model achieved substantially higher recall than the CTR baseline:

**0.9009 vs 0.1535**

The model also achieved a higher F1 score:

**0.2983 vs 0.1703**

However, the model had lower accuracy and slightly lower precision than the baseline.

The ROC-AUC of **0.5914** indicates that the model has limited ranking/discrimination strength and should therefore be interpreted cautiously.

## Error Analysis

| Error Type     | Count |
| -------------- | ----: |
| True Positive  |   927 |
| True Negative  |   874 |
| False Positive | 4,260 |
| False Negative |   102 |

The high number of false positives is an important limitation of the selected threshold. The model favors broad screening and finding potential positive cases, but many surfaced items require human validation.

## Interpretation

The model should be treated as a **prioritization and screening signal**, not as an automatic decision-maker.

The analysis identifies associations between observed signals and the target outcome. It does not demonstrate that any individual feature causes positive content performance.

---

# 5. Limitations

Several limitations should be considered when interpreting the results.

### 5.1 Modest predictive discrimination

The ROC-AUC of 0.5914 indicates limited predictive discrimination.

### 5.2 High false-positive volume

The selected threshold generated 4,260 false positives. This means the model can surface many items that require additional human investigation.

### 5.3 Threshold trade-off

The threshold of 0.10 increases recall but reduces precision. It is therefore appropriate for broad screening rather than automatic classification.

### 5.4 Observational data

The analysis uses observed historical data. It cannot establish causal relationships between features and content performance.

### 5.5 Generalization

The client-aware split improves the evaluation of cross-client generalization, but performance may still differ on future or substantially different datasets.

### 5.6 Decision-support only

The recommendations should be reviewed by a human editor before any content changes are made.

---

# 6. Ranked Recommendations

The model scores were converted into a ranked content-prioritization output.

## Recommendation Summary

| Recommended Action         |     Items |
| -------------------------- | --------: |
| Review search visibility   |     1,726 |
| Monitor                    |     1,710 |
| Review CTR / metadata      |       537 |
| **Total assigned actions** | **3,973** |

The remaining **2,190** test items were not assigned to one of these three action categories by the reported recommendation output.

## Action Playbook

### 1. Review Search Visibility

Investigate:

* Search position
* Search visibility
* Search-intent alignment
* Content structure
* Topical coverage

The model recommendation should trigger investigation rather than automatically require a content update.

### 2. Monitor

Continue monitoring the item's observed performance signals.

Reassess the item after additional evidence becomes available.

### 3. Review CTR / Metadata

Investigate:

* Title
* Meta description
* Search-intent alignment
* Other factors that may affect observed click-through

## Recommended Workflow

**Rank → Review → Act → Monitor → Reassess**

---

# 7. Artifacts the Paper Embeds

The capstone should provide links to the evidence and reproducibility artifacts used to produce the findings.

## Research Paper

Live deployed paper:

`[INSERT DEPLOYED PAPER URL]`

## GitHub Repository

`[INSERT GITHUB REPOSITORY URL]`

## Capstone Notebook

`[INSERT CAPSTONE NOTEBOOK PATH OR URL]`

## Assignment Notebooks

`[INSERT WORK / NOTEBOOK LINKS]`

## Model Evaluation

Include:

* Baseline metrics
* Logistic Regression metrics
* Threshold analysis
* ROC-AUC
* Error analysis
* Confusion/error counts

## Ranked Recommendations

Include:

* Model scores
* Ranking
* Recommendation reason
* Recommended action
* Action counts

## Reproducibility

The repository contains the analysis notebooks and supporting artifacts required to reproduce the reported analysis, subject to the availability and access restrictions of the underlying dataset.

---

# 8. Acknowledgments & Data Credit

Built on the FlyRank ML Internship dataset.

Data source:

**FlyRank**

https://flyrank.ai

This project uses anonymized data and follows the public-safety requirements of the internship. No client names, domains, private queries, credentials, raw exports, or client-identifying information are included.

The findings are presented as measured, directional decision support. They should not be interpreted as causal claims about search-engine algorithms or guaranteed future content performance.

---

# Final Evidence Summary

| Item                | Result               |
| ------------------- | -------------------- |
| Dataset size        | 30,000 rows          |
| Clients             | 32                   |
| Train rows          | 23,837               |
| Test rows           | 6,163                |
| Client overlap      | 0                    |
| Model               | Logistic Regression  |
| Model features      | 9                    |
| Decision threshold  | 0.10                 |
| Leakage check       | PASS                 |
| Model precision     | 0.1787               |
| Model recall        | 0.9009               |
| Model F1            | 0.2983               |
| Model ROC-AUC       | 0.5914               |
| False positives     | 4,260                |
| True positives      | 927                  |
| Recommended actions | 3,973                |
| Deployed paper      | Live on GitHub Pages |
