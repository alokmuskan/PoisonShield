# Chapter 5 — Results
This chapter presents the empirical results obtained from the PoisonShield evaluation. Results are reported using the persisted experimental artifacts generated during the study. No additional experiments are introduced at this stage.

## 5.1 Dataset and Experimental Evidence
The evaluation used the two supplied malware datasets, MalMem and the supplied Android dataset. The experimental analysis preserved the seven primary poisoning mechanisms and treated the class-imbalance audit artifacts separately. Dataset structure, feature counts, attack composition, missingness, duplication, and other data-quality properties were established during the dataset audit.

## 5.2 Exploratory Data Analysis
Exploratory analysis examined attack distributions, class distributions, missingness, duplicate observations, invalid values, outliers, descriptive statistics, and feature relationships. Missingness and duplication were retained as potential attack evidence rather than automatically removed.

## 5.3 Integrity Results
The integrity layer was evaluated as reference-consistency evidence. The trusted reference manifest was used to identify deviations including modified, added, removed, and duplicated observations. Integrity evidence therefore indicates deviation from the trusted reference state rather than directly proving malicious intent.

## 5.4 Anomaly-Detection Results
The anomaly layer used Isolation Forest trained only on the clean training reference. Anomaly scores were evaluated across the poisoning mechanisms using the predefined detection metrics, including precision, recall, F1, ROC-AUC, PR-AUC, false-positive rate, and false-negative rate.

## 5.5 PoisonShield Results
PoisonShield combined normalized anomaly evidence with integrity evidence through the risk-fusion formulation. Fusion parameters and decision thresholds were determined using the designated validation procedure before final evaluation. The persisted results are used here without alteration.

## 5.6 Attack-Specific Results
Detection performance was examined separately for backdoor injection, feature poisoning, Gaussian-noise injection, label flipping, missing-value injection, outlier injection, and sample duplication. This analysis is necessary because different poisoning mechanisms can produce different statistical signatures.

## 5.7 Poisoning-Rate Results
Controlled poisoning-rate experiments examined the effect of increasing poisoning prevalence at the predefined rates of 1%, 5%, 10%, and 20%. The clean test set remained fixed for downstream evaluation.

## 5.8 Downstream Model Results
Downstream effects were evaluated using the Random Forest classifier and the same clean test reference. Performance was summarized using classification metrics including macro-F1, with comparisons across the experimental training conditions.

## 5.9 Ablation
The ablation analysis compared integrity-only, anomaly-only, combined, unoptimized, and component-removal configurations. The purpose was to determine whether the combined architecture provided additional empirical value over its individual components.

## 5.10 Robustness
Robustness analysis considered seed sensitivity, threshold sensitivity, fusion-weight sensitivity, attack-specific behavior, and dataset-specific behavior. These analyses identify conditions under which the observed defense behavior is stable or changes materially.

## 5.11 Statistical Comparisons
Statistical analysis used the persisted matched experimental results. Where repeated matched observations were available, comparisons considered effect sizes, confidence intervals, paired differences, and appropriate multiple-method testing. Statistical significance was distinguished from practical significance.

## 5.12 Computational Overhead
Computational overhead was assessed using the persisted runtime-related experimental evidence where available. Reported computational observations are interpreted in the context of the detection and filtering benefits measured elsewhere in the evaluation.
