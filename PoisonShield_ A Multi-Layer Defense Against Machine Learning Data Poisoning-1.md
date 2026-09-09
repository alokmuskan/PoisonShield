# PoisonShield: A Multi-Layer Defense Against Machine Learning Data Poisoning

## Research Proposal

---

## 1. Title

**PoisonShield: A Multi-Layer Defense Against Machine Learning Data Poisoning Using Dataset Integrity Verification, Sample-Level Anomaly Detection, and Risk-Based Evidence Fusion**

---

## 2. Abstract

Machine learning systems increasingly depend on large datasets collected, processed, transferred, and maintained through complex data pipelines. This dependence introduces a critical security vulnerability: adversaries may manipulate training data through poisoning attacks in order to degrade model performance, induce targeted behavior, or create hidden backdoors. Existing research has developed a range of poisoning attacks and defenses, including robust statistics, outlier filtering, backdoor detection, certified defenses, and data-sanitization techniques. However, poisoning defenses are often evaluated against particular attack mechanisms or within a single application domain, making it difficult to determine whether complementary evidence sources can provide a more general and operationally useful defense.

This research proposes **PoisonShield**, a multi-layer defense framework that combines two distinct forms of evidence: **dataset integrity/provenance evidence** and **sample-level anomaly evidence**. The integrity layer establishes whether an observed record corresponds to a trusted reference state using canonicalized records and cryptographic hashing. The anomaly layer uses unsupervised anomaly detection, with Isolation Forest as the primary detector, to identify observations whose feature patterns differ from the trusted clean training distribution. These signals are combined through a validation-based risk-fusion mechanism to assign observations to trusted, review, or high-risk categories.

The empirical evaluation will use two heterogeneous cybersecurity datasets. The first is CIC-MalMem-2022, a Windows memory-forensics dataset containing 58,596 observations and 55 underlying memory features. The second supplied dataset, stored as `cccs_andmal2020_poisoned.csv`, contains 11,598 observations and 470 feature variables and structurally corresponds to CICMalDroid2020 rather than the official 400,000-sample CCCS-CIC-AndMal-2020 dataset. The supplied poisoned datasets contain controlled attack labels for backdoor injection, Gaussian-noise injection, label flipping, feature poisoning, missing-value injection, outlier injection, and sample duplication. Separate audit files contain an additional class-imbalance attack condition.

The study will compare four principal conditions: no defense, integrity-only defense, anomaly-only defense, and the proposed PoisonShield fusion. Evaluation will consider precision, recall, F1-score, false-positive rate, false-negative rate, ROC-AUC, PR-AUC, attack-specific detection performance, clean-data retention, computational overhead, and downstream classification performance after filtering. Controlled poisoning proportions and multiple random seeds will be used to assess robustness. The study will not treat integrity verification as a universal poisoning detector; rather, integrity provides provenance evidence while anomaly detection provides sample-level behavioral evidence.

The expected contribution is an empirically evaluated, modular framework for combining complementary security signals for training-data screening across heterogeneous cybersecurity domains. The study aims to determine whether such evidence fusion improves poisoning detection and downstream model robustness while controlling the removal of legitimate training observations.

**Keywords:** machine learning security, data poisoning, adversarial machine learning, dataset integrity, data provenance, anomaly detection, Isolation Forest, malware detection, backdoor attacks, training-data security.

---

# 3. Introduction and Background

Machine learning has become an important component of cybersecurity systems, including malware detection, intrusion detection, fraud detection, threat classification, and behavioral analysis. Such systems depend heavily on the quality and trustworthiness of their training data. If the training dataset is manipulated before or during model training, the resulting model may learn incorrect relationships or attacker-controlled behaviors.

This problem is commonly referred to as **data poisoning**. In a poisoning attack, an adversary manipulates or injects training observations with the intention of changing the behavior of a learned model. Biggio, Nelson, and Laskov demonstrated that carefully constructed poisoning samples can increase the test error of learning algorithms, establishing poisoning as a significant security threat to the training process.

The threat has subsequently expanded beyond classical poisoning attacks. Backdoor attacks, for example, can insert specially crafted training observations that cause a model to behave normally on ordinary inputs but respond maliciously when an attacker-controlled trigger is present. BadNets demonstrated this threat in neural-network supply chains, while later work such as spectral-signature detection and activation clustering investigated methods for identifying poisoned training examples.

A broader survey by Wang et al. organizes poisoning attacks and defenses and emphasizes the diversity of attack mechanisms and defense strategies. The survey also highlights the need for more systematic and reproducible approaches to poisoning research. Goldblum et al. similarly frame dataset security as a fundamental component of machine-learning security, emphasizing vulnerabilities introduced by untrusted or insufficiently supervised training data.

A central challenge is that no single observable signal necessarily identifies every type of poisoning. Some attacks modify feature values, some manipulate labels, some introduce unusual observations, some introduce duplicates, and others can modify data in ways that remain close to the clean distribution. Consequently, a defense based exclusively on feature-space anomaly detection may miss attacks that do not appear sufficiently anomalous, while an integrity mechanism cannot identify arbitrary new or modified records unless an appropriate trusted reference exists.

This research therefore investigates whether two complementary forms of evidence can be combined:

1. **Integrity/provenance evidence**, which addresses whether an observed record corresponds to a trusted reference state; and
2. **Sample-level anomaly evidence**, which addresses whether an observation is unusual relative to a trusted clean distribution.

The resulting framework is named **PoisonShield**.

---

# 4. Research Problem

The fundamental research problem is the difficulty of reliably screening machine-learning training data when the data may contain heterogeneous poisoning attacks.

Existing defenses frequently focus on particular attack families or assumptions. For example, outlier-removal approaches can be useful against certain poisoning strategies, while backdoor-specific methods exploit characteristic properties of poisoned representations. Certified defenses provide formal guarantees under specific assumptions, but such approaches may not directly provide a general operational mechanism for heterogeneous tabular cybersecurity datasets. Steinhardt, Koh, and Liang demonstrate that outlier removal can be studied theoretically as a poisoning defense, while also showing that the effectiveness of a defense depends on the underlying data and attack conditions.

A second problem concerns the distinction between **data integrity** and **data anomaly**.

An integrity mechanism can determine whether a record matches a previously trusted reference representation. However, it cannot automatically determine that every new or previously unseen record is malicious. Conversely, anomaly detection can identify unusual observations without requiring a trusted copy of every record, but anomalous observations are not necessarily poisoned. Legitimate cybersecurity data may naturally contain rare or unusual observations.

Therefore, a research framework is required that does not assume that either integrity verification or anomaly detection is sufficient by itself.

The problem addressed by this study is consequently:

> **Can a multi-layer defense that combines trusted-data integrity evidence with sample-level anomaly evidence improve the detection and mitigation of heterogeneous machine-learning poisoning attacks while limiting unnecessary removal of legitimate training observations?**

---

# 5. Research Gap

The literature establishes several important directions in machine-learning poisoning defense.

First, poisoning attacks and defenses have been extensively studied, but the attack and defense landscape is heterogeneous. Wang et al. identify a broad range of poisoning attacks and countermeasures and emphasize the need for systematic and reproducible evaluation.

Second, several studies have developed specialized sample-detection mechanisms. Spectral signatures exploit statistical properties of poisoned representations, while activation clustering investigates clustering-based detection of backdoored training examples.

Third, robust-statistical and outlier-removal approaches have demonstrated that filtering suspicious observations can provide a useful defense under certain assumptions. Certified defenses provide a more formal treatment of poisoning robustness but are also based on particular assumptions about the data and defense process.

Fourth, dataset security research increasingly recognizes the importance of data provenance and trustworthiness. However, provenance evidence and sample-level anomaly evidence address different questions. Provenance establishes whether a data object or dataset state corresponds to a trusted reference; anomaly detection evaluates whether an observation appears unusual.

The research gap investigated here is therefore not the invention of hashing, provenance, anomaly detection, or poisoning defense individually. Those components are already established.

Instead, the proposed contribution is:

> **The systematic integration and empirical evaluation of dataset integrity/provenance evidence and sample-level anomaly evidence as complementary signals in a risk-based poisoning-defense framework, evaluated across heterogeneous cybersecurity datasets and multiple poisoning mechanisms.**

The study will therefore make a **conservative novelty claim**. It will not claim that PoisonShield is the first poisoning defense, the first use of anomaly detection, or the first use of dataset integrity. Its contribution lies in the proposed integration, experimental design, cross-domain evaluation, and empirical assessment of whether the combination provides measurable advantages over its individual components.

---

# 6. Research Aim

The overall aim of the research is:

> **To design, implement, and empirically evaluate PoisonShield, a multi-layer defense framework that combines dataset integrity verification and sample-level anomaly detection to identify and mitigate heterogeneous machine-learning data poisoning attacks.**

---

# 7. Research Objectives

### Objective 1
To characterize the supplied poisoned cybersecurity datasets with respect to dimensions, feature structure, labels, poisoning mechanisms, missingness, duplicates, constant variables, and other data-quality properties relevant to poisoning detection.

### Objective 2
To develop an integrity/provenance layer that establishes whether observed records correspond to a trusted clean reference state.

### Objective 3
To develop an unsupervised sample-level anomaly detection layer trained exclusively on trusted clean training observations.

### Objective 4
To develop a risk-fusion mechanism that combines integrity evidence and anomaly evidence without using attack labels as model inputs.

### Objective 5
To compare PoisonShield against integrity-only, anomaly-only, and no-defense conditions.

### Objective 6
To evaluate detection performance separately across the available poisoning mechanisms.

### Objective 7
To evaluate whether filtering high-risk observations improves downstream cybersecurity classification performance.

### Objective 8
To examine the robustness of the framework across poisoning proportions, decision thresholds, random seeds, and two heterogeneous cybersecurity datasets.

---

# 8. Research Questions

| RQ | Research Question |
|---|---|
| RQ1 | How effectively can the proposed integrity/provenance mechanism identify alterations between a trusted dataset state and an observed training dataset? |
| RQ2 | How effectively can sample-level anomaly detection identify poisoned observations without using poisoning labels as model inputs? |
| RQ3 | How does poisoning-detection performance vary across the different poisoning mechanisms represented in the supplied datasets? |
| RQ4 | Does combining integrity evidence with anomaly evidence improve poisoning-detection performance compared with either mechanism independently? |
| RQ5 | What is the effect of PoisonShield on the false-positive removal of legitimate training observations? |
| RQ6 | Does filtering high-risk observations with PoisonShield improve downstream model performance under poisoned-training conditions? |

These questions are directly supported by the available data because the supplied datasets contain attack-type annotations that can be used for evaluation while the attack labels can be withheld from the detection models.

---

# 9. Hypotheses

## H1: Overall detection effectiveness

**H1:** PoisonShield will achieve better overall poisoning-detection performance than anomaly-only detection under the evaluated poisoning conditions.

The primary comparison will use F1, PR-AUC, recall, precision, and false-positive rate rather than accuracy alone.

---

## H2: Complementary evidence

**H2:** Combining integrity evidence with anomaly evidence will produce better poisoning-detection performance than either integrity-only or anomaly-only detection under conditions where both forms of evidence are informative.

---

## H3: Attack-specific variation

**H3:** Poisoning-detection performance will differ significantly across poisoning mechanisms.

This hypothesis recognizes that attacks such as feature poisoning, missing-value injection, duplication, label flipping, and backdoor injection can produce different observable patterns.

---

## H4: Risk-threshold trade-off

**H4:** PoisonShield's risk threshold will produce a measurable precision-recall/false-positive trade-off, with more aggressive filtering increasing poisoning recall at the cost of potentially removing more legitimate observations.

---

## H5: Downstream model robustness

**H5:** Filtering high-risk observations with PoisonShield will improve downstream classification performance relative to training on the corresponding poisoned dataset without defense.

---

# 10. Literature Review

## 10.1 Data Poisoning

Data poisoning attacks target the training stage rather than merely manipulating test-time inputs. Biggio et al. showed that an attacker can construct training observations designed to increase the error of a target learning algorithm.

This distinction is important for the current research because PoisonShield operates on the training dataset before downstream model fitting.

Wang et al. provide a broad taxonomy of poisoning threats and defenses and distinguish different attack objectives and mechanisms. Their survey highlights the importance of systematic evaluation because poisoning attacks can target different components of the training process.

---

## 10.2 Backdoor Poisoning

Backdoor attacks represent an important form of poisoning because the model may perform normally on standard test observations while exhibiting attacker-controlled behavior under a trigger.

BadNets demonstrated the security implications of poisoned model training and supply-chain vulnerabilities.

Tran, Li, and Madry proposed spectral signatures for identifying poisoned examples based on statistical properties of learned representations.

Chen et al. proposed activation clustering as a mechanism for detecting backdoor-poisoned data in neural networks.

These approaches demonstrate the value of examining statistical or representational irregularities, supporting the anomaly-detection component of PoisonShield. However, the present study deliberately uses a more general tabular-data setting and evaluates multiple poisoning mechanisms rather than only backdoors.

---

## 10.3 Outlier-Based Defenses

Outlier detection is a natural defense against poisoning because some attacks intentionally create observations that differ from the clean distribution.

Steinhardt et al. studied defenses that remove outliers before empirical risk minimization and developed certified bounds under specific assumptions. Their work demonstrates both the usefulness and limitations of outlier-removal approaches.

Isolation Forest, introduced by Liu, Ting, and Zhou, provides an unsupervised mechanism for isolating observations using recursive partitioning.

Isolation Forest is therefore selected as the primary anomaly detector in this research. It provides a practical baseline that does not require poisoned labels during training.

---

## 10.4 Dataset Security and Provenance

Dataset security extends beyond statistical anomaly detection. Goldblum et al. emphasize that training datasets themselves can represent a security vulnerability when their collection and curation cannot be sufficiently trusted.

This motivates the integrity layer of PoisonShield.

However, an important conceptual distinction is maintained throughout this research:

> **Integrity verification is not treated as a universal poisoning detector.**

A cryptographic hash can establish whether a record matches a trusted reference representation. It cannot determine whether an arbitrary new record is malicious merely because the record has no corresponding hash.

Therefore, integrity is treated as **provenance/integrity evidence**, while anomaly detection is treated as **sample-level behavioral evidence**.

---

# 11. Theoretical and Conceptual Framework

The conceptual framework is based on the assumption that trustworthy training data can be characterized using complementary evidence dimensions.

Let an observed training observation be represented by:

\[
x_i
\]

and let its trusted reference state, when available, be represented by:

\[
x_i^{*}.
\]

The framework produces two principal evidence signals.

### 11.1 Integrity evidence

A canonical representation of a trusted observation is hashed:

\[
h_i = H(x_i^{*})
\]

where \(H\) is a cryptographic hash function such as SHA-256.

The observed observation is then compared against the trusted manifest.

An exact match provides positive integrity evidence, whereas a mismatch provides integrity-risk evidence.

---

### 11.2 Anomaly evidence

An anomaly detector is trained on the trusted clean training distribution:

\[
D_{\text{clean}}.
\]

For observation \(x_i\), the detector produces an anomaly score:

\[
A_i.
\]

Higher values indicate greater deviation from the learned clean distribution after the score has been oriented consistently for interpretation.

---

### 11.3 Risk fusion

PoisonShield combines the two evidence sources:

\[
R_i =
\alpha A_i^{*}
+
\beta I_i
\]

where:

- \(R_i\) = overall poisoning risk score;
- \(A_i^{*}\) = normalized anomaly evidence;
- \(I_i\) = integrity-risk evidence;
- \(\alpha\) and \(\beta\) = fusion weights.

The weights and final decision threshold will **not be assumed to be optimal in advance**. They will be selected using validation data under a predefined procedure.

The resulting risk score can be operationalized as:

\[
R_i < \tau_1
\Rightarrow
\text{Trusted}
\]

\[
\tau_1 \leq R_i < \tau_2
\Rightarrow
\text{Review}
\]

\[
R_i \geq \tau_2
\Rightarrow
\text{High Risk}
\]

The exact thresholds will be determined empirically.

---

# 12. Data and Dataset Description

## 12.1 Dataset identification and an important correction

Two primary poisoned datasets are available for the research.

### Dataset A: CIC-MalMem-2022

CIC-MalMem-2022 is a malware-memory dataset developed through the Canadian Institute for Cybersecurity. Public descriptions report 58,596 observations and 55 memory-derived features, with 29,298 benign and 29,298 malicious observations. The dataset was created from malware samples and benign Windows activity, with memory features extracted using VolMemLyzer.

The supplied poisoned file contains:

- 58,596 rows
- 59 columns
- 55 underlying feature variables
- `Class`
- `attack_type`
- `original_label`
- `source_index`

The additional columns are metadata introduced for the poisoning benchmark and are not considered predictor variables.

---

### Dataset B: supplied Android poisoning dataset

The supplied file is named:

`cccs_andmal2020_poisoned.csv`

However, its observed structure is:

- 11,598 rows
- 474 columns
- 471 numerical feature columns
- `Class`
- `attack_type`
- `original_label`
- `source_index`

The five class categories in the supplied file are:

- Benign
- Adware
- Banking
- SMS malware
- Riskware

This structure matches the official **CICMalDroid2020** dataset rather than the official CCCS-CIC-AndMal-2020 dataset. The CICMalDroid2020 page reports exactly 11,598 analyzed samples and a 470-feature CSV consisting of system-call, binder, and composite-behavior frequencies.

By contrast, the official CCCS-CIC-AndMal-2020 dataset is described as containing 400,000 Android applications, consisting of 200,000 benign and 200,000 malware samples, with 14 malware categories and 191 malware families.

Therefore, this proposal **does not incorrectly identify the supplied 11,598-row file as the official CCCS-CIC-AndMal-2020 dataset**.

For reproducibility, the research will record the actual filename and document this dataset-identity discrepancy in the data provenance log.

---

# 13. Poisoning Conditions

The primary poisoned files contain the following attack conditions.

| Attack condition | CIC-MalMem | Android dataset |
|---|---:|---:|
| Clean | 42,202 | 6,117 |
| Backdoor injection | 2,342 | 783 |
| Gaussian-noise injection | 2,342 | 783 |
| Label flipping | 2,342 | 783 |
| Feature poisoning | 2,342 | 783 |
| Missing-value injection | 2,342 | 783 |
| Outlier injection | 2,342 | 783 |
| Sample duplication | 2,342 | 783 |
| **Total** | **58,596** | **11,598** |

The attack proportions in these supplied benchmark files are not interpreted as real-world poisoning prevalence. In particular, the fact that the CIC-MalMem poisoned file contains a particular percentage of attack-labeled observations does not imply that such a poisoning rate is representative of operational environments.

---

# 14. Separate Class-Imbalance Audit Data

Two additional files are available:

- `cic_malmem2022_removed_audit.csv`
- `cccs_andmal2020_removed_audit.csv`

These contain observations labeled:

`class_imbalance_attack`

The CIC audit file contains 2,342 observations, while the Android audit file contains 783 observations.

These files will initially be treated as **separate experimental material** rather than being appended to the primary poisoned datasets.

This prevents the research from incorrectly assuming that the class-imbalance manipulation is structurally equivalent to the seven primary poisoning mechanisms.

---

# 15. Variables and Measurement

## 15.1 Primary dependent variable

The principal detection outcome is:

\[
Y_i =
\begin{cases}
1 & \text{if observation } i \text{ is poisoned}\\
0 & \text{if observation } i \text{ is clean}
\end{cases}
\]

For evaluation only:

```text
poisoned = 1 if attack_type != clean
poisoned = 0 if attack_type == clean
```

This variable will **never be provided to the anomaly detector as an input**.

---

## 15.2 Attack type

`attack_type` identifies the poisoning mechanism.

It is used for:

- attack-specific evaluation;
- stratified reporting;
- robustness analysis.

It is not used as a predictor.

---

## 15.3 Original and observed labels

`original_label` represents the reference label available in the benchmark.

`Class` represents the observed class label in the supplied poisoned file.

The difference between these variables is particularly relevant for label-changing attacks.

In CIC-MalMem-2022, 4,684 observations have:

\[
Class \neq original\_label.
\]

In the Android poisoned file, 1,566 observations have:

\[
Class \neq original\_label.
\]

Neither `Class` nor `original_label` will be used as anomaly-model predictors.

---

## 15.4 Feature variables

All numerical feature variables that remain after documented quality screening will be candidates for anomaly modeling.

Metadata variables will be excluded.

The following are excluded from model predictors:

- `Class`
- `attack_type`
- `original_label`
- `source_index`
- derived `poisoned`
- derived evaluation variables

Constant features will also be excluded because they provide no discriminatory information.

---

# 16. Data-Quality Characteristics

The supplied primary CIC-MalMem poisoned file contains:

- 4,684 missing cells;
- 170 exact duplicate rows;
- three constant numerical features:
  - `pslist.nprocs64bit`
  - `handles.nport`
  - `svcscan.interactive_process_services`.

The supplied Android poisoned file contains:

- 1,566 missing cells;
- 33 exact duplicate rows;
- two constant numerical features:
  - `getGroupIdLevel1`
  - `registerSuggestionSpansForNotification`.

The duplicate rows will **not automatically be removed** because sample duplication itself is one of the benchmark attack mechanisms.

Similarly, missing observations will not simply be discarded because missing-value injection is itself an evaluated attack.

---

# 17. Methodology

## 17.1 Research design

The study adopts a quantitative experimental machine-learning research design.

The framework will be evaluated through controlled experiments in which:

1. trusted clean data are established;
2. poisoning conditions are identified;
3. integrity evidence is calculated;
4. anomaly evidence is calculated;
5. evidence is fused;
6. suspicious observations are filtered;
7. downstream models are trained;
8. clean test performance is evaluated.

The two datasets will be analyzed **independently**, not merged.

---

# 18. Phase 1: Dataset Audit

The first stage will produce a complete reproducible audit for each dataset.

The audit will report:

- number of rows;
- number of columns;
- numerical and non-numerical variables;
- class distributions;
- attack distributions;
- missing values;
- duplicate observations;
- constant features;
- label mismatches;
- source-index characteristics;
- feature ranges and descriptive statistics.

The audit establishes the empirical foundation for all subsequent preprocessing decisions.

---

# 19. Phase 2: Data Partitioning

The research will distinguish among:

### Clean reference/training data

Used to establish the trusted clean distribution and train the anomaly detector.

### Validation data

Used to:

- select anomaly thresholds;
- tune risk-fusion parameters;
- determine candidate decision thresholds;
- select hyperparameters under a predefined protocol.

### Clean test data

Used only for final downstream evaluation.

No test-set information will be used to tune PoisonShield.

---

# 20. Leakage Prevention

Leakage control is a central methodological requirement.

All preprocessing operations that learn parameters from data will be fitted using training/reference data only.

This includes:

- imputation parameters;
- scaling parameters;
- feature-selection parameters, if used;
- anomaly-model parameters;
- anomaly thresholds;
- risk-fusion weights;
- final decision thresholds.

The test set will remain isolated until final evaluation.

Furthermore, poisoning metadata such as `attack_type`, `original_label`, and `Class` will not be supplied to the anomaly detector.

---

# 21. Handling Missing Values

Missingness requires special treatment because missing-value injection is itself one of the evaluated attack mechanisms.

Therefore, the research will not immediately erase missingness through unconditional preprocessing.

Instead, the pipeline will:

1. preserve missingness information;
2. create missingness indicators where appropriate;
3. fit imputation parameters using clean training data only;
4. apply the learned transformation to validation and test data.

This prevents missing-value attacks from being completely hidden by preprocessing.

---

# 22. Feature Screening

Feature screening will include:

1. removal of metadata variables;
2. identification of constant features;
3. missingness analysis;
4. examination of feature scale and distribution;
5. correlation analysis where appropriate;
6. assessment of near-zero variance features;
7. optional dimensionality-reduction sensitivity analysis if high-dimensionality materially affects detector behavior.

PCA will not automatically be applied. If dimensionality reduction is evaluated, it will be treated as a separate experimental condition and its transformation will be fitted using clean training data only.

---

# 23. Integrity Layer

## 23.1 Trusted manifest

A trusted clean reference dataset will be established.

Each trusted observation will be converted to a canonical representation.

A cryptographic hash such as SHA-256 will then be computed:

\[
h_i=SHA256(canonical(x_i)).
\]

The resulting hashes form a trusted manifest.

---

## 23.2 Verification

For an observed record:

\[
h_i^{obs}=SHA256(canonical(x_i^{obs})).
\]

The observed hash is compared against the trusted manifest.

An exact match provides integrity evidence.

A mismatch provides integrity-risk evidence.

---

## 23.3 Scope of the integrity layer

The integrity mechanism is explicitly **not** interpreted as a universal poison detector.

For example, a new record that has never appeared in the trusted reference dataset may fail the hash comparison without necessarily being poisoned.

Similarly, a malicious modification cannot be identified solely by claiming that "different hash = poisoned" unless the trusted reference relationship is valid.

Therefore, the integrity layer provides **reference-consistency evidence**, not an absolute poisoning label.

---

# 24. Anomaly Detection Layer

The primary anomaly detector will be **Isolation Forest**.

Isolation Forest was introduced by Liu, Ting, and Zhou as an efficient tree-based method for isolating anomalous observations.

The model will be trained only on clean reference observations.

The conceptual pipeline is:

\[
D_{clean}
\rightarrow
Preprocessing
\rightarrow
IsolationForest
\rightarrow
AnomalyScore.
\]

The initial implementation may use parameters such as:

- 300 trees;
- fixed random seed;
- parallel computation;
- contamination determined through validation rather than assumed as the true poisoning rate.

These values are implementation starting points, not final scientifically optimal parameters.

---

# 25. Threshold Selection

Anomaly scores will be converted into binary decisions using thresholds determined from clean validation data.

Candidate operating points may include:

- 90th percentile;
- 95th percentile;
- 97.5th percentile;
- 99th percentile.

The primary threshold will be selected according to a predefined validation criterion.

The final test set will not be used to choose the threshold.

---

# 26. PoisonShield Risk Fusion

After independently calculating integrity and anomaly evidence, the two signals will be normalized using training/validation information.

The proposed risk score is:

\[
R_i=\alpha A_i^*+\beta I_i.
\]

The values of \(\alpha\) and \(\beta\) will be determined using validation data.

This prevents the study from arbitrarily declaring one component more important than another.

---

# 27. Experimental Conditions

Four primary conditions will be evaluated.

| Condition | Integrity | Anomaly detection | Fusion |
|---|---:|---:|---:|
| E1: No Defense | No | No | No |
| E2: Integrity Only | Yes | No | No |
| E3: Anomaly Only | No | Yes | No |
| E4: PoisonShield | Yes | Yes | Yes |

This design allows the contribution of each component to be separated.

---

# 28. Controlled Poisoning Rates

The supplied benchmark files contain fixed attack compositions.

To avoid interpreting those compositions as real-world poisoning prevalence, additional controlled experiments will be conducted where feasible.

Candidate poisoning proportions are:

- 1%;
- 5%;
- 10%;
- 20%.

For each rate, poisoning samples will be sampled using controlled procedures.

The clean test distribution will remain fixed.

This allows the research to determine whether performance remains stable as the proportion of poisoned observations changes.

---

# 29. Attack-Specific Evaluation

Performance will be evaluated separately for:

1. backdoor injection;
2. Gaussian-noise injection;
3. label flipping;
4. feature poisoning;
5. missing-value injection;
6. outlier injection;
7. sample duplication.

This is necessary because overall performance can conceal severe weaknesses against individual attack mechanisms.

---

# 30. Evaluation Metrics

## 30.1 Detection metrics

The primary poisoning-detection metrics will be:

- Precision
- Recall
- F1-score
- False-positive rate
- False-negative rate
- ROC-AUC
- PR-AUC

PR-AUC will receive particular attention because poisoning detection can involve different class proportions under controlled poisoning-rate experiments.

Accuracy will not be treated as the primary metric.

---

## 30.2 Data-retention metrics

The study will also report:

- proportion of clean observations incorrectly removed;
- proportion of poisoned observations successfully removed;
- clean-data retention rate.

This directly addresses RQ5.

---

## 30.3 Computational metrics

Where feasible, the study will report:

- training time;
- inference time;
- memory requirements;
- relative computational overhead.

---

# 31. Downstream Model Evaluation

Detection performance alone is insufficient.

A defense should ultimately improve the reliability of the model trained on the screened dataset.

Therefore, a fixed downstream classifier, with Random Forest as the primary candidate, will be evaluated under:

1. clean training;
2. poisoned training without defense;
3. poisoned training after anomaly filtering;
4. poisoned training after PoisonShield filtering.

The same clean test set will be used across conditions.

For the Android multiclass dataset, evaluation will include:

- accuracy;
- macro-precision;
- macro-recall;
- macro-F1;
- balanced accuracy;
- confusion matrix.

For CIC-MalMem, binary and/or dataset-supported multiclass outcomes will be evaluated according to the predefined modeling target.

---

# 32. Statistical Analysis

The research is an experimental machine-learning study rather than an econometric study.

Consequently, traditional regression diagnostics such as:

- heteroskedasticity tests;
- autocorrelation tests;
- endogeneity tests;

are not primary analyses.

Instead, the statistical strategy will focus on:

- repeated experimental runs;
- confidence intervals;
- effect sizes;
- paired comparisons where the same sampled conditions are evaluated across methods;
- attack-specific comparisons;
- robustness across random seeds;
- sensitivity to threshold and poisoning rate.

At least three fixed random seeds will be considered, for example:

- 42;
- 123;
- 2026.

Where repeated matched experiments produce metric distributions across methods, paired statistical comparisons will be preferred.

If an omnibus comparison across multiple matched conditions is required, a repeated-measures/non-parametric approach such as Friedman testing followed by corrected paired post-hoc comparisons may be used, subject to the actual experimental structure.

Statistical significance will not be interpreted as automatically implying practical importance. Effect sizes and absolute performance differences will also be reported.

---

# 33. Robustness and Sensitivity Analysis

Robustness experiments will examine:

### Poisoning proportion

- 1%;
- 5%;
- 10%;
- 20%.

### Risk thresholds

- 90%;
- 95%;
- 97.5%;
- 99%.

### Random seeds

At least three fixed seeds.

### Attack mechanisms

All seven primary poisoning mechanisms.

### Dataset domain

- Windows memory forensics;
- Android behavioral features.

### Feature representation

Where justified:

- full screened feature space;
- reduced feature representation.

The purpose is to determine whether PoisonShield's performance is dependent on a particular configuration.

---

# 34. Model Diagnostics

Diagnostics will include:

- score distributions for clean and poisoned observations;
- confusion matrices;
- ROC curves;
- precision-recall curves;
- threshold-performance curves;
- attack-specific performance;
- clean-retention curves;
- downstream model performance;
- sensitivity to random seeds;
- sensitivity to poisoning rate.

The analysis will also examine whether legitimate observations systematically receive high risk scores.

---

# 35. Proposed Analytical Framework

The complete empirical strategy can be represented as:

```text
                   RAW SUPPLIED DATA
                           │
                           ▼
                   DATASET AUDIT
                           │
             ┌─────────────┴─────────────┐
             │                           │
       CIC-MalMem                   Android
       58,596 rows                 11,598 rows
             │                           │
             └─────────────┬─────────────┘
                           ▼
                 DATA-QUALITY SCREENING
                           │
             ┌─────────────┴─────────────┐
             │                           │
             ▼                           ▼
      TRUSTED CLEAN DATA          POISONED DATA
             │                           │
             ▼                           │
       TRAIN / VALIDATION                │
             │                           │
       ┌─────┴─────┐                     │
       ▼           ▼                     │
  Integrity    Anomaly                  │
    Layer       Layer                   │
       │           │                     │
       └─────┬─────┘                     │
             ▼                           │
        RISK FUSION                      │
             │                           │
             ▼                           │
       POISONSHIELD                     │
             │                           │
       ┌─────┴───────┐                   │
       ▼             ▼                   │
   Trusted        High Risk              │
                       │                 │
                       ▼                 │
                    FILTER               │
                       │                 │
                       └────────┬────────┘
                                ▼
                       DOWNSTREAM MODEL
                                │
                                ▼
                         CLEAN TEST SET
                                │
                                ▼
                  PERFORMANCE + ROBUSTNESS
```

---

# 36. Expected Results and Outcomes

No empirical results are claimed at the proposal stage.

The expected outcomes are instead formulated as testable expectations.

### Expected Outcome 1

The integrity layer is expected to provide strong evidence for exact correspondence with trusted records but is not expected to identify all arbitrary poisoned observations.

### Expected Outcome 2

The anomaly layer is expected to detect some poisoned observations that deviate from the clean feature distribution.

### Expected Outcome 3

Detection performance is expected to vary across poisoning mechanisms.

Attacks that create stronger feature-space deviations may be more readily identified than attacks that remain close to the clean distribution.

### Expected Outcome 4

The combination of integrity and anomaly evidence is expected to provide a more informative risk signal than either component alone when their evidence is complementary.

### Expected Outcome 5

Aggressive risk thresholds are expected to improve poisoning recall while potentially increasing false-positive removal of legitimate observations.

### Expected Outcome 6

Effective filtering is expected to improve downstream model performance relative to an identically poisoned training set without defense.

These are hypotheses and expectations, not findings.

---

# 37. Academic Contribution

The research is expected to contribute in four principal ways.

## 37.1 Conceptual contribution

The study distinguishes two often-confused security concepts:

- dataset integrity/provenance;
- sample-level anomaly detection.

This distinction provides a clearer conceptual model for training-data defense.

---

## 37.2 Methodological contribution

The research develops a modular risk-fusion framework that can combine heterogeneous evidence sources.

The framework can be decomposed into:

- integrity layer;
- anomaly layer;
- fusion layer;
- filtering layer.

This modularity facilitates ablation and comparative evaluation.

---

## 37.3 Empirical contribution

The framework will be evaluated against multiple poisoning mechanisms rather than a single attack type.

The use of two heterogeneous cybersecurity datasets provides an opportunity to evaluate whether the approach is sensitive to a particular feature domain.

---

## 37.4 Practical contribution

If supported by the empirical results, PoisonShield could provide a practical screening architecture for organizations that need to evaluate training data before model fitting.

However, the study will not claim production readiness solely from benchmark results.

---

# 38. Scope

The study focuses on:

- training-data poisoning;
- tabular cybersecurity data;
- sample-level detection;
- dataset integrity;
- unsupervised anomaly detection;
- risk-based filtering;
- downstream malware classification.

The study does not primarily address:

- model poisoning in federated learning;
- adversarial examples at inference time;
- model extraction;
- model inversion;
- membership inference;
- cryptographic guarantees against an adaptive adversary;
- universal poisoning detection;
- formal certification of PoisonShield.

---

# 39. Limitations

## 39.1 Benchmark dependence

The poisoning mechanisms are represented through supplied benchmark datasets. Their distributions may not fully represent real-world attack prevalence.

---

## 39.2 Integrity-reference dependence

Cryptographic verification requires an appropriate trusted reference.

A hash mismatch does not automatically imply poisoning.

---

## 39.3 Imperfect anomaly detection

Legitimate observations can be anomalous, and sophisticated poisoning attacks may be designed to remain close to the clean distribution.

---

## 39.4 Dataset identity limitation

The second supplied file is named `cccs_andmal2020_poisoned.csv`, but its observed structure matches CICMalDroid2020 rather than the official CCCS-CIC-AndMal-2020 dataset. This must be transparently documented in the final research report.

---

## 39.5 Source-index limitation

The supplied datasets contain `source_index`, but it is predominantly `-1`. Therefore, it cannot currently be treated as a reliable universal grouping identifier.

Consequently, group-based leakage controls based on `source_index` cannot be assumed without further evidence.

---

## 39.6 Duplicate observations

Exact duplicate observations exist in the primary poisoned datasets.

Because sample duplication is itself an attack condition, duplicates cannot simply be removed without potentially destroying the experimental signal.

---

## 39.7 Synthetic/controlled attack composition

The supplied attack labels and compositions reflect the benchmark construction and should not be interpreted as naturally occurring attack prevalence.

---

## 39.8 Generalizability

Results obtained from CIC-MalMem-2022 and the supplied Android dataset cannot automatically be generalized to all machine-learning systems or all cybersecurity domains.

---

# 40. Ethical Considerations

The study uses existing cybersecurity datasets for defensive machine-learning research.

No new malware will be created or deployed as part of the proposed experiments.

The research focuses on:

- detection;
- dataset security;
- model robustness;
- defensive evaluation.

Where datasets require citation or redistribution conditions, the corresponding dataset publications and official sources will be acknowledged.

All experimental artifacts will be handled in accordance with institutional research policies.

---

# 41. Reproducibility Strategy

The research will maintain:

- fixed random seeds;
- version-controlled source code;
- immutable raw datasets;
- experiment configuration files;
- documented preprocessing;
- saved model parameters;
- experiment logs;
- generated result tables;
- generated figures.

The project structure will separate raw data, processed data, source code, notebooks, configurations, and results.

A recommended structure is:

```text
PoisonShield/
├── data/
│   ├── raw/
│   ├── interim/
│   ├── processed/
│   └── external/
├── notebooks/
│   ├── 01_dataset_audit.ipynb
│   ├── 02_eda_malmem.ipynb
│   ├── 03_eda_android.ipynb
│   ├── 04_preprocessing.ipynb
│   ├── 05_integrity.ipynb
│   ├── 06_anomaly_detection.ipynb
│   ├── 07_poisonshield.ipynb
│   ├── 08_downstream_model.ipynb
│   ├── 09_controlled_poisoning.ipynb
│   └── 10_statistics.ipynb
├── src/
│   ├── data_audit.py
│   ├── preprocessing.py
│   ├── integrity.py
│   ├── anomaly.py
│   ├── fusion.py
│   ├── evaluation.py
│   └── experiments.py
├── results/
│   ├── tables/
│   ├── figures/
│   ├── models/
│   └── logs/
├── configs/
│   └── experiment.yaml
├── docs/
│   ├── data_dictionary.md
│   ├── decisions.md
│   └── experiment_log.md
├── requirements.txt
└── README.md
```

The recommended primary development environment is **VS Code with Python 3.11 and Jupyter notebooks**, allowing the research to combine modular Python source files with reproducible notebooks.

---

# 42. Proposed Chapter Structure

## Chapter 1 — Introduction

1. Background
2. Machine-learning training-data security
3. Data poisoning
4. Research problem
5. Research gap
6. Aim and objectives
7. Research questions
8. Hypotheses
9. Contribution
10. Scope

## Chapter 2 — Literature Review

1. Adversarial machine learning
2. Data poisoning
3. Poisoning attack mechanisms
4. Backdoor attacks
5. Anomaly-based defenses
6. Outlier detection
7. Dataset security and provenance
8. Existing research limitations
9. Research gap

## Chapter 3 — Methodology

1. Research design
2. Dataset description
3. Dataset provenance
4. Data audit
5. Variable definitions
6. Data partitioning
7. Leakage prevention
8. Preprocessing
9. Integrity layer
10. Anomaly layer
11. Risk fusion
12. Experimental conditions
13. Controlled poisoning rates
14. Downstream model
15. Evaluation metrics
16. Statistical analysis
17. Robustness analysis

## Chapter 4 — Results

1. Dataset audit
2. Descriptive statistics
3. Integrity results
4. Anomaly results
5. PoisonShield results
6. Attack-specific results
7. Ablation results
8. Threshold sensitivity
9. Controlled poisoning-rate results
10. Downstream model results
11. Statistical results
12. Robustness results

## Chapter 5 — Discussion

1. Interpretation of findings
2. Comparison with previous literature
3. RQ1 findings
4. RQ2 findings
5. RQ3 findings
6. RQ4 findings
7. RQ5 findings
8. RQ6 findings
9. Theoretical implications
10. Practical implications
11. Limitations

## Chapter 6 — Conclusion

1. Summary
2. Main contribution
3. Research implications
4. Limitations
5. Future work

---

# 43. Proposed Timeline

| Phase | Activity | Approx. duration |
|---|---|---:|
| 1 | Environment and project setup | 2–3 days |
| 2 | Dataset audit | 2–3 days |
| 3 | EDA and data-quality analysis | 4–5 days |
| 4 | Preprocessing and leakage controls | 3–4 days |
| 5 | Integrity layer | 3–4 days |
| 6 | Isolation Forest baseline | 3–4 days |
| 7 | PoisonShield fusion | 4–5 days |
| 8 | Downstream model evaluation | 3–4 days |
| 9 | Controlled poisoning experiments | 4–6 days |
| 10 | Statistical and robustness analysis | 4–5 days |
| 11 | Figures and tables | 3–4 days |
| 12 | Discussion and final paper | 7–10 days |

The actual schedule may be adjusted according to computational requirements and experimental findings.

---

# 44. Expected Final Tables and Figures

The final research report is expected to contain:

### Table 1
Dataset characteristics.

### Table 2
Attack distribution.

### Table 3
Variable and measurement definitions.

### Table 4
Data-quality assessment.

### Table 5
Baseline anomaly-detection performance.

### Table 6
Integrity-only versus anomaly-only versus PoisonShield.

### Table 7
Attack-specific performance.

### Table 8
Clean-data retention and false-positive removal.

### Table 9
Downstream model performance.

### Table 10
Ablation study.

### Table 11
Controlled poisoning-rate robustness.

### Table 12
Statistical comparisons and effect sizes.

Figures may include:

- class/attack distributions;
- missingness;
- anomaly-score distributions;
- ROC curves;
- precision-recall curves;
- threshold sensitivity;
- poisoning-rate sensitivity;
- downstream performance comparison;
- PoisonShield conceptual architecture.

---

# 45. References

1. Biggio, B., Nelson, B., & Laskov, P. (2012). **Poisoning Attacks against Support Vector Machines.** *Proceedings of the 29th International Conference on Machine Learning (ICML)*.

2. Wang, Z., Ma, J., Wang, X., Hu, J., Qin, Z., & Ren, K. (2022). **Threats to Training: A Survey of Poisoning Attacks and Defenses on Machine Learning Systems.** *ACM Computing Surveys, 55*(7), Article 134. https://doi.org/10.1145/3538707.

3. Goldblum, M., Tsipras, D., Xie, C., Chen, X., Schwarzschild, A., Song, D., Madry, A., Li, B., & Goldstein, T. (2023). **Dataset Security for Machine Learning: Data Poisoning, Backdoor Attacks, and Defenses.** *IEEE Transactions on Pattern Analysis and Machine Intelligence, 45*(2), 1563–1580. https://doi.org/10.1109/TPAMI.2022.3162397.

4. Gu, T., Dolan-Gavitt, B., & Garg, S. (2017). **BadNets: Identifying Vulnerabilities in the Machine Learning Model Supply Chain.** arXiv:1708.06733.

5. Tran, B., Li, J., & Madry, A. (2018). **Spectral Signatures in Backdoor Attacks.** *Advances in Neural Information Processing Systems (NeurIPS).*

6. Chen, B., Carvalho, W., Baracaldo, N., Ludwig, H., Edwards, B., Lee, T., Molloy, I., & Srivastava, B. (2018). **Detecting Backdoor Attacks on Deep Neural Networks by Activation Clustering.** arXiv:1811.03728.

7. Steinhardt, J., Koh, P. W., & Liang, P. (2017). **Certified Defenses for Data Poisoning Attacks.** *Advances in Neural Information Processing Systems (NeurIPS).*

8. Liu, F. T., Ting, K. M., & Zhou, Z.-H. (2008). **Isolation Forest.** *Proceedings of the 2008 IEEE International Conference on Data Mining*, 413–422.

9. Carrier, T., Victor, P., Tekeoglu, A., & Lashkari, A. H. (2022). **Detecting Obfuscated Malware using Memory Feature Engineering.** *The 8th International Conference on Information Systems Security and Privacy (ICISSP).* Dataset information is available through the Canadian Institute for Cybersecurity.

10. Mahdavifar, S., Abdul Kadir, A. F., Fatemi, R., Alhadidi, D., & Ghorbani, A. A. (2020). **Dynamic Android Malware Category Classification using Semi-Supervised Deep Learning.** *18th IEEE International Conference on Dependable, Autonomic and Secure Computing (DASC).* This is the primary publication associated with the CICMalDroid2020 dataset structure used by the supplied 11,598-row Android file.

11. Mahdavifar, S., Alhadidi, D., & Ghorbani, A. A. (2022). **Effective and Efficient Hybrid Android Malware Classification Using Pseudo-Label Stacked Auto-Encoder.** *Journal of Network and Systems Management, 30*(1), 1–34.

12. Canadian Institute for Cybersecurity. **CCCS-CIC-AndMal-2020.** University of New Brunswick. The official dataset description reports 400,000 Android applications, including 200,000 benign and 200,000 malware samples.

---

# 46. Final Research Design Check

| Component | Status | Assessment |
|---|---|---|
| Research topic | ✓ | Clearly focused on ML training-data poisoning |
| PoisonShield concept | ✓ | Coherent and technically feasible |
| Research problem | ✓ | Directly connected to poisoning-defense limitations |
| Research gap | ✓ | Conservative; based on integration and evaluation rather than an exaggerated novelty claim |
| Objectives | ✓ | Directly operationalizable |
| Research questions | ✓ | Testable using the available benchmark labels |
| Hypotheses | ✓ | Empirically testable |
| Integrity layer | ✓ | Feasible as reference-consistency verification |
| Anomaly layer | ✓ | Isolation Forest is appropriate as the primary unsupervised baseline |
| Risk fusion | ✓ | Requires validation-based parameter selection |
| CIC-MalMem data | ✓ | Consistent with supplied file and published dataset characteristics |
| Android data identity | **Corrected** | Supplied file matches CICMalDroid2020, not official 400K CCCS-CIC-AndMal-2020 |
| Attack variables | ✓ | Seven primary poisoning mechanisms are available |
| Class/label variables | ✓ | Available for evaluation, excluded from anomaly predictors |
| Missing-value attack | ✓ | Supported; preprocessing must preserve missingness evidence |
| Duplication attack | ✓ | Supported; duplicates must not be blindly deleted |
| Class-imbalance audit | ✓ | Treated as a separate experimental condition |
| Controlled poisoning rates | ✓ | Feasible through controlled sampling |
| Downstream evaluation | ✓ | Feasible using a fixed classifier and clean test set |
| Statistical methodology | ✓ | ML-appropriate rather than unnecessary econometric diagnostics |
| Cross-dataset evaluation | ✓ | Appropriate because datasets are heterogeneous |
| Novelty claim | ✓ | Conservative and defensible |
| Empirical results | Not yet claimed | Correctly left as expected outcomes |
| Reproducibility | ✓ | Supported through fixed seeds, configs, logs and version control |
| Overall feasibility | **Yes** | Feasible with the supplied data and proposed implementation |

### Final conclusion

The research design is **internally coherent and feasible**, with one crucial dataset-identification correction: the supplied `cccs_andmal2020_poisoned.csv` should **not** be described as the official CCCS-CIC-AndMal-2020 dataset unless its provenance is independently verified. Its observed structure matches CICMalDroid2020, whose official documentation reports exactly 11,598 samples and a 470-feature CSV.

With that correction, the study has a strong logical chain:

**training-data poisoning problem → limitations of single-signal defenses → complementary integrity and anomaly evidence → PoisonShield → controlled multi-attack evaluation → ablation → robustness → downstream model recovery.**

Most importantly, the proposal does **not** claim that hashing detects poisoning universally, does **not** use attack labels as detector inputs, does **not** treat benchmark attack prevalence as real-world poisoning prevalence, and does **not** claim empirical superiority before the experiments are performed.

The next implementation stage should therefore begin with **dataset auditing and provenance verification**, followed by preprocessing, integrity verification, Isolation Forest, risk fusion, and finally the controlled experiments described above.