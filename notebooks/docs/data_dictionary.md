# PoisonShield Data Dictionary and Variable Governance

## Core Governance Rules

- `attack_type` is attack-mechanism metadata and is excluded from anomaly-model predictors.
- `source_index` is provenance metadata and is excluded from model predictors.
- `poisoned` is an evaluation target and is never a model predictor.
- `original_label` is reference/original label information and is excluded from anomaly predictors.
- `Class` is the observed class label and is used as the downstream classification target, not as an anomaly predictor.
- Constant features are excluded from modeling but retained in the raw datasets.
- Raw datasets are not modified by this phase.

## Final Anomaly Feature Counts

- CIC-MalMem-2022: 52 features
- Android / CICMalDroid2020 structure: 468 features

## Leakage Control

No attack labels, poisoning indicators, provenance identifiers, or observed class labels are included in the anomaly-model feature sets.
