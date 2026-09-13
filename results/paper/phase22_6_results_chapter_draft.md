# Chapter 5 — Results
This chapter presents the empirical findings obtained under the predefined PoisonShield experimental protocol. The results are reported from persisted experimental artifacts and are not supplemented with fabricated or estimated empirical values.

## 5.1 Dataset and Experimental Evidence

Dataset characteristics, attack composition, experimental design, and empirical evidence coverage.

**Persisted evidence used:**

- `table_attack_distribution.csv`
- `table_poisoned_summary.csv`
- `table_class_imbalance_class_distribution.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.2 Exploratory Data Analysis

Descriptive statistics, class distributions, attack distributions, missingness, duplication, outliers, and invalid-value findings.

**Persisted evidence used:**

- `table_attack_distribution.csv`
- `table_class_imbalance_class_distribution.csv`
- `table_missingness_overall.csv`
- `table_duplicate_by_attack.csv`
- `table_outlier_by_attack.csv`
- `table_negative_values_by_attack.csv`
- `table_descriptive_malmem.csv`
- `table_descriptive_android.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.3 Integrity Results

Reference-consistency and provenance verification including modification, addition, removal, and multiplicity detection.

**Persisted evidence used:**

- `table_integrity_unit_tests_malmem.csv`
- `trusted_manifest_malmem.csv`
- `trusted_manifest_android.csv`
- `table_phase19_2_rq1_integrity_interpretation.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.4 Anomaly-Detection Results

Isolation Forest anomaly detection performance, threshold sensitivity, and attack-specific results.

**Persisted evidence used:**

- `table_anomaly_detection_performance.csv`
- `table_anomaly_attack_specific_malmem.csv`
- `table_anomaly_attack_specific_android.csv`
- `table_anomaly_threshold_sensitivity.csv`
- `table_phase19_3_rq2_anomaly_interpretation.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.5 PoisonShield Results

Combined anomaly and integrity evidence using the risk-based PoisonShield fusion architecture.

**Persisted evidence used:**

- `table8_attack_specific_results.csv`
- `table_phase19_4_rq3_poisonshield_interpretation.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.6 Attack-Specific Detection Results

Detection behavior across the seven primary poisoning mechanisms.

**Persisted evidence used:**

- `attack_specific_detection.csv`
- `attack_specific_detection_summary.csv`
- `table8_attack_specific_results.csv`
- `table_phase11_attack_specific_detection.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.7 Poisoning-Rate Results

Performance under controlled poisoning rates of 1%, 5%, 10%, and 20%.

**Persisted evidence used:**

- `table_phase18_11_poisoning_rate_sensitivity.csv`
- `table_phase19_6_rq4_rate_comparison.csv`
- `table_phase19_6_rq4_rate_summary.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.8 Downstream Model Performance

Clean-test classification performance under the evaluated training conditions.

**Persisted evidence used:**

- `table_phase19_7_rq5_downstream_comparison.csv`
- `table_phase19_7_rq5_downstream_summary.csv`
- `table_phase10_E1_E4_downstream_comparison.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.9 Ablation Results

Comparison of complete PoisonShield with component removal and alternative fusion configurations.

**Persisted evidence used:**

- `table_phase18_14_ablation_study.csv`
- `table_phase14_2_ablation_summary.csv`
- `table_phase14_3_ablation_comparisons.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.10 Robustness and Sensitivity Results

Seed, attack, threshold, dataset, and other persisted sensitivity analyses.

**Persisted evidence used:**

- `table_phase13_2_seed_robustness.csv`
- `table_phase13_4d_final_dataset_sensitivity_summary.csv`
- `table_phase13_4d_dataset_defense_summary.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.11 Statistical Comparisons

Statistical comparison results, effect sizes, confidence intervals, and adjusted significance where available.

**Persisted evidence used:**

- `table_phase18_15_statistical_comparison.csv`
- `table_phase15_7_statistical_interpretation.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.12 Computational Overhead

Training and filtering computational overhead and operational trade-offs.

**Persisted evidence used:**

- `table_phase18_15_statistical_comparison.csv`

**Results narrative:**

The empirical findings for this section should be reported directly from the listed persisted artifacts. Numerical values should be transcribed without alteration, with dataset-specific results and relevant uncertainty or comparison statistics reported where available.

## 5.13 Cross-Result Interpretation Principles

Integrity evidence is interpreted as reference-consistency and provenance evidence rather than direct proof of maliciousness. Anomaly detection is interpreted as feature-space statistical unusualness rather than independent proof of attacker intent. PoisonShield is therefore evaluated using complementary detection, false-positive, retention, downstream-performance, robustness, and statistical evidence.

The results must also preserve observed negative findings. Where an individual defense performs better than the combined PoisonShield configuration under a particular condition, that result must be reported rather than suppressed. The empirical evidence is interpreted under the locked experimental conditions and should not be generalized beyond the evaluated datasets, attack mechanisms, poisoning rates, and experimental protocol.

## 5.14 Results-to-Research-Question Mapping

- **RQ1:** Integrity and provenance verification.
- **RQ2:** Sample-level anomaly detection.
- **RQ3:** Combination of integrity and anomaly evidence through PoisonShield fusion.
- **RQ4:** Attack-specific and poisoning-rate robustness.
- **RQ5:** Downstream clean-test classification performance.
- **RQ6:** Detection, false-positive, retention, ablation, and operational trade-offs.
