# Content Refresh Opportunity Scoring: A Baseline and Classification Study

## Abstract

This project explores how content items can be prioritized for review using available content attributes and traffic metrics. A rule-based baseline assigns review-priority scores using signals such as impression declines, content age, update recency, and search-position ranges. A Logistic Regression model was also evaluated on a client-grouped test split to classify an already-observed downward trend category. The model achieved 73.9% accuracy and an F1-score of 0.744 on 6,163 held-out records. Both the reconstructed baseline and Logistic Regression achieved Precision@20 of 1.00 on this test set. These results are exploratory: the target is based on an existing trend category, and the experiment does not demonstrate future prediction or prove that refreshing content improves performance.

## 1. Introduction

Content teams may have many pages to review but limited time and resources. A consistent scoring approach can help identify items that deserve further investigation.

This project studies a content refresh opportunity scoring workflow. It compares a rule-based review-priority baseline with a classification model. The goal is to explore whether the available data can support content-review decisions, not to establish causal effects from refreshing content.

## 2. Research Question

How can observed content and traffic signals be used to rank content items for review, and how does a simple classification model perform when classifying observed downward trends for clients excluded from training?

## 3. Data

The analysis uses an anonymized dataset containing 30,000 records and 44 columns. It includes content attributes, content age and update recency, search metrics, traffic measurements, and an observed trend category. The dataset represents 32 client groups.

The analysis excludes content identifiers and client identifiers from model features. Client identifiers are retained only to form grouped training and test sets. The trend fields `trend_direction` and `trend_pct` are excluded from the model inputs.

## 4. Methodology

### 4.1 Rule-based baseline

The baseline assigns review-priority points using predefined rules. These include observed impression declines, content age, time since the last update, the presence of recent impressions, search-position ranges, and low click-through rate under specified conditions.

The score ranks items for review; it is not a calibrated probability that an item needs refreshing or will improve after an update.

### 4.2 Classification model

Logistic Regression was trained to classify whether the existing `trend_direction` value was `down`. Numerical missing values were imputed using training-set medians and scaled. Categorical missing values were filled using the most frequent training-set category, and categorical variables were one-hot encoded.

The target is an observed trend category. It is not a future-period outcome, so this experiment should be interpreted as classification of existing labels rather than forecasting future declines.

### 4.3 Validation design

A grouped split assigned 23,837 records from 25 clients to training and 6,163 records from seven different clients to testing. There was no client overlap between the two sets. This evaluates performance on clients not seen during training, although the split is not time-aware.

### 4.4 Evaluation metrics

The classification model was evaluated using accuracy, precision, recall, F1-score, and a confusion matrix. The baseline and model rankings were also compared using Precision@20 on the same held-out records.

## 5. Results

### 5.1 Logistic Regression

| Metric | Observed result |
|---|---:|
| Test records | 6,163 |
| Accuracy | 0.739 |
| Precision for the `down` class | 0.745 |
| Recall for the `down` class | 0.742 |
| F1-score for the `down` class | 0.744 |

The confusion matrix was:

| Actual class | Predicted not down | Predicted down |
|---|---:|---:|
| Not down | 2,214 | 800 |
| Down | 811 | 2,338 |

The model correctly classified 2,214 records in the not-down class and 2,338 records in the down class. It produced 800 false positives and 811 false negatives.

### 5.2 Top-20 comparison

| Method | Precision@20 |
|---|---:|
| ML-07 rule-based baseline | 1.00 |
| Logistic Regression | 1.00 |

Both rankings placed 20 records labelled `down` among their respective top 20. This result does not show that one method outperforms the other. The baseline implementation and its ranking rules should be checked against the original notebook before treating the comparison as final.

## 6. Error Analysis

Some false positives had stable or increasing observed trends. Some false negatives had very low recent impression counts. These examples show that traffic volume and content attributes do not perfectly distinguish the observed trend classes.

The fitted model assigned positive coefficients to features including previous-period impressions and total impressions. Recent-period impressions had a large negative coefficient. These coefficients describe associations learned by the model; they do not establish causal relationships.

Because the target itself is derived from observed trend information, traffic features may help reconstruct the existing label. This limits the interpretation of the model as a forecasting system.

## 7. Limitations

- The target represents an already-observed trend, not a future decline.
- The train/test split is grouped by client but is not time-aware.
- The baseline is a rule-based review score, not a probability model.
- Precision@20 is based on only 20 top-ranked records per method and does not establish overall superiority.
- Traffic changes may reflect seasonality, changes in demand, measurement differences, or other factors.
- The analysis does not measure the causal impact of content refreshes.
- Results may not generalize to other datasets or future periods.

## 8. Recommendations

1. Treat high-priority scores as review candidates rather than automatic refresh decisions.
2. Investigate declining impressions alongside recent clicks, search position, content age, and update history.
3. Check whether a decline reflects seasonality, demand changes, or measurement issues before recommending a change.
4. For future forecasting, develop a label based on a genuinely later observation period and validate it with a time-aware split.
5. Evaluate future recommendations against a documented baseline and appropriate ranking metrics.

## 9. Reproducibility

The analysis notebooks and supporting work are maintained in the public project repository:

[FlyRank ML Internship Repository](https://github.com/Pallavibt/flyrank-ml-internship)

The ML-07 baseline and ML-08 modeling notebooks document the scoring rules, classification experiment, evaluation metrics, and error analysis.

## 10. Acknowledgments and Data Credit

This project was completed as part of the FlyRank internship work. Data and project credit: [FlyRank](https://flyrank.ai).

## Conclusion

The rule-based baseline and Logistic Regression provide two approaches to organizing content-review candidates. The classification experiment achieved 73.9% accuracy and a 0.744 F1-score on held-out clients, while both methods achieved 1.00 Precision@20 under the tested label definition. These findings are exploratory and do not establish future predictive performance or the causal value of refreshing content. A future-period target and time-aware evaluation would be needed to assess genuine forecasting performance.
