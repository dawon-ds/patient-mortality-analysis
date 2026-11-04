# Final Notebook Setup

## Dependencies

Install requirements.txt in a private Python environment. Transformer notebooks require a compatible PyTorch/CUDA environment and memory suitable for BERT/RoBERTa. The original report used Python 3.8.18, PyTorch 2.0.1+cu117, and Transformers 4.41.2 for sequence experiments.

## Data and Working Directory

Use only independently authorized clinical data. The original sequence workflow expects train/, test/, and dictionary/ directories relative to its working directory, and generates train_F/ and test_F/ intermediates. Keep these directories private and outside tracked source files.

Raw tables use _train.csv and _test.csv suffixes; dictionary/d_items.csv supplies item labels. The final tabular notebook reads private CSV inputs from `PATIENT_DATA_ROOT` (default: `data/` under the working directory). For example, set `PATIENT_DATA_ROOT=/secure/path/to/mimic` before running it. Its `merged_data/` inputs are resolved beneath the same root. It also writes relative JSON/CSV intermediates; review output locations before execution. `original_analysis.ipynb` is kept as a historical notebook and retains its original Colab `/content/` paths.

## Order

1. tabular_modeling.ipynb: integration, target creation, feature processing, and tabular experiments.
2. sequence_preprocessing_train.ipynb: training timelines, item selection, and train_F/bert_input.json.
3. sequence_preprocessing_test.ipynb: test timelines and test_F/bert_input.json; it references training-derived item-selection files.
4. sequence_training_evaluation.ipynb: BERT/RoBERTa training and evaluation using the generated JSON files.

The notebooks have sequential cell dependencies. They are not converted into an automated pipeline. One bare pip-install cell was corrected to %pip; other model logic and original paths are preserved.

## Verification and Boundaries

Notebook JSON and Python-cell syntax were checked, excluding IPython magics. Stored outputs and metadata were removed before publication.

Review label construction, observation windows, feature fitting, and patient-group splitting before interpreting the models as prospective predictions. Read data-security.md before using or sharing any generated output.
