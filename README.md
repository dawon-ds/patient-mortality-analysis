# Patient Mortality Prediction with MIMIC Data

**Clinical data integration, tabular modeling, and event-sequence classification**

This project integrates MIMIC clinical records at the patient and admission levels to predict **mortality within 120 hours of admission**. Aggregate clinical features and chronological event text were used to compare **XGBoost, logistic regression, BERT, and RoBERTa**, using **1,615 training patients and 390 test patients**.

[Portfolio](https://incredible-march-0ef.notion.site/66068564df5a83329dc2012278107120)

## Project Overview

![Patient mortality modeling pipeline](docs/images/project-overview.jpg)

| Item | Description |
| --- | --- |
| Objective | Predict mortality within 120 hours of admission |
| Data | MIMIC clinical records and item dictionaries |
| Data partition | 1,615 training patients / 390 test patients |
| Models | XGBoost, logistic regression, BERT, RoBERTa |
| Project report | December 1, 2024 |
| Available code | Final tabular modeling, sequence preprocessing, BERT/RoBERTa training and evaluation; earlier exploratory analysis |

## Data Integration

![Clinical table structure](docs/images/final/data-tables.jpg)

Admission, ICU, demographic, medication, procedure, and chart-event tables were joined using `subject_id` and `hadm_id`. Item dictionaries supplied categories and labels. Clinical events were sorted by their timestamps and consolidated into patient/admission records.

## Approach

### Tabular Features and Models

Tabular features included label-use indicators, event counts, average durations, ICU length of stay, and demographics. Highly missing and constant columns were removed from 5,240 initial columns, yielding approximately 3,900 final features. `hospital_expire_flag` and `survival_hours` were excluded as predictors of `Dead_within_120_hours`.

XGBoost and logistic regression were tuned using grid search and five-fold cross-validation. The training partition was split 80:20 for training and validation. Additional tree, SVM, boosting, and KNN comparisons were presented.

### Clinical Event Sequences

![BERT event-sequence classification](docs/images/final/event-sequence-model.jpg)

Chronologically ordered event types and item labels were serialized into text. BERT and RoBERTa were fine-tuned with learning rate `2e-5`, batch size `16`, `10` epochs, and weight decay `0.01`; checkpoints were selected using validation F1.

The sequence experiments used 6,478 training admissions from 1,615 patients and 390 test admissions, using each test patient's last admission. Inputs were limited to 512 tokens. Elapsed time ordered events but was not implemented as a separate time embedding.

## Final Results

| Model | Validation accuracy | Validation F1 | Test accuracy | Test F1 |
| --- | ---: | ---: | ---: | ---: |
| XGBoost | 0.7692 | 0.77 | 0.7692 | 0.77 |
| Logistic regression | 0.7282 | 0.72 | 0.7282 | 0.72 |
| BERT | 0.9136 | 0.8904 | 0.7231 | 0.6566 |
| RoBERTa | 0.9128 | 0.8712 | 0.6769 | 0.5465 |

**XGBoost achieved the strongest test performance among the compared models, with 76.92% accuracy and an F1 score of 0.77.** BERT and RoBERTa showed lower test performance despite high validation scores, highlighting the importance of evaluating generalization to held-out patient data alongside model complexity.

[Detailed experiment results](docs/results.md)

### Model Interpretation

![Final logistic regression coefficients](docs/images/final/final-logistic-coefficients.jpg)

![Final XGBoost feature importance](docs/images/final/final-xgboost-importance.jpg)

## Limitations

- **Define the prediction time:** The observation window extends through the 120-hour outcome horizon. An earlier cutoff is needed to evaluate early prediction.
- **Evaluate by patient group:** Extend record-level sequence splitting to patient-group splitting and check for patient overlap between training and validation.
- **Align comparison conditions:** Use consistent admission selection and partitioning across tabular and sequence experiments.
- **Improve sequence representations:** Incorporate quantitative clinical values and elapsed-time information, and reduce information loss from the 512-token input limit.

## Final Code

| Notebook | Purpose |
| --- | --- |
| [Tabular modeling](notebooks/final/tabular_modeling.ipynb) | Clinical-table integration, 120-hour target construction, EDA, feature processing, XGBoost/logistic regression and comparison models |
| [Sequence training-data preprocessing](notebooks/final/sequence_preprocessing_train.ipynb) | Event timelines, time-window filtering, item mapping, training text preparation |
| [Sequence test-data preprocessing](notebooks/final/sequence_preprocessing_test.ipynb) | Last-admission selection and test text preparation |
| [Sequence training and evaluation](notebooks/final/sequence_training_evaluation.ipynb) | BERT/RoBERTa fine-tuning and evaluation |

[Setup and execution order](docs/execution.md) · [Data security policy](docs/data-security.md)

## Data Security

**Patient-level data is strictly excluded from this public repository.** Clinical CSVs contain sensitive health records, patient/admission identifiers, timestamps, treatments, and outcomes. Even de-identified records remain subject to source access and data-use restrictions; de-identification does not authorize redistribution.

Raw and processed patient CSVs, patient-level JSON/event text, notebook outputs, dataset archives, and locally generated model artifacts are not published. Access to source data must be obtained independently through the authorized provider and used only in an approved private environment. Do not commit data, submit records in issues, or share screenshots containing patient information.

The repository publishes source code and aggregate presentation figures only. All published notebooks have stored outputs, execution counts, attachments, and cell metadata removed. `.gitignore` provides an additional guard against accidental inclusion of clinical data and artifacts.

## Code and Earlier Analysis

The earlier exploratory notebook analyzes **in-hospital mortality**; it does not implement the final 120-hour XGBoost/BERT comparison. The earlier 57.97% accuracy and 0.605 ROC-AUC belong to that exploratory stage.

- [Original exploratory notebook](notebooks/original_analysis.ipynb)
- [Earlier logistic-regression experiment](notebooks/mortality_modeling.ipynb)
- [Earlier analysis documentation](docs/earlier-analysis.md)
- [Final methodology](docs/methodology.md)
- [Final results and limitations](docs/results.md)

```bash
pip install -r requirements.txt
jupyter notebook
```

For the earlier model notebook, place the authorized prepared CSV at `data/testing_h.csv` and run from the `notebooks` directory. The original archive retains Colab paths and original cell order; stored outputs and metadata are removed. This setup runs the earlier experiment only. Final experiment instructions are in [Setup and execution order](docs/execution.md). Patient-level records are excluded.

## Review

The project compared aggregate clinical features with event-text representations. Held-out performance, patient-level partitioning, and a clearly defined observation window were central lessons alongside model complexity.
