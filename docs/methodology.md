# Final Methodology

## Task and Data

The project predicts death within **120 hours after the patient's last hospital admission** using eight MIMIC clinical CSV tables and the `d_items` dictionary. The report lists **1,615 training patients / 390 test patients**.

## Tabular Preprocessing

1. Merge source tables by `subject_id` and `hadm_id`.
2. Sort all events within an admission in chronological order; merge records into JSON and export structured train/test CSV files.
3. Map `itemid` to category and label through `d_items.csv`.
4. Engineer `label_used`, `label_total_count`, `label_avg_time`, and `icu_avg_los`, alongside demographics and weight.
5. Both reports state 5,240 constructed columns, 1,330 columns removed for at least 80% missingness, 420 constant columns removed, and approximately 3,900 final features. Simple subtraction is 3,490, so the feature-count discrepancy originates in the reports; the true final dimensionality cannot be established from those reported figures alone.
6. `Dead_within_120_hours` is the tabular target. `hospital_expire_flag` and `survival_hours` are excluded from predictors.

### Tabular Models

XGBoost and Logistic Regression were evaluated using grid search and five-fold cross-validation. The reported train/validation/test split is 64%/16%/20%.

- XGBoost: 324 combinations; selected `n_estimators=100`, `max_depth=3`, `learning_rate=0.2`, `subsample=0.8`, `colsample_bytree=0.8`, `gamma=5`.
- Logistic Regression: 30 combinations; selected `C=0.01`, `max_iter=100`, `penalty=l2`, `solver=lbfgs`.

## Sequence Preprocessing and Modeling

1. Join admission and event tables by `hadm_id`. Map timestamp types (`admittime`, `charttime`, etc.) to `event_type`, storing the start timestamp as `event_time`.
2. Consolidate `itemid`, `value`, and `amount` into intermediate event records; missing attributes are set to zero.
3. Compute `elapsed_time` since admission. The full report states that admissions with `elapsed_time >= 120` have their `hospital_expire_flag` changed to 0; then retain only events within `[0, 120)` hours. This explicitly modifies the original in-hospital mortality label for the sequence experiment.
4. When an admission has at least 200 events, select important `itemid` entries based on differences in usage ratio between outcome groups.
5. Form a text sequence from each `event_type` and mapped `itemid` label.

| Field | Description | Final model role |
| --- | --- | --- |
| `hospital_expire_flag` | Modified in the sequence workflow to represent death before 120 hours | Label |
| `hadm_id` | Hospital admission ID | Grouping |
| `elapsed_time` | Hours after admission | Ordering only |
| `event_type` | Timestamp/event type | Text input |
| `itemid` | Dictionary-mapped clinical item label | Text input |
| `value` | Event value or amount (zero if absent) | Intermediate only |

The report describes **6,478 training admissions** and **390 test admissions** (last admission per test patient). The training data was split 80:20 into train/validation. BERT and RoBERTa were fine-tuned for 10 epochs with learning rate `2e-5`, batch size 16, weight decay `0.01`, maximum 512 input tokens, and best-checkpoint selection by F1.

## Evaluation Boundaries

The 120-hour observation window overlaps the outcome horizon, so earlier prediction cutoffs must be specified for genuine early-warning claims. Patient-level validation grouping and consistent sampling between tabular and sequence branches also merit further investigation.

## Earlier Analysis

[Earlier in-hospital mortality analysis](earlier-analysis.md) is distinct from the final 120-hour model comparison.
