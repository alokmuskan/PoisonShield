# PoisonShield Research Decisions

## Research Focus

Training-data poisoning detection and defense.

## Primary Datasets

1. CICMalDroid2020-structured Android benchmark
2. CIC-MalMem-2022

## Primary Defense

PoisonShield:
1. Dataset integrity/provenance verification
2. Sample-level anomaly detection
3. Risk-based evidence fusion
4. High-risk sample filtering
5. Downstream malware classification

## Primary Anomaly Detector

Isolation Forest.

## Primary Downstream Classifier

Random Forest.

## Experimental Conditions

E1: No Defense
E2: Integrity Only
E3: Anomaly Only
E4: PoisonShield

## Random Seeds

42, 123, 2026

## Controlled Poisoning Rates

1%, 5%, 10%, 20%

## Anomaly Threshold Candidates

90th, 95th, 97.5th, 99th percentiles.

## Important Leakage Rules

The following must never be used as anomaly-model predictors:

- poisoned
- attack_type
- original_label
- Class
- source_index

The `poisoned` variable is an evaluation target only.

## Raw Data Rule

Files in data/raw must never be modified.

## Test Set Rule

The clean test set must remain locked until final downstream evaluation.

## Integrity Interpretation

Integrity evidence indicates reference inconsistency.
It does not independently establish maliciousness.

## Dataset Identity Note

The supplied file named
cccs_andmal2020_poisoned.csv
matches the structure of CICMalDroid2020 rather than the official CCCS-CIC-AndMal-2020 dataset.

This distinction must be documented transparently in the final research report.