# PoisonShield
## Complete Step-by-Step Research Implementation Plan

### Research Title

**PoisonShield: A Multi-Layer Defense Against Machine Learning Data Poisoning Using Dataset Integrity Verification, Sample-Level Anomaly Detection, and Risk-Based Evidence Fusion**

---

# 0. Locked Research Design

Before writing any code, the following decisions are considered the current research baseline.

### Core research problem

Determine whether combining:

1. dataset integrity/provenance evidence,
2. sample-level anomaly detection, and
3. risk-based evidence fusion

provides a measurable advantage over individual defense mechanisms against training-data poisoning.

The proposal explicitly defines the framework as three layers: trust/integrity, behavioral anomaly detection, and risk-based decision/mitigation.

---

## Research Questions

**RQ1:** How effectively can a watermark/integrity mechanism verify the trusted state or provenance of a training dataset?

**RQ2:** How effectively can anomaly detection identify poisoned training samples in the selected feature space?

**RQ3:** Does combining integrity/provenance verification with anomaly detection improve poisoning detection compared with either mechanism independently?

**RQ4:** How does PoisonShield perform under different poisoning attack types and poisoning rates?

**RQ5:** What is the effect of PoisonShield filtering on clean accuracy, attack success rate, and downstream model performance?

**RQ6:** What trade-offs arise between detection effectiveness, false-positive rate, computational overhead, and data retention?

These are the finalized research questions in the proposal. 
---

## Hypotheses

### H1 — Primary

The combined PoisonShield framework will detect and mitigate a greater proportion of poisoning attacks than either the integrity layer or anomaly-detection layer independently.

### H2

Adding sample-level anomaly detection to integrity/provenance verification will improve poisoned-sample recall while maintaining an acceptable false-positive rate on clean data.

### H3

Filtering samples identified as high-risk by PoisonShield will reduce poisoning-induced degradation in downstream model performance relative to training on the unfiltered poisoned dataset.

The proposal explicitly treats these as empirical hypotheses that may be supported, partially supported, or rejected.

---

# Phase 1 — Research Setup

## Goal

Create a completely reproducible research environment before touching the analysis.

---

## Step 1. Freeze the research protocol

Create:

```text
docs/research_protocol.md
```

Record:

- title
- RQs
- hypotheses
- datasets
- attack types
- variables
- preprocessing rules
- models
- metrics
- thresholds
- random seeds
- experimental conditions
- statistical comparison strategy

### Important

Do **not** change the methodology simply because an early result looks poor.

Any later methodological modification must be documented as a research decision.

---

## Step 2. Define the unit of analysis

The primary unit is:

> **One training-data observation/sample.**

For the detection task:

```text
Observation → clean or poisoned
```

For downstream classification:

```text
Observation → original ML classification task
```

---

## Step 3. Define variables

### Metadata/evaluation variables

| Variable | Purpose | Model predictor? |
|---|---|---|
| `attack_type` | identifies attack | NO |
| `original_label` | reference label | NO |
| `Class` | observed task label | NO for anomaly model |
| `source_index` | provenance/reference information | NO initially |
| `poisoned` | derived evaluation target | NO |

### Main derived evaluation target

```python
poisoned = 1 if attack_type != "clean"
poisoned = 0 if attack_type == "clean"
```

### Detection variables

- anomaly score
- anomaly flag
- integrity match
- integrity risk
- normalized anomaly score
- fusion risk score
- PoisonShield flag

### Downstream variables

- original classification label
- predicted class
- clean-test performance
- poisoned-training performance
- filtered-training performance

---

# Step 4. Define experimental conditions

The primary comparison is:

| Condition | Description |
|---|---|
| E1 | No Defense |
| E2 | Integrity Only |
| E3 | Anomaly Only |
| E4 | PoisonShield |

This comparison is central to the proposal.

Later add:

- ablation
- attack-specific analysis
- poisoning-rate analysis
- sensitivity analysis

---

# Step 5. Define random seeds

Use:

```text
42
123
2026
```

Every stochastic experiment must record its seed.

---

# Step 6. Software environment

Use **VS Code + Python 3.11** as the primary environment.

Recommended packages:

```text
pandas
numpy
scipy
scikit-learn
matplotlib
seaborn
joblib
pyyaml
jupyter
statsmodels
```

Why Python?

- Both datasets are CSV-based.
- Isolation Forest and Random Forest are directly supported.
- preprocessing can be placed inside reproducible pipelines.
- experiments can be automated.
- statistical testing can be performed in the same environment.
- results can be exported programmatically.

GPU is **not required** for the primary Isolation Forest + Random Forest methodology.

---

# Step 7. Create the project structure

```text
PoisonShield/
│
├── data/
│   ├── raw/
│   ├── interim/
│   ├── processed/
│   └── external/
│
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
│
├── src/
│   ├── data_audit.py
│   ├── preprocessing.py
│   ├── integrity.py
│   ├── anomaly.py
│   ├── fusion.py
│   ├── evaluation.py
│   └── experiments.py
│
├── configs/
│   └── experiment.yaml
│
├── results/
│   ├── tables/
│   ├── figures/
│   ├── models/
│   └── logs/
│
├── docs/
│   ├── research_protocol.md
│   ├── data_dictionary.md
│   ├── decisions.md
│   └── experiment_log.md
│
├── requirements.txt
└── README.md
```

---

# Step 8. Establish the raw-data rule

Never modify:

```text
data/raw/
```

The four original datasets remain untouched.

Create derived versions under:

```text
data/interim/
data/processed/
```

This gives us an auditable data lineage.

---

# Phase 1 Expected Output

You should finish Phase 1 with:

```text
☑ Research protocol
☑ Python environment
☑ Project structure
☑ Raw datasets copied
☑ requirements.txt
☑ experiment configuration
☑ experiment log
```

### STOP if:

- Python environment does not work.
- files are missing.
- filenames differ unexpectedly.
- you are tempted to modify raw CSVs.

---

# Phase 2 — Dataset Preparation and Audit

# Goal

Understand exactly what is inside the supplied datasets **before modeling**.

The supplied Android file contains system-call/binder/composite-behavior features and the metadata fields `Class`, `attack_type`, `original_label`, and `source_index`.

---

# Step 9. Copy datasets into `data/raw`

Place:

```text
data/raw/cic_malmem2022_poisoned.csv
data/raw/cic_malmem2022_removed_audit.csv

data/raw/cccs_andmal2020_poisoned.csv
data/raw/cccs_andmal2020_removed_audit.csv
```

Do not rename the original Android filename.

The filename is part of provenance.

---

# Step 10. Load the datasets

Create:

```text
notebooks/01_dataset_audit.ipynb
```

First cell:

```python
from pathlib import Path
import pandas as pd

ROOT = Path("..")

malmem_path = ROOT / "data/raw/cic_malmem2022_poisoned.csv"
android_path = ROOT / "data/raw/cccs_andmal2020_poisoned.csv"

malmem = pd.read_csv(malmem_path)
android = pd.read_csv(android_path)

print("MalMem:", malmem.shape)
print("Android:", android.shape)
```

### Expected

Based on our audit:

```text
MalMem ≈ 58,596 × 59
Android ≈ 11,598 × 474
```

If the shapes differ:

**STOP.**

Do not continue until we determine why.

---

# Step 11. Inspect columns

```python
print(malmem.columns.tolist())
print(android.columns.tolist())
```

Then:

```python
print(malmem.dtypes.value_counts())
print(android.dtypes.value_counts())
```

---

# Step 12. Identify metadata

Run:

```python
metadata_cols = [
    "Class",
    "attack_type",
    "original_label",
    "source_index"
]

print(
    [c for c in metadata_cols if c in malmem.columns]
)

print(
    [c for c in metadata_cols if c in android.columns]
)
```

### Decision

These variables are retained for:

- evaluation
- attack analysis
- provenance
- label comparison

but are **not anomaly predictors**.

---

# Step 13. Create poisoning indicator

```python
malmem["poisoned"] = (
    malmem["attack_type"] != "clean"
).astype(int)

android["poisoned"] = (
    android["attack_type"] != "clean"
).astype(int)
```

Check:

```python
print(malmem["poisoned"].value_counts())
print(android["poisoned"].value_counts())
```

---

# Step 14. Verify attack distribution

```python
print(malmem["attack_type"].value_counts())
print(android["attack_type"].value_counts())
```

The previously audited structure is:

### MalMem

- clean: 42,202
- seven attack types: 2,342 each

### Android

- clean: 6,117
- seven attack types: 783 each

These equal-sized attack blocks indicate a **controlled benchmark construction**, not real-world poisoning prevalence.

This distinction must be explicitly stated in the thesis.

---

# Step 15. Verify labels

```python
for name, df in {
    "MalMem": malmem,
    "Android": android
}.items():

    mismatch = (
        df["Class"] != df["original_label"]
    ).sum()

    print(name, "label mismatches:", mismatch)
```

Previously observed:

- MalMem: 4,684
- Android: 1,566

These correspond to the controlled label-changing attacks.

---

# Step 16. Missing-value audit

```python
missing = malmem.isna().sum()
print(missing[missing > 0])

missing_android = android.isna().sum()
print(missing_android[missing_android > 0])
```

Do **not** immediately impute.

Why?

Because `missing_value_injection` is itself an attack.

If you replace the missing values before measuring detection, you may destroy evidence that the defense is supposed to detect.

---

# Step 17. Duplicate audit

```python
print("MalMem duplicates:", malmem.duplicated().sum())
print("Android duplicates:", android.duplicated().sum())
```

Previously observed:

- MalMem: 170 exact duplicate rows
- Android: 33 exact duplicate rows

Do **not** remove these globally.

`sample_duplication` is an experimental attack.

---

# Step 18. Constant-feature audit

```python
def constant_columns(df):
    return [
        c for c in df.columns
        if df[c].nunique(dropna=False) <= 1
    ]

print(constant_columns(malmem))
print(constant_columns(android))
```

### Decision

Remove constant predictors from the **modeling feature matrix**.

Do not delete them from raw data.

Document:

```text
feature excluded because variance = 0
```

---

# Step 19. Impossible-value audit

Because these datasets are primarily count/frequency-style features, inspect:

```python
numeric = malmem.select_dtypes(include="number")

print((numeric < 0).sum().sort_values(ascending=False).head())
```

Repeat for Android.

### Decision

If negative values occur:

1. identify feature;
2. determine whether negative is theoretically possible;
3. do not automatically replace it;
4. document the decision.

If none occur, record:

> No negative values were detected in numeric predictors.

---

# Step 20. Outlier audit

Do **not** automatically remove statistical outliers.

For each numeric feature:

```python
q1 = numeric.quantile(0.25)
q3 = numeric.quantile(0.75)
iqr = q3 - q1

outlier_count = (
    (numeric < q1 - 1.5 * iqr) |
    (numeric > q3 + 1.5 * iqr)
).sum()

print(
    outlier_count.sort_values(
        ascending=False
    ).head(20)
)
```

### Important

An outlier may be:

- legitimate malware behavior,
- a natural rare observation,
- an injected outlier attack.

Therefore:

> **Outlier ≠ erroneous observation.**

Keep them unless there is evidence of data corruption.

---

# Step 21. Dataset identity verification

This is especially important for:

```text
cccs_andmal2020_poisoned.csv
```

Our audit established that its 11,598-row structure and 470/471-feature system-call/binder characteristics correspond to **CICMalDroid2020**, not the official 400,000-sample CCCS-CIC-AndMal-2020 dataset.

Therefore the thesis should transparently state:

> The supplied Android poisoning file was retained under its original filename for provenance but was identified structurally as corresponding to CICMalDroid2020 rather than the official CCCS-CIC-AndMal-2020 dataset.

Do **not** claim that the 400,000-sample CCCS-CIC-AndMal-2020 dataset was used.

This is a critical publication-integrity issue.

---

# Step 22. Create the data dictionary

Create:

```text
docs/data_dictionary.md
```

For every variable record:

```text
Name
Dataset
Type
Meaning
Role
Allowed/observed values
Used by anomaly model?
Used by integrity layer?
Used for evaluation?
Transformation
```

---

# Phase 2 Decision Gate

Proceed only if:

- dimensions verified;
- metadata verified;
- attack labels verified;
- missingness documented;
- duplicates documented;
- constants documented;
- feature types verified;
- Android dataset identity documented.

---

# Phase 3 — Exploratory Data Analysis

# Goal

Understand the structure of legitimate and poisoned observations before implementing the defense.

---

# Step 23. Descriptive statistics

```python
feature_cols = [
    c for c in malmem.select_dtypes(include="number").columns
    if c not in ["source_index", "poisoned"]
]

malmem[feature_cols].describe().T
```

Save:

```text
results/tables/table_descriptive_malmem.csv
```

Repeat for Android.

---

# Step 24. Attack-frequency figure

```python
import matplotlib.pyplot as plt

malmem["attack_type"].value_counts().plot(kind="bar")

plt.xlabel("Attack Type")
plt.ylabel("Number of Observations")
plt.title("Poisoning Attack Distribution — CIC-MalMem")
plt.xticks(rotation=45)
plt.tight_layout()

plt.savefig(
    "../results/figures/fig_attack_distribution_malmem.png",
    dpi=300
)

plt.show()
```

Purpose:

- documents experimental composition;
- answers part of RQ4.

---

# Step 25. Class distribution

```python
print(
    pd.crosstab(
        malmem["attack_type"],
        malmem["Class"]
    )
)
```

This allows us to identify whether specific attacks disproportionately affect particular classes.

---

# Step 26. Missingness visualization

```python
missing_pct = (
    malmem.isna().mean() * 100
).sort_values(ascending=False)

print(missing_pct[missing_pct > 0])
```

Plot only features with missingness.

Purpose:

- establish whether missing-value attacks are observable;
- avoid hiding attack evidence.

---

# Step 27. Distribution plots

Do not create 55/471 plots immediately.

First identify:

- highest variance;
- highest skew;
- highest missingness;
- features with substantial attack-group differences.

Then plot selected variables.

---

# Step 28. Group comparison

For selected features:

```python
malmem.groupby("attack_type")[feature_cols].mean()
```

Also:

```python
malmem.groupby("attack_type")[feature_cols].median()
```

The mean/median comparison is useful because poisoning may generate extreme values.

---

# Step 29. Correlation analysis

Correlation is **descriptive**, not causal.

```python
corr = malmem[feature_cols].corr()
```

For visualization:

```python
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 10))
sns.heatmap(
    corr,
    cmap="coolwarm",
    center=0
)
plt.tight_layout()
plt.show()
```

Because MalMem has relatively few features, a full heatmap is reasonable.

For Android's ~471 predictors, use:

- top correlated features;
- clustered heatmap;
- or summary statistics.

Do not produce an unreadable 471×471 figure unless necessary.

---

# Step 30. Time/trend analysis

The datasets do not provide a valid time-series dimension for the poisoning experiment.

Therefore:

> **No time-series trend analysis, autocorrelation analysis, or temporal regression is required.**

Do not invent a time dimension from row order.

---

# Phase 3 Outputs

Essential:

1. Dataset composition table
2. Descriptive statistics
3. Attack distribution
4. Class distribution
5. Missingness table
6. Duplicate table
7. Selected distribution figures
8. Correlation summary
9. Group comparisons

---

# Phase 4 — Variable and Measurement Validation

## Goal

Ensure every research question has a measurable empirical counterpart.

| RQ | Outcome | Main Evidence | Evaluation |
|---|---|---|---|
| RQ1 | Integrity detection | hash/manifest mismatch | precision, recall, F1 |
| RQ2 | Poison detection | anomaly score | ROC-AUC, PR-AUC, recall, F1 |
| RQ3 | Combined detection | fusion risk | compare E2/E3/E4 |
| RQ4 | attack robustness | attack-specific detection | metrics by attack/rate |
| RQ5 | downstream performance | model performance | clean-test metrics |
| RQ6 | trade-offs | FPR, retention, runtime | comparative analysis |

---

# Hypothesis Mapping

## H1

**Independent variable:**

Defense method:

```text
Integrity
Anomaly
PoisonShield
```

**Dependent variables:**

- recall
- precision
- F1
- PR-AUC
- FPR

Primary comparison:

```text
PoisonShield vs Integrity Only
PoisonShield vs Anomaly Only
```

---

## H2

IV:

```text
Defense architecture
```

DV:

```text
poison recall
clean-data false-positive rate
```

Primary analysis:

paired comparison across identical attack conditions/seeds.

---

## H3

IV:

```text
training condition
```

Levels:

```text
Clean
Poisoned/no defense
Anomaly-filtered
PoisonShield-filtered
```

DV:

```text
clean test performance
```

Potential additional DV:

```text
attack success rate
```

---

# Important Statistical Design Correction

This project is **not an econometric study**.

Therefore we do **not** need to force:

- heteroskedasticity tests,
- autocorrelation tests,
- endogeneity tests,
- instrumental variables,
- panel regression,
- time-series regression.

Those diagnostics are appropriate for certain regression/econometric designs, not for our primary classification/anomaly-detection experiment.

We will instead use:

- repeated experimental comparisons;
- confidence intervals;
- effect sizes;
- paired statistical tests where appropriate;
- robustness/sensitivity analysis.

---

# Phase 5 — Preprocessing

# Goal

Prepare data without leaking poisoning information into the detector.

---

# Step 31. Define predictor columns

```python
EXCLUDED = {
    "Class",
    "attack_type",
    "original_label",
    "source_index",
    "poisoned"
}

numeric_cols = malmem.select_dtypes(
    include="number"
).columns

feature_cols = [
    c for c in numeric_cols
    if c not in EXCLUDED
]
```

Then remove constants:

```python
constant_cols = [
    c for c in feature_cols
    if malmem[c].nunique(dropna=False) <= 1
]

feature_cols = [
    c for c in feature_cols
    if c not in constant_cols
]
```

---

# Step 32. Establish clean reference data

```python
clean = malmem[
    malmem["attack_type"] == "clean"
].copy()
```

The same concept applies to Android.

---

# Step 33. Train/validation/test split

The clean data should be divided into:

```text
Clean Reference/Training
Clean Validation
Clean Test
```

Recommended:

```text
70% train
15% validation
15% test
```

The clean test set must remain untouched until final evaluation.

---

# Critical leakage rule

Do **not** use attack labels to train the anomaly detector.

Do not fit:

- imputer;
- scaler;
- feature-selection thresholds;
- anomaly detector;
- fusion normalization;
- fusion weights;
- risk threshold

using the final test set.

---

# Step 34. Preprocessing pipeline

For continuous numeric predictors:

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler

preprocessor = Pipeline([
    (
        "imputer",
        SimpleImputer(strategy="median")
    ),
    (
        "scaler",
        StandardScaler()
    )
])
```

Fit only on clean training data.

---

# Missing-value attack handling

There is a subtle but important issue.

If missing values are one of the attack types, we need two representations:

### Detection representation

Preserve the fact that values were missing.

### Model representation

Impute only when required by the downstream algorithm.

Therefore, before imputation calculate:

```text
row_missing_count
feature_missing_indicator
```

but **do not use `attack_type` itself**.

A useful derived variable is:

```python
df["missing_count"] = df[feature_cols].isna().sum(axis=1)
```

This is legitimate because it derives directly from observed data.

However, the thesis should explain that missingness is both a data-quality characteristic and a potential attack signal.

---

# Phase 6 — Integrity/Provenance Layer

# Goal

Determine whether the observed training dataset corresponds to a trusted reference state.

The proposal describes this as the trust layer.

---

# Critical implementation correction

Do **not** implement integrity using only:

```text
hash(features)
```

because label flipping would not necessarily be detected.

Instead the trusted manifest should incorporate:

```text
feature values
+
observed label
```

and maintain **multiplicity/count information**.

This is also necessary for the sample-duplication attack.

---

# Step 35. Canonical row representation

```python
import hashlib
import pandas as pd

def canonical_value(x):
    if pd.isna(x):
        return "<NA>"
    return str(x)

def row_hash(row, columns):
    content = "|".join(
        canonical_value(row[c])
        for c in columns
    )
    return hashlib.sha256(
        content.encode("utf-8")
    ).hexdigest()
```

---

# Step 36. Create trusted manifest

Use:

```python
integrity_columns = feature_cols + ["Class"]

clean["row_hash"] = clean.apply(
    lambda r: row_hash(
        r,
        integrity_columns
    ),
    axis=1
)
```

Then:

```python
trusted_manifest = (
    clean["row_hash"]
    .value_counts()
    .rename("trusted_count")
    .reset_index()
)

trusted_manifest.columns = [
    "row_hash",
    "trusted_count"
]
```

Save:

```text
results/tables/trusted_manifest.csv
```

---

# Step 37. Integrity evaluation

For each observed dataset:

1. calculate row hashes;
2. compare against trusted manifest;
3. compare multiplicities;
4. detect additions;
5. detect removals;
6. detect modifications.

This produces:

```text
integrity_match
integrity_risk
```

But also:

```text
added_records
removed_records
modified_records
multiplicity_anomalies
```

---

# Integrity-only experiments

Run separately:

### I1

Untouched dataset.

### I2

Modified feature.

### I3

Modified label.

### I4

Added observation.

### I5

Removed observation.

### I6

Duplicated observation.

This demonstrates what the integrity mechanism can and cannot detect.

---

# Important interpretation

Integrity verification is **not equivalent to poisoning detection**.

It answers:

> "Does this observed data state correspond to the trusted reference?"

It does not inherently answer:

> "Is this sample malicious?"

That distinction is central to the theoretical framework.

---

# Phase 7 — Sample-Level Anomaly Detection

# Goal

Detect observations that are statistically unusual relative to clean training data.

Primary algorithm:

> **Isolation Forest**

This is the locked primary anomaly detector.

---

# Step 38. Build clean-training matrix

```python
X_clean_train = clean_train[feature_cols]
```

Do not include:

```text
attack_type
Class
original_label
source_index
poisoned
integrity_match
integrity_risk
```

---

# Step 39. Create Isolation Forest pipeline

```python
from sklearn.ensemble import IsolationForest
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler

anomaly_pipeline = Pipeline([
    (
        "imputer",
        SimpleImputer(strategy="median")
    ),
    (
        "scaler",
        StandardScaler()
    ),
    (
        "model",
        IsolationForest(
            n_estimators=300,
            contamination="auto",
            random_state=42,
            n_jobs=-1
        )
    )
])
```

---

# Step 40. Train only on clean data

```python
anomaly_pipeline.fit(
    X_clean_train
)
```

---

# Step 41. Generate anomaly scores

```python
scores = -anomaly_pipeline.decision_function(
    X_all
)
```

Higher score:

```text
more anomalous
```

Store:

```python
df["anomaly_score"] = scores
```

---

# Step 42. Select threshold

Do **not** simply assume 0.5.

Instead derive threshold from clean validation data.

Initial sensitivity analysis:

```text
90th percentile
95th percentile
97.5th percentile
99th percentile
```

For example:

```python
threshold = np.quantile(
    clean_validation_scores,
    0.95
)
```

Then:

```python
df["anomaly_flag"] = (
    df["anomaly_score"] >= threshold
).astype(int)
```

---

# Step 43. Evaluate anomaly detector

Required:

```text
Precision
Recall
F1
FPR
FNR
ROC-AUC
PR-AUC
```

Code:

```python
from sklearn.metrics import (
    precision_score,
    recall_score,
    f1_score,
    roc_auc_score,
    average_precision_score,
    confusion_matrix
)

y_true = df["poisoned"]
y_score = df["anomaly_score"]
y_pred = df["anomaly_flag"]

print(
    "Precision:",
    precision_score(y_true, y_pred)
)

print(
    "Recall:",
    recall_score(y_true, y_pred)
)

print(
    "F1:",
    f1_score(y_true, y_pred)
)

print(
    "ROC-AUC:",
    roc_auc_score(y_true, y_score)
)

print(
    "PR-AUC:",
    average_precision_score(
        y_true,
        y_score
    )
)

print(confusion_matrix(y_true, y_pred))
```

---

# Phase 8 — PoisonShield Fusion

# Goal

Combine complementary evidence.

The conceptual structure is:

```text
Integrity Evidence
        +
Anomaly Evidence
        ↓
Risk Fusion
        ↓
Trusted / Review / High Risk
```

This follows the three-layer conceptual framework established in the proposal.

---

# Step 44. Normalize anomaly score

Fit normalization using training/validation information only.

```python
from sklearn.preprocessing import MinMaxScaler

score_scaler = MinMaxScaler()

score_scaler.fit(
    clean_validation_scores.reshape(-1, 1)
)

df["anomaly_norm"] = score_scaler.transform(
    df[["anomaly_score"]]
)
```

The exact implementation can later be refined to avoid extrapolation artifacts.

---

# Step 45. Define risk score

Conceptually:

```text
Risk =
α × anomaly evidence
+
β × integrity evidence
```

where:

```text
α + β = 1
```

Do **not** immediately declare:

```text
α = 0.7
β = 0.3
```

to be optimal.

That was only an initial implementation value.

---

# Step 46. Tune α and β

Use validation data.

Example grid:

```text
α = 0.0
0.1
0.2
...
1.0
```

and:

```text
β = 1 - α
```

Choose the combination according to a **predefined validation criterion**.

Recommended primary criterion:

> maximize F1 subject to a predefined acceptable clean-data false-positive rate.

This prevents optimizing solely for recall.

---

# Step 47. Select PoisonShield threshold

Again use validation data.

Test:

```text
90%
95%
97.5%
99%
```

Then freeze the selected threshold before touching the clean test set.

---

# Step 48. Generate final PoisonShield risk

```python
risk_score = (
    alpha * anomaly_norm
    +
    beta * integrity_risk
)

df["risk_score"] = risk_score

df["poisonshield_flag"] = (
    df["risk_score"] >= risk_threshold
).astype(int)
```

---

# Phase 9 — Main Experimental Comparison

Run:

## E1 — No Defense

Train downstream model on poisoned data.

No filtering.

---

## E2 — Integrity Only

Remove/quarantine samples identified by integrity.

Train downstream model.

---

## E3 — Anomaly Only

Remove/quarantine anomaly-flagged observations.

Train downstream model.

---

## E4 — PoisonShield

Remove/quarantine high-risk observations.

Train downstream model.

---

# Phase 10 — Downstream Model

Use:

> **Random Forest**

as the primary downstream classifier.

Reason:

- handles nonlinear relationships;
- works well with tabular data;
- does not require linearity;
- provides a stable benchmark;
- avoids making the downstream model itself the research contribution.

---

# Step 49. Train clean baseline

Train Random Forest on:

```text
clean training data
```

Evaluate on:

```text
untouched clean test
```

This establishes:

> Clean-data performance ceiling/baseline.

---

# Step 50. Train poisoned baseline

Train Random Forest on:

```text
poisoned training data
```

Evaluate on:

```text
same clean test
```

This measures poisoning-induced degradation.

---

# Step 51. Train filtered models

Repeat for:

```text
Integrity-filtered
Anomaly-filtered
PoisonShield-filtered
```

Always evaluate using the **same clean test set**.

---

# Required downstream metrics

Depending on the classification structure:

```text
Accuracy
Macro Precision
Macro Recall
Macro F1
Confusion Matrix
```

For imbalanced classes, prioritize:

> Macro F1

over accuracy alone.

---

# Phase 11 — Attack-Specific Analysis

This is essential for RQ4.

Run separately for:

```text
backdoor_injection
gaussian_noise_injection
label_flipping
feature_poisoning
missing_value_injection
outlier_injection
sample_duplication
```

For every attack calculate:

```text
Precision
Recall
F1
PR-AUC
FPR
FNR
```

Create:

```text
results/tables/attack_specific_detection.csv
```

---

# Phase 12 — Controlled Poisoning Rates

The native dataset attack composition must not be interpreted as real-world prevalence.

Construct controlled rates:

```text
1%
5%
10%
20%
```

For each rate:

```text
clean data
+
selected proportion of poisoning
```

Keep:

```text
clean test = fixed
```

---

# Experimental matrix

For each:

```text
Dataset
×
Attack Type
×
Poisoning Rate
×
Defense
×
Seed
```

run the experiment.

For example:

```text
MalMem
feature poisoning
10%
PoisonShield
seed 42
```

---

# Phase 13 — Robustness and Sensitivity

## Threshold sensitivity

Test:

```text
90%
95%
97.5%
99%
```

---

## Seed sensitivity

```text
42
123
2026
```

---

## Weight sensitivity

Test:

```text
α = 0.0 → 1.0
β = 1 - α
```

---

## Attack sensitivity

Evaluate separately by attack.

---

## Dataset sensitivity

Compare:

```text
MalMem
Android
```

---

# Phase 14 — Ablation Study

The proposal specifically calls for ablation to determine whether improvements arise from the combined architecture.

Run:

### A1

Integrity only.

### A2

Anomaly only.

### A3

Integrity + anomaly.

### A4

Integrity + anomaly without optimized weighting.

### A5

PoisonShield with each component removed.

The key question:

> Does the combination actually provide additional information?

If:

```text
PoisonShield ≈ anomaly only
```

then the integrity layer may be redundant for that attack.

That is still a scientifically valuable finding.

---

# Phase 15 — Statistical Analysis

# Goal

Determine whether observed differences are consistent and meaningful rather than relying on one impressive result.

---

## Step 52. Build paired experiment results

Every experimental result should have:

```text
dataset
attack_type
poisoning_rate
seed
defense
metric
value
```

Example:

```text
MalMem | feature_poisoning | 10% | 42 | PoisonShield | F1 | 0.84
```

---

# Step 53. Confidence intervals

Report:

```text
mean
standard deviation
95% confidence interval
```

where repeated runs permit it.

---

# Step 54. Paired comparisons

Because the same:

- dataset;
- attack;
- poisoning rate;
- seed

can be evaluated under multiple defenses, comparisons are naturally paired.

Primary comparisons:

```text
PoisonShield vs Anomaly
PoisonShield vs Integrity
PoisonShield vs No Defense
```

---

# Step 55. Multiple-method comparison

When enough matched repeated conditions exist:

> Friedman test

can be used for comparing several defense methods across matched experimental conditions.

If significant:

> corrected pairwise Wilcoxon signed-rank tests.

Use an appropriate multiple-comparison correction such as Holm.

---

# Step 56. Effect sizes

Do not report only:

```text
p < 0.05
```

Also report:

```text
absolute improvement
relative improvement
effect size
confidence interval
```

Example:

> PoisonShield increased mean recall by 8.4 percentage points relative to anomaly-only detection.

This is much more informative than simply saying:

> The result was statistically significant.

---

# Statistical significance vs practical significance

Always distinguish:

### Statistical significance

Whether the difference is unlikely under the null hypothesis.

### Practical significance

Whether the difference is large enough to matter in a real ML security setting.

---

# Phase 16 — Model Diagnostics

Because this is primarily an anomaly-detection/classification experiment, diagnostics must match the model.

---

## Multicollinearity

Not a primary assumption of Isolation Forest or Random Forest.

Therefore:

> Do not make VIF a mandatory analysis.

However, for descriptive understanding of the feature space, correlation analysis can be reported.

If a linear/logistic regression is later introduced as an optional statistical model, then VIF becomes relevant.

---

# Heteroskedasticity

Not a primary assumption of:

- Isolation Forest;
- Random Forest;
- ROC-AUC;
- PR-AUC;
- F1.

Therefore:

> No Breusch-Pagan test is required for the primary analysis.

---

# Normality

Not required for:

- Isolation Forest;
- Random Forest;
- ROC-AUC;
- PR-AUC;
- F1.

Do not force Shapiro-Wilk tests onto thousands of observations.

---

# Autocorrelation

No time variable exists for the experimental observations.

Therefore:

> No serial-correlation diagnostic is required.

---

# Endogeneity

Endogeneity is a causal-regression concept.

This study is not estimating a causal treatment effect.

Therefore:

> Endogeneity is not a primary diagnostic.

---

# Influential observations

Classical regression leverage/Cook's distance is not appropriate for Isolation Forest/Random Forest.

Instead examine:

- extreme feature values;
- anomaly-score sensitivity;
- duplicated observations;
- attack-specific behavior;
- performance sensitivity to filtering.

---

# Model specification

For the primary models verify:

```text
predictor columns correct
metadata excluded
preprocessing fit correctly
test set untouched
random seed recorded
hyperparameters recorded
threshold selected on validation
```

These are more important than traditional regression diagnostics for this research.

---

# Phase 17 — Hypothesis Testing Plan

## H1

### Claim

PoisonShield detects more poisoning than individual layers.

### Test

Compare:

```text
PoisonShield
vs
Integrity Only
vs
Anomaly Only
```

### Metrics

Primary:

```text
F1
Recall
PR-AUC
```

Secondary:

```text
Precision
FPR
FNR
```

### Support criterion

H1 is supported if PoisonShield demonstrates consistently better detection performance across matched attack/rate/seed conditions, with the improvement being practically meaningful.

Do not define "supported" merely as:

```text
p < 0.05
```

---

# H2

### Claim

Adding anomaly detection to integrity verification improves recall while maintaining acceptable FPR.

### Compare

```text
Integrity Only
vs
PoisonShield
```

### Evidence

```text
Recall ↑
FPR remains acceptable
```

### Interpretation

If recall improves but FPR becomes unacceptably high:

> H2 is only partially supported.

---

# H3

### Claim

PoisonShield filtering improves downstream model performance.

### Compare

```text
No Defense
Anomaly Only
Integrity Only
PoisonShield
```

### Dependent variable

```text
clean-test Macro F1
```

and other task-specific metrics.

### Important

Use the same clean test set for every method.

---

# Phase 18 — Results Presentation

The final Results chapter should contain the following.

---

## Table 1 — Dataset Overview

Columns:

```text
Dataset
Observations
Features
Classes
Clean observations
Attack observations
Attack types
```

---

## Table 2 — Data Quality Audit

```text
Dataset
Missing cells
Duplicate rows
Constant features
Invalid values
```

---

## Table 3 — Attack Distribution

```text
Attack Type
Count
Percentage
```

Add a note:

> Percentages represent the controlled experimental dataset composition and should not be interpreted as real-world poisoning prevalence.

---

## Table 4 — Descriptive Statistics

For selected relevant variables:

```text
Mean
Median
SD
Min
Max
```

---

## Figure 1 — Attack Distribution

Bar chart.

---

## Figure 2 — Missingness

Feature-level missingness.

---

## Figure 3 — Feature Distribution

Selected representative variables.

---

## Table 5 — Anomaly Detection Performance

```text
Dataset
Threshold
Precision
Recall
F1
ROC-AUC
PR-AUC
FPR
FNR
```

---

## Table 6 — Integrity Performance

```text
Attack
Integrity Detection Rate
False Positive Rate
```

---

## Table 7 — Main Defense Comparison

```text
Defense
Precision
Recall
F1
PR-AUC
FPR
Retention
```

---

## Figure 4 — ROC Curves

Compare:

```text
Integrity
Anomaly
PoisonShield
```

---

## Figure 5 — Precision-Recall Curves

Especially important because attack prevalence can vary under controlled rates.

---

## Table 8 — Attack-Specific Results

```text
Attack
Integrity
Anomaly
PoisonShield
```

---

## Table 9 — Poisoning Rate Sensitivity

```text
1%
5%
10%
20%
```

---

## Table 10 — Downstream Model Performance

```text
Training Condition
Accuracy
Macro Precision
Macro Recall
Macro F1
```

---

## Table 11 — Ablation

```text
Configuration
F1
Recall
FPR
Retention
```

---

## Table 12 — Statistical Comparison

```text
Comparison
Mean Difference
CI
Effect Size
Adjusted p-value
```

---

## Table 13 — Computational Overhead

```text
Method
Training Time
Detection Time
Filtering Time
Memory/Resource Cost
```

---

# Essential vs Optional Results

### Essential

- dataset audit
- attack distribution
- anomaly detection
- integrity detection
- PoisonShield comparison
- attack-specific results
- poisoning-rate results
- downstream model results
- ablation
- false-positive/data-retention analysis

### Optional

- extensive correlation matrices
- alternative anomaly algorithms
- feature importance
- additional classifiers
- autoencoder
- One-Class SVM
- LOF

Do not add alternatives simply to make the thesis longer.

---

# Phase 19 — Interpretation and Discussion

Use the following structure.

---

## RQ1 Discussion

Ask:

> Did integrity verification correctly identify deviations from the trusted reference?

Then discuss:

- modified features;
- modified labels;
- additions;
- removals;
- duplication.

Explain that integrity evidence is fundamentally about **reference consistency/provenance**.

---

## RQ2 Discussion

Ask:

> Which poisoning mechanisms produce anomalous feature-space behavior?

Possible outcome:

```text
Outlier injection → high detection
Gaussian noise → high detection
Label flipping → potentially weaker detection
```

Do not assume this before seeing results.

The actual results determine the conclusion.

---

## RQ3 Discussion

Ask:

> Does the combination outperform individual evidence sources?

Discuss:

```text
PoisonShield
vs
Integrity
vs
Anomaly
```

If improvement occurs:

> Evidence supports complementarity.

If no improvement:

> The evidence suggests the components may be redundant under the tested conditions.

Both are legitimate findings.

---

## RQ4 Discussion

Discuss differences between attacks.

A useful table:

| Attack | Why it may be detected | Why it may evade |
|---|---|---|
| Feature poisoning | distribution changes | subtle modifications |
| Outlier injection | extreme values | — |
| Missing values | missingness pattern | imputation |
| Duplication | multiplicity change | exact trusted match |
| Label flipping | label inconsistency | feature space unchanged |
| Gaussian noise | feature perturbation | low noise |
| Backdoor | potentially subtle feature changes | attack may resemble clean data |

These are **interpretive mechanisms**, not predetermined findings.

---

# RQ5 Discussion

Explain:

```text
poisoning
→ degraded training
→ defense filtering
→ cleaner training set
→ downstream performance
```

But do not claim causal certainty beyond the controlled experiment.

Use wording such as:

> "Under the controlled experimental conditions, PoisonShield filtering was associated with improved downstream performance."

Avoid:

> "PoisonShield proves that it will improve real-world model performance."

---

# RQ6 Discussion

Discuss the trade-off:

```text
higher detection
       ↕
higher false positives
       ↕
lower data retention
```

A good defense is not necessarily the one with maximum recall.

---

# Phase 20 — Limitations and Validity

## Internal validity

Ask:

- Was the clean test set untouched?
- Were thresholds selected without test leakage?
- Was preprocessing fitted only on clean training data?
- Were the attacks generated consistently?
- Were seeds controlled?

---

# External validity

The datasets are controlled benchmarks.

Therefore results may not directly generalize to:

- real organizations;
- unknown attacks;
- different data modalities;
- adaptive attackers;
- continuously evolving datasets.

---

# Measurement validity

Integrity risk measures:

> reference consistency.

Anomaly score measures:

> statistical unusualness.

Neither directly measures:

> attacker intent.

Therefore do not equate anomaly with maliciousness.

---

# Sampling limitations

The attack proportions are experimentally controlled.

They are not estimates of real-world poisoning prevalence.

---

# Model limitations

Isolation Forest may fail when:

- poisoned samples closely resemble clean data;
- poisoning is distributed subtly;
- legitimate rare samples appear anomalous.

---

# Data limitations

The Android dataset identity must be transparently reported.

The original supplied filename says `cccs_andmal2020`, but its structure corresponds to CICMalDroid2020.

Do not hide this discrepancy.

---

# Provenance limitations

The current integrity mechanism assumes access to a trusted reference state.

Therefore it does not solve the problem:

> "What if the initial reference dataset is already compromised?"

This should be acknowledged.

---

# Adaptive attacker limitation

An attacker who knows the anomaly detector may deliberately construct low-anomaly poisoning.

This is outside the primary threat model unless later added as an explicit experiment.

---

# Phase 21 — Reproducibility

Every experiment should produce:

```text
experiment_id
dataset
attack
rate
seed
defense
parameters
threshold
alpha
beta
metrics
runtime
timestamp
```

Store in:

```text
results/logs/
```

---

# Configuration File

Create:

```yaml
random_seeds:
  - 42
  - 123
  - 2026

poisoning_rates:
  - 0.01
  - 0.05
  - 0.10
  - 0.20

threshold_percentiles:
  - 0.90
  - 0.95
  - 0.975
  - 0.99

isolation_forest:
  n_estimators: 300
  random_state: 42
  n_jobs: -1

downstream_model:
  type: random_forest
```

---

# Phase 22 — Final Research Paper

Once experiments are complete, write the paper in this order.

---

# Chapter 1 — Introduction

Include:

1. ML data security problem
2. training-data poisoning
3. motivation
4. research gap
5. PoisonShield concept
6. research aim
7. RQs
8. hypotheses
9. contributions
10. paper organization

Do not include results here.

---

# Chapter 2 — Literature Review

Cover:

### Training-data poisoning

Biggio et al.

### Poisoning surveys

Wang et al.

### Dataset security

Goldblum et al.

### Backdoor attacks

BadNets.

### Backdoor detection

Activation Clustering.

### Spectral methods

Spectral Signatures.

### Certified defenses

Steinhardt et al.

### Anomaly detection

Isolation Forest.

Then identify the specific gap:

> integration and empirical evaluation of complementary integrity/provenance and sample-level anomaly evidence under controlled heterogeneous poisoning conditions.

Keep the novelty claim conservative, as the proposal explicitly requires.

---

# Chapter 3 — Methodology

Include:

1. research design;
2. threat model;
3. datasets;
4. variables;
5. data preparation;
6. integrity layer;
7. anomaly layer;
8. fusion;
9. filtering;
10. downstream classifier;
11. attack mechanisms;
12. poisoning rates;
13. experimental conditions;
14. metrics;
15. statistical analysis;
16. robustness analysis.

The controlled experimental structure is already established in the proposal.

---

# Chapter 4 — Data Description

Present:

- dataset origins;
- dataset identity;
- observations;
- features;
- classes;
- attack types;
- missingness;
- duplicates;
- constants;
- preprocessing decisions.

This is where the Android dataset identity discrepancy should be transparently explained.

---

# Chapter 5 — Results

Follow this sequence:

```text
5.1 Dataset audit
5.2 EDA
5.3 Integrity results
5.4 Anomaly results
5.5 PoisonShield results
5.6 Attack-specific results
5.7 Poisoning-rate results
5.8 Downstream model results
5.9 Ablation
5.10 Robustness
5.11 Statistical comparisons
5.12 Computational overhead
```

Do not mix interpretation heavily into the Results chapter.

---

# Chapter 6 — Discussion

Organize by RQ:

```text
6.1 RQ1
6.2 RQ2
6.3 RQ3
6.4 RQ4
6.5 RQ5
6.6 RQ6
```

Then:

```text
theoretical implications
practical implications
comparison with literature
unexpected findings
```

---

# Chapter 7 — Limitations

Discuss:

- controlled attacks;
- dataset dependence;
- reference-state assumption;
- anomaly-detector limitations;
- attack adaptivity;
- generalizability;
- Android dataset identity issue;
- absence of real-world deployment validation.

---

# Chapter 8 — Conclusion

Answer:

1. What problem was studied?
2. What was proposed?
3. What was tested?
4. What did the evidence show?
5. Which hypotheses were supported?
6. Where did PoisonShield fail?
7. What should future work investigate?

Never write:

> "PoisonShield completely solves data poisoning."

Instead:

> "The results provide empirical evidence regarding the conditions under which the proposed layered defense improves poisoning detection and downstream robustness."

---

# Appendices

Include:

- complete feature lists;
- configuration;
- hyperparameters;
- pseudocode;
- additional confusion matrices;
- attack-generation procedures;
- sensitivity tables;
- statistical test details;
- reproducibility instructions.

---

# Final Research Decision Rules

At every stage use this rule:

### If output matches expectations

Proceed.

### If output differs moderately

Investigate and document.

### If output contradicts the known dataset structure

STOP.

### If a preprocessing step changes attack evidence

STOP and redesign.

### If a variable is derived from attack labels

Remove it from model predictors.

### If a threshold uses test data

STOP and redo.

### If a result looks "too good"

Investigate leakage before celebrating it.

### If PoisonShield performs worse

Do not manipulate the method.

Report the finding and analyze why.

---

# Master Execution Checklist

## Research Setup

☐ 1. Freeze research questions **[CRITICAL]**

☐ 2. Freeze hypotheses **[CRITICAL]**

☐ 3. Freeze variables **[CRITICAL]**

☐ 4. Freeze unit of analysis **[CRITICAL]**

☐ 5. Freeze experimental conditions **[CRITICAL]**

☐ 6. Create VS Code/Python environment **[CRITICAL]**

☐ 7. Create project structure **[CRITICAL]**

☐ 8. Create research protocol **[CRITICAL]**

---

## Dataset Audit

☐ 9. Copy raw datasets **[CRITICAL]**

☐ 10. Load datasets **[CRITICAL]**

☐ 11. Verify dimensions **[CRITICAL]**

☐ 12. Verify columns **[CRITICAL]**

☐ 13. Verify data types **[CRITICAL]**

☐ 14. Verify metadata variables **[CRITICAL]**

☐ 15. Create `poisoned` evaluation variable **[CRITICAL]**

☐ 16. Verify attack distributions **[CRITICAL]**

☐ 17. Verify label mismatches **[CRITICAL]**

☐ 18. Audit missing values **[CRITICAL]**

☐ 19. Audit duplicates **[CRITICAL]**

☐ 20. Audit constant features **[CRITICAL]**

☐ 21. Audit impossible values **[CRITICAL]**

☐ 22. Audit outliers **[CRITICAL]**

☐ 23. Verify Android dataset identity **[CRITICAL]**

☐ 24. Create data dictionary **[CRITICAL]**

---

## EDA

☐ 25. Descriptive statistics **[CRITICAL]**

☐ 26. Attack distribution **[CRITICAL]**

☐ 27. Class distribution **[CRITICAL]**

☐ 28. Missingness analysis **[CRITICAL]**

☐ 29. Distribution analysis **[CRITICAL]**

☐ 30. Group comparisons **[CRITICAL]**

☐ 31. Correlation analysis **[IMPORTANT]**

☐ 32. Generate EDA figures **[CRITICAL]**

---

## Preprocessing

☐ 33. Define predictor columns **[CRITICAL]**

☐ 34. Remove constant predictors from modeling **[CRITICAL]**

☐ 35. Preserve attack evidence **[CRITICAL]**

☐ 36. Create clean train/validation/test **[CRITICAL]**

☐ 37. Fit preprocessing only on clean training **[CRITICAL]**

☐ 38. Verify no leakage **[CRITICAL]**

---

## Integrity

☐ 39. Build canonical row representation **[CRITICAL]**

☐ 40. Build trusted manifest **[CRITICAL]**

☐ 41. Implement multiplicity-aware comparison **[CRITICAL]**

☐ 42. Test feature modification **[CRITICAL]**

☐ 43. Test label modification **[CRITICAL]**

☐ 44. Test addition/removal **[CRITICAL]**

☐ 45. Test duplication **[CRITICAL]**

☐ 46. Calculate integrity metrics **[CRITICAL]**

---

## Anomaly Detection

☐ 47. Train Isolation Forest on clean data **[CRITICAL]**

☐ 48. Generate anomaly scores **[CRITICAL]**

☐ 49. Tune threshold on validation **[CRITICAL]**

☐ 50. Evaluate anomaly detection **[CRITICAL]**

☐ 51. Calculate ROC-AUC/PR-AUC **[CRITICAL]**

☐ 52. Calculate FPR/FNR **[CRITICAL]**

---

## PoisonShield

☐ 53. Normalize anomaly evidence **[CRITICAL]**

☐ 54. Combine integrity + anomaly evidence **[CRITICAL]**

☐ 55. Tune α/β on validation **[CRITICAL]**

☐ 56. Tune risk threshold **[CRITICAL]**

☐ 57. Freeze final parameters **[CRITICAL]**

☐ 58. Evaluate PoisonShield on test data **[CRITICAL]**

---

## Main Experiments

☐ 59. E1 No Defense **[CRITICAL]**

☐ 60. E2 Integrity Only **[CRITICAL]**

☐ 61. E3 Anomaly Only **[CRITICAL]**

☐ 62. E4 PoisonShield **[CRITICAL]**

☐ 63. Attack-specific analysis **[CRITICAL]**

☐ 64. Poisoning-rate analysis **[CRITICAL]**

☐ 65. Downstream Random Forest evaluation **[CRITICAL]**

---

## Robustness

☐ 66. Seed 42 **[CRITICAL]**

☐ 67. Seed 123 **[CRITICAL]**

☐ 68. Seed 2026 **[CRITICAL]**

☐ 69. Threshold sensitivity **[CRITICAL]**

☐ 70. α/β sensitivity **[CRITICAL]**

☐ 71. Ablation study **[CRITICAL]**

☐ 72. Computational overhead **[IMPORTANT]**

---

## Statistical Analysis

☐ 73. Aggregate repeated results **[CRITICAL]**

☐ 74. Confidence intervals **[CRITICAL]**

☐ 75. Effect sizes **[CRITICAL]**

☐ 76. Paired comparisons **[CRITICAL]**

☐ 77. Friedman test if applicable **[IMPORTANT]**

☐ 78. Corrected pairwise comparisons if applicable **[IMPORTANT]**

☐ 79. Distinguish statistical/practical significance **[CRITICAL]**

---

## Paper

☐ 80. Final literature review update **[CRITICAL]**

☐ 81. Methodology chapter **[CRITICAL]**

☐ 82. Dataset chapter/section **[CRITICAL]**

☐ 83. Results tables **[CRITICAL]**

☐ 84. Results figures **[CRITICAL]**

☐ 85. Discussion by RQ **[CRITICAL]**

☐ 86. Hypothesis evaluation **[CRITICAL]**

☐ 87. Limitations **[CRITICAL]**

☐ 88. Reproducibility appendix **[IMPORTANT]**

☐ 89. Final references **[CRITICAL]**

☐ 90. Final proofreading **[CRITICAL]**

☐ 91. Code/repository cleanup **[CRITICAL]**

☐ 92. Final submission **[CRITICAL]**

---

# Definition of Done

The research is considered complete only when:

```text
Raw data preserved
        ↓
Dataset audited
        ↓
EDA completed
        ↓
Variables validated
        ↓
Leakage ruled out
        ↓
Integrity implemented
        ↓
Anomaly detector implemented
        ↓
PoisonShield implemented
        ↓
Baselines evaluated
        ↓
Attack-specific experiments
        ↓
Poisoning-rate experiments
        ↓
Downstream evaluation
        ↓
Ablation
        ↓
Robustness
        ↓
Statistical analysis
        ↓
Results tables/figures
        ↓
Discussion
        ↓
Limitations
        ↓
Final manuscript
        ↓
Reproducible repository
        ↓
Submission
```

# The Most Important Rule

**Do not jump directly to PoisonShield.**

The scientifically correct order is:

```text
Dataset
  ↓
Audit
  ↓
EDA
  ↓
Clean reference
  ↓
Integrity baseline
  ↓
Anomaly baseline
  ↓
PoisonShield
  ↓
Comparison
  ↓
Robustness
  ↓
Statistics
  ↓
Paper
```

That sequence allows us to determine whether PoisonShield actually adds value rather than building the combined system first and then searching for evidence that it works.