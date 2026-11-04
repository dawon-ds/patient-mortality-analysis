# Final Results and Interpretation

## Validation

| Model | Accuracy | Precision | Recall | F1 |
| --- | ---: | ---: | ---: | ---: |
| XGBoost | 0.7692 | 0.78 | 0.77 | 0.77 |
| Logistic Regression | 0.7282 | 0.74 | 0.73 | 0.72 |
| BERT | 0.9136 | 0.8878 | 0.9136 | 0.8904 |
| RoBERTa | 0.9128 | 0.8332 | 0.9128 | 0.8712 |

## Test

| Model | Accuracy | Precision | Recall | F1 |
| --- | ---: | ---: | ---: | ---: |
| **XGBoost** | **0.7692** | **0.78** | **0.77** | **0.77** |
| Logistic Regression | 0.7282 | 0.74 | 0.73 | 0.72 |
| BERT | 0.7231 | 0.6566 | 0.7231 | 0.6566 |
| RoBERTa | 0.6769 | 0.4582 | 0.6769 | 0.5465 |

**XGBoost was the strongest test performer (F1 0.77).** The sequence models exhibited pronounced validation-to-test declines.

## Model Interpretation

![Logistic Regression coefficients](images/final/final-logistic-coefficients.jpg)

The report identifies positive coefficients for Midazolam use, patient weight, and cardiac-arrest event counts.

![XGBoost feature importance](images/final/final-xgboost-importance.jpg)

Highlighted XGBoost features include oxygen-flow use, head-of-bed adjustments, pulse oximetry frequency, and heart-rhythm measurement frequency. These are model associations, not proof of clinical causation.

## Limitations and Future Work

- Quantitative variables such as medication dose and fluid balance were not fully incorporated.
- Earlier admissions and rare clinical events were not comprehensively represented.
- Sequence inputs did not use learned elapsed-time embeddings or numerical event values; BERT/RoBERTa had a 512-token limit.
- Learning rate and weight decay sweeps were limited in the sequence experiments.
- Training/validation patient overlap, admission-selection differences between methods, and a clinically meaningful observation cutoff need additional validation.

## Source Notes

The earlier comparison reports XGBoost validation accuracy 0.8296 / test accuracy 0.7743 and Logistic Regression validation accuracy 0.8359 / test accuracy 0.7589. These differ from the final summary, which repeats the tabular validation and test entries. This page follows the final report summary while preserving that distinction. The coefficient figure and explanatory paragraphs do not establish a single unambiguous feature ranking.

Earlier in-hospital mortality metrics are documented separately in [Earlier Analysis](earlier-analysis.md).
