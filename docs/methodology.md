# Final Methodology

## Task and Data

The final presentation defines mortality within 120 hours of admission as the target. Clinical tables are integrated by patient and admission IDs, with dictionaries mapping item IDs to categories and labels. Events are sorted chronologically and transformed through JSON into structured CSV records.

## Tabular Models

Features include demographics, weight, average ICU stay, label-use indicators, event counts, and average durations. The report describes removing columns with over 80% missing values and constant columns, and excluding hospital_expire_flag and survival_hours from predictors. XGBoost and logistic regression use grid search and five-fold cross-validation.

## Sequence Models

BERT and RoBERTa classify text formed by concatenating event types and item labels. The report uses 6,478 training admissions and each test patient's last admission (390 records). The training data is split 80:20 for validation. Learning rate is 2e-5, batch size 16, epochs 10, weight decay 0.01, and maximum sequence length 512 tokens.

Elapsed time is used to order events, not as a learned time embedding. Numerical values are not fully represented in model input.

## Evaluation Boundaries

The source describes events within the outcome horizon, so an earlier prediction cutoff must be specified before claiming prospective early-warning performance. Patient overlap across sequence training and validation needs verification. Final experiment notebooks are now available in notebooks/final. The sequence code splits text/label records rather than explicitly grouping admissions by patient.

## Earlier Analysis

[Earlier analysis documentation](earlier-analysis.md) describes the earlier in-hospital mortality notebook, its 70:30 split, SMOTE, and outcome-informed thresholds computed before splitting. This is distinct from the final 120-hour model comparison.
