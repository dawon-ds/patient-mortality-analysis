# Final Results and Limitations

The README table follows the final presentation summary and the final report summary.

| Model | Validation accuracy | Validation F1 | Test accuracy | Test F1 |
| --- | ---: | ---: | ---: | ---: |
| XGBoost | 0.7692 | 0.77 | 0.7692 | 0.77 |
| Logistic regression | 0.7282 | 0.72 | 0.7282 | 0.72 |
| BERT | 0.9136 | 0.8904 | 0.7231 | 0.6566 |
| RoBERTa | 0.9128 | 0.8712 | 0.6769 | 0.5465 |

## Source Differences

The earlier baseline comparison reports XGBoost validation accuracy 0.8296 and test accuracy 0.7743, and logistic regression validation accuracy 0.8359 and test accuracy 0.7589. These differ from the final summary, which repeats tabular validation and test entries. The comparisons are documented separately because their reported values differ. Metric averaging in the final summary is unspecified.

The coefficient chart, slide annotation, and report prose also differ in their listed top features. Preserve the original figures without claiming one definitive feature ranking.

## Interpretation and Limits

The final summary shows a marked Transformer validation-to-test decline. Tabular and sequence sampling differs, and patient-level grouping in sequence validation is not verified. Clinical events within the outcome window can compromise an early prediction claim unless an earlier observation cutoff is defined. Model associations and feature importances do not establish causal effects.

Final tabular and sequence notebooks are now available alongside the earlier in-hospital mortality code. Earlier accuracy 57.97% and ROC-AUC 0.605 are documented separately in [Earlier analysis](earlier-analysis.md).
