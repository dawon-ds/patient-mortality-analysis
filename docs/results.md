# Results

## Cohort Summary

| Outcome | Patients |
| --- | ---: |
| Deceased | 1,147 |
| Survived | 858 |
| Total | 2,005 |

## Logistic Regression Performance

| Metric | Result |
| --- | ---: |
| Accuracy | ~57.97% |
| ROC-AUC | ~0.605 |
| Precision (non-survivor class) | 0.75 |
| Recall (non-survivor class) | 0.37 |

The model showed limited predictive performance, so the project used it mainly as an interpretable analysis tool rather than as a clinically deployable predictor.

## Variables Highlighted in the Analysis

The project materials identified several patterns associated with mortality:

- higher mean-glucose values,
- the engineered metabolic-stress feature,
- higher norepinephrine-related signals,
- renal dysfunction / AKI-related variables.

More stable blood-pressure and oxygen-related measurements were associated with lower modeled mortality risk.

These relationships should be interpreted as **associations within this dataset**, not causal effects.

## Interpretation

The analysis suggests that mortality risk is reflected across multiple physiological systems rather than by a single variable. In particular, metabolic, renal, hemodynamic, and treatment-related features all contributed useful signals.

At the same time, the modest ROC-AUC indicates that the available feature set and modeling approach were insufficient for strong patient-level prediction.

## Limitations

- Imbalanced outcome distribution
- Missing values and incomplete clinical records
- Potentially irrelevant or noisy variables
- Limited clinical context around diagnoses and disease severity
- No external validation cohort
- Logistic regression may not capture nonlinear interactions
- Patient-level source data cannot be publicly released because it contains sensitive medical information

## Future Work

Potential extensions include:

- stronger missing-data handling,
- more rigorous feature selection,
- diagnosis and disease-severity variables,
- post-ICU outcomes,
- nonlinear models,
- class-imbalance handling,
- external validation on an independent cohort.
