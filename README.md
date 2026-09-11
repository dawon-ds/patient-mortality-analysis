# Patient Mortality Factor Analysis

**Clinical Data Visualization & Modeling**

This repository documents a team project that explored clinical factors associated with in-hospital mortality among cardiac-arrest patients through exploratory data analysis, feature engineering, visualization, and classification modeling.

> **Important:** The original dataset contains sensitive real-patient medical information and is **not included** in this public repository.

## Project Overview

- **Course:** Data Analysis Visualization
- **Submission date:** November 26, 2024
- **Task:** Mortality-factor analysis and classification
- **Dataset size:** 2,005 patients
- **Target:** `hospital_expire_flag`
- **Approach:** EDA, feature engineering, logistic regression, result interpretation

## Dataset

The project used clinical data from **2,005 cardiac-arrest patients**:

- **1,147 deceased**
- **858 survived**

The dataset included variables related to:

- demographics,
- vital signs,
- laboratory tests,
- ICU/treatment variables,
- medications and procedures.

Records were merged by patient ID for analysis.

### Data Privacy

The original source data contains sensitive medical information from real patients. For privacy and data-governance reasons:

- no raw patient-level records are published,
- no sample rows derived from the original dataset are included,
- no identifiers or potentially re-identifiable fields are exposed,
- this repository contains only project-level methodology and aggregate findings.

## Analysis Workflow

1. Integrate patient-level clinical tables
2. Inspect missing values and distributions
3. Explore mortality-associated variables
4. Engineer threshold-based features
5. Build a logistic-regression classifier
6. Evaluate predictive performance
7. Interpret variables associated with higher or lower mortality risk

See [`docs/methodology.md`](docs/methodology.md) for details.

## Key Findings

The analysis suggested several variables were associated with mortality risk:

- **Norepinephrine use** appeared more frequently among deceased patients.
- **Kidney dysfunction / AKI-related indicators** showed meaningful association with mortality.
- **Glucose-related variables** also showed notable patterns.
- A derived **metabolic stress** feature combined oxygen-saturation information with extreme mean-glucose values.
- More stable blood-pressure and oxygen-related measurements were associated with lower predicted mortality risk in the fitted model.

These findings are **associational**, not causal.

## Modeling

### Logistic Regression

The main classification model achieved approximately:

- **Accuracy:** 57.97%
- **ROC-AUC:** 0.605
- **Precision for non-survivors:** 0.75
- **Recall for non-survivors:** 0.37

The model was used primarily to support interpretation of mortality-associated factors rather than to claim clinically deployable performance.

### Linear Regression

A linear-regression alternative was also explored as a continuous-score model, but it was not well suited to the binary mortality-classification objective.

See [`docs/results.md`](docs/results.md) for the summarized results and limitations.

## Limitations

Major limitations included:

- class imbalance,
- missing values,
- irrelevant or noisy variables,
- limited model performance,
- restricted clinical context,
- lack of external validation.

Future work would benefit from improved preprocessing, larger and more representative datasets, diagnosis-level context, post-ICU information, and stronger classification models.

## Repository Structure

```text
patient-mortality-analysis/
├── README.md
├── .gitignore
└── docs/
    ├── methodology.md
    └── results.md
```

## Notes

- This is a **report-centered portfolio repository**.
- The original project code is no longer available, so no newly written code is presented as if it were the original implementation.
- A future reproduction can be added separately and clearly labeled as a later reimplementation.
- The original patient dataset is intentionally excluded because it contains sensitive real-world medical data.
