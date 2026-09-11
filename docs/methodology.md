# Methodology

## Objective

The project aimed to identify clinical variables associated with in-hospital mortality among cardiac-arrest patients and to summarize those relationships through visualization and statistical modeling.

## Cohort

The analysis covered **2,005 patients** with a binary target variable, `hospital_expire_flag`.

The dataset integrated multiple types of clinical information, including demographics, vital signs, laboratory measurements, medications, ICU/treatment variables, and procedures.

## Data Integration

Clinical tables were merged using patient identifiers to build a patient-level analysis table. Because the original data contains real-patient medical information, the raw tables and any patient-level samples are excluded from this public repository.

## Exploratory Analysis

The project examined distributions and mortality-group differences across several categories of variables, including:

- hemodynamic measurements,
- oxygen-related measurements,
- glucose measurements,
- renal-function indicators,
- medication exposure,
- treatment-related variables.

## Feature Engineering

Threshold-based features were created using distribution cutoffs such as the **90th and 10th percentiles** to capture unusually high or low measurements.

A derived **metabolic stress** feature combined oxygen-saturation information with extreme mean-glucose behavior to represent joint physiological stress.

## Modeling

### Logistic Regression

Logistic regression was used as the primary classification model because it provides both a binary prediction framework and interpretable coefficient directions for feature analysis.

The project examined which variables were positively or negatively associated with the mortality target while also evaluating overall classification performance.

### Linear Regression

Linear regression was explored as an alternative continuous scoring approach, but it was considered less appropriate for the binary target than logistic regression.

## Interpretation Principle

The project reports **associations rather than causal effects**. For example, higher norepinephrine exposure among deceased patients may reflect underlying illness severity and should not be interpreted as evidence that norepinephrine itself causes mortality.
