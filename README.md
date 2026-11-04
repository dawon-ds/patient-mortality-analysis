# Patient Mortality Prediction with MIMIC Data

**Predicting mortality within 120 hours of the last hospital admission using tabular clinical features and chronological event sequences.**

This project processes eight MIMIC clinical CSV tables and the `d_items` dictionary, compares **XGBoost**, **Logistic Regression**, **BERT**, and **RoBERTa**, and analyzes predictive clinical features. The final report documents **1,615 training patients and 390 test patients**.

[Portfolio (Korean)](https://app.notion.com/p/3d568564df5a8106b931ef2eb591c508) · [Portfolio (English)](https://app.notion.com/p/66068564df5a83329dc2012278107120)

## Project Overview

![Patient mortality modeling pipeline](docs/images/project-overview.jpg)

| Item | Description |
| --- | --- |
| Objective | Classify death within 120 hours after the last admission |
| Source | MIMIC clinical tables and `d_items.csv` |
| Training / test patients | 1,615 / 390 |
| Tabular models | XGBoost, Logistic Regression |
| Sequence models | `bert-base-uncased`, `roberta-base` |
| Evaluation | Accuracy, Precision, Recall, F1-score |

## 01. Dataset and Exploratory Analysis

Eight clinical CSVs—`admissions`, `icustays`, `chartevents`, `ingredientevents`, `inputevents`, `outputevents`, `procedureevents`, and `patients`—are joined using `subject_id` and `hadm_id`. `d_items.csv` maps item IDs to labels and categories.

![Clinical table structure](docs/images/final/data-tables.jpg)

The final report compares ICU length of stay: approximately **1.2 days** for patients who died within 120 hours versus **6.5 days** for those surviving beyond 120 hours. Differences in item usage between the groups also informed feature selection. These are descriptive associations, not causal conclusions.

## 02. Tabular Data Processing and Feature Engineering

1. **Data integration:** Join tables using `subject_id` / `hadm_id`; sort admission events by time; consolidate records into JSON and convert them to training and test CSV files.
2. **Feature construction:** Use `d_items` labels/categories to derive label usage, event frequency, mean event duration, and average ICU length of stay.
3. **Feature filtering:** The report lists **5,240 columns** after feature engineering; remove **1,330 columns** with at least 80% missing values and **420 single-value columns**. The report describes the resulting feature set as approximately **3,900 features**.
4. **Target:** `Dead_within_120_hours`. `hospital_expire_flag` and `survival_hours` were excluded from tabular predictors.

| Feature | Meaning |
| --- | --- |
| `gender`, `anchor_age`, `race` | Demographic information |
| `patientweight` | Patient weight |
| `icu_avg_los` | Average ICU length of stay |
| `label_used` | Whether a clinical label was used |
| `label_total_count` | Total number of occurrences of a label |
| `label_avg_time` | Average event duration |
| `Dead_within_120_hours` | Binary outcome (target) |

### Model Selection Rationale

- **XGBoost:** Captures nonlinear relationships and interactions among tabular clinical features.
- **Logistic Regression:** Provides interpretable coefficients to examine associations between predictors and the outcome.
- **BERT and RoBERTa:** Test whether chronological clinical events, serialized as text, can provide useful contextual information for classification.

### Tabular modeling

XGBoost and Logistic Regression were compared using five-fold cross-validated grid search. The report describes a **64% training / 16% validation / 20% test** split.

| Model | Selected hyperparameters |
| --- | --- |
| XGBoost | `n_estimators=100`, `max_depth=3`, `learning_rate=0.2`, `subsample=0.8`, `colsample_bytree=0.8`, `gamma=5` |
| Logistic Regression | `C=0.01`, `max_iter=100`, `penalty=l2`, `solver=lbfgs` |

The report evaluates **324 XGBoost** and **30 Logistic Regression** parameter combinations.

## 03. Clinical Event Sequence Processing

For BERT and RoBERTa, the workflow separately joins clinical events by `hadm_id` and records timestamps as `event_time` alongside `event_type`. Available `itemid`, `value`, and `amount` fields are consolidated; absent fields are set to zero in intermediate representations. Events are ordered by their start times.

Elapsed time is measured from admission. Only events with `0 <= elapsed_time < 120` are retained. For admissions containing 200 or more events, the report describes filtering for informative item IDs with differing usage between outcome groups.

| Sequence field | Description | Model role |
| --- | --- | --- |
| `hospital_expire_flag` | Outcome label used in the sequence workflow | Target |
| `hadm_id` | Unique hospital admission ID | Record grouping |
| `elapsed_time` | Hours since admission | Chronological ordering only |
| `event_type` | Event type (e.g., `admittime`, `charttime`) | Text input |
| `itemid` | Item name mapped via `d_items` | Text input |
| `value` | Measurement / amount, default 0 when absent | Intermediate data, not final text input |

**Final sequence input:** a single sentence formed from `event_type` and the mapped item label, e.g. `admittime_Item_name dischtime_Item_name`.

![Clinical event sequence classification](docs/images/final/event-sequence-model.jpg)

The report lists **6,478 training admissions** and **390 test admissions** (last admission per test patient). Training admissions were split 80:20 into train/validation. Both models used learning rate `2e-5`, batch size `16`, `10` epochs, weight decay `0.01`, maximum sequence length `512`, and F1-based checkpoint selection.

## 04. Results

![Model performance comparison](docs/images/final/final-model-comparison.jpg)

### Validation

| Model | Accuracy | Precision | Recall | F1 |
| --- | ---: | ---: | ---: | ---: |
| XGBoost | 0.7692 | 0.78 | 0.77 | 0.77 |
| Logistic Regression | 0.7282 | 0.74 | 0.73 | 0.72 |
| BERT | 0.9136 | 0.8878 | 0.9136 | 0.8904 |
| RoBERTa | 0.9128 | 0.8332 | 0.9128 | 0.8712 |

### Test

| Model | Accuracy | Precision | Recall | F1 |
| --- | ---: | ---: | ---: | ---: |
| **XGBoost** | **0.7692** | **0.78** | **0.77** | **0.77** |
| Logistic Regression | 0.7282 | 0.74 | 0.73 | 0.72 |
| BERT | 0.7231 | 0.6566 | 0.7231 | 0.6566 |
| RoBERTa | 0.6769 | 0.4582 | 0.6769 | 0.5465 |

XGBoost attained the highest reported test F1 (**0.77**). BERT and RoBERTa had substantially higher validation than test performance. Data sampling and split conventions were not identical across tabular and sequence branches, so this comparison does not isolate model architecture as the sole cause of the difference.

### Key Findings

- **Best test performance:** XGBoost achieved the highest reported Test F1 (**0.77**) across the four models.
- **Validation–test gap:** BERT and RoBERTa reached Validation F1 scores of **0.8904** and **0.8712**, respectively, but Test F1 dropped to **0.6566** and **0.5465**. This highlights the importance of held-out evaluation.
- **Input representation and evaluation conditions:** The final event-text input did not incorporate quantitative measurements or learned elapsed-time embeddings. Differences in admission selection and splitting also limit direct architectural comparisons.

### Model interpretation

![Logistic Regression coefficients](docs/images/final/final-logistic-coefficients.jpg)

**Top three positive Logistic Regression coefficients:**

1. **Medications_Midazolam (Versed)_used_1.0:** Midazolam is used for sedation, anxiety relief, or anesthesia during intensive and emergency care. Its positive coefficient may reflect clinical severity and associated interventions; it does not establish that the medication causes mortality.
2. **Patient Weight:** Body weight can relate to cardiovascular and metabolic status and medication management. A positive model coefficient alone does not determine distinct risks for obesity and low body weight.
3. **Significant Events_Cardiac Arrest_total_count:** Counts recorded cardiac arrest events. Its positive coefficient indicates an association between cardiac-arrest event frequency and the model's predicted mortality outcome.

These coefficients represent associations in this fitted model, not causal effects.

![XGBoost feature importance](docs/images/final/final-xgboost-importance.jpg)

The report highlights **oxygen flow use**, **head-of-bed adjustment count**, **pulse oximetry measurement count**, and **heart-rhythm observation count**. Coefficients and importances describe predictive associations rather than medical causality.

[Detailed results and interpretation](docs/results.md)

## 05. Limitations and Future Work

- Quantitative information such as medication dose and fluid amounts was not fully incorporated into model inputs.
- Tabular modeling focused on the last admission; earlier admissions and infrequent events may provide additional information.
- Sequence modeling did not incorporate `elapsed_time` as a learned temporal embedding or `value` in its final text representation.
- The 512-token input limit restricted long clinical-event sequences; alternative long-context or clinical-pretrained models could be investigated.
- Patient-level validation splits, consistent admission selection across models, and an observation cutoff preceding the outcome horizon would strengthen claims about generalization and early prediction.

## 06. Code and Documentation

| File | Description |
| --- | --- |
| [Tabular modeling](notebooks/final/tabular_modeling.ipynb) | Integration, feature engineering and tabular classification |
| [Sequence preprocessing (train)](notebooks/final/sequence_preprocessing_train.ipynb) | Training-event preparation |
| [Sequence preprocessing (test)](notebooks/final/sequence_preprocessing_test.ipynb) | Test-event preparation |
| [Sequence model training/evaluation](notebooks/final/sequence_training_evaluation.ipynb) | BERT/RoBERTa experiments |
| [Methodology](docs/methodology.md) | Full processing and model setup |
| [Results](docs/results.md) | Results and interpretive notes |
| [Execution](docs/execution.md) | Run order and dependencies |
| [Earlier analysis](docs/earlier-analysis.md) | Separate earlier in-hospital mortality experiments |

```bash
pip install -r requirements.txt
jupyter notebook
```

The earlier in-hospital mortality analysis and its metrics are distinct from this 120-hour modeling comparison.
