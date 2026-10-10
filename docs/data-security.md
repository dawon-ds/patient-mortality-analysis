# Data Security and Access Policy

## Strict Public Data Exclusion

This public repository must not contain patient-level clinical data. The source tables include medical measurements, demographics, patient/admission identifiers, timestamps, medications, procedures, and mortality outcomes. De-identification does not remove source access restrictions or grant permission to redistribute clinical records.

Source data access and use must follow the applicable provider's authorization, credentialing, training, and data-use conditions. This repository does not grant data access or redistribution rights.

## Excluded Materials

- All clinical CSVs, including raw, train/test, merged, intermediate, and feature-engineered tables.
- Patient-level JSON, event sequences, text inputs, and identifier mappings.
- Dataset archives, backups, and notebook checkpoints.
- Notebook outputs, embedded patient screenshots, cached records, and logs that may reveal patient data.
- Locally generated model checkpoints and prediction artifacts, pending separate privacy and release review.

Item dictionaries are not distributed in this repository either. Obtain required source tables and dictionaries through authorized access.

## Private Execution

Keep data and generated artifacts in an approved private environment. Do not upload records through GitHub issues, pull requests, releases, or public screenshots. Review every commit for data and outputs; ignore rules are a guard, not an enforcement or privacy guarantee.

All published notebooks are source-only with stored outputs, execution counts, attachments, and cell metadata removed. Historical presentation images contain aggregate model results.

## Reproducibility

Code and aggregate reported findings are public. Reproduction requires independently authorized data, path configuration, suitable dependencies, and private compute. No patient samples are supplied.
