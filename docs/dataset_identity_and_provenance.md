# Dataset Identity and Provenance

## CIC-MalMem-2022

The primary memory-malware dataset is retained under the working
identity `CIC-MalMem-2022`.

Local audited structure:

- Rows: 58,596
- Columns: 59
- Substantive numeric features: 55
- Seven poisoning mechanisms plus clean observations are represented
  in the primary experimental file.

## Android dataset

The local Android poisoning dataset is stored as:

`cccs_andmal2020_poisoned.csv`

The audited local structure is:

- Rows: 11,598
- Columns: 474
- Substantive numeric features: 470
- Five observed malware/benign classes.

The local structure matches the documented CICMalDroid2020
configuration of 11,598 final samples and 470 extracted features.

Therefore, for thesis reporting, this file should NOT be described
as the official 400,000-sample CCCS-CIC-AndMal-2020 dataset.

The thesis should instead explicitly document the local Android
dataset as a CICMalDroid2020-structured 11,598-sample, 470-feature
Android dataset, while preserving the original local filename as
part of the provenance record.

## Separate class-imbalance artifacts

The two `removed_audit` files are retained separately and are not
merged into the primary seven poisoning mechanisms.

They are used only for the separate class-imbalance audit.

## Methodological decision

Raw dataset files are not renamed, modified, or overwritten during
this provenance audit. Dataset identity is documented separately
from the local filenames.
