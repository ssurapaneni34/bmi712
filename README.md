# bmi712

# Foundation Model Representation Quality on HEST-1k

**Status: work-in-progress / exploratory.** Currently evaluates UNI (v1) patch embeddings on breast and lung cancer samples from [HEST-1k](https://github.com/mahmoodlab/HEST). UNI-2 comparison and morphology-signal metrics are planned.

## Research question

Do pathology foundation model embeddings capture generalizable tissue morphology, or are they dominated by sample-level artifacts (staining, scanner, tissue preparation)?

We probe this by extracting UNI embeddings for all patches from a chosen cancer cohort, clustering the pooled embedding space with Leiden, and examining whether resulting clusters mix patches from many samples (good: morphology-driven) or are dominated by individual samples (bad: batch-driven).

## What's done

- Extract UNI v1 embeddings for all breast cancer samples in HEST-1k (n=140)
- Extract UNI v1 embeddings for all lung cancer samples in HEST-1k (n=11)
- Leiden clustering on pooled embeddings for each organ
- UMAP visualization colored by sample ID and Leiden cluster
- Per-cluster sample-mixing entropy table

## Repo structure

```
.
├── notebooks/
│   ├── LUNG_cancer_UNI1.ipynb  
│   └── BREAST_cancer_UNI1.ipynb       
├── tables/
│   ├── BREAST_table1_leiden_patient_mixing.csv
│   └── LUNG_table2_leiden_patient_mixing.csv
└── README.md
```

## Setup

### Requirements

- Colab with GPU
- HuggingFace account with approved access to:
  - `MahmoodLab/hest` (gated dataset)
  - `MahmoodLab/UNI` (gated model)
- Google Drive space for cached embeddings (tens of GB for breast, much less for lung)

### Dependencies

```bash
pip install timm h5py huggingface_hub scanpy leidenalg igraph umap-learn
```

## Usage

### 1. Extract embeddings

Open notebooks. Set your HF token (recommended via Colab `userdata`), then choose the organ filter:

```python
subset = meta_df[(meta_df['organ'] == 'Breast') & (meta_df['disease_state'] == 'Cancer')]
# or
subset = meta_df[(meta_df['organ'] == 'Lung')   & (meta_df['disease_state'] == 'Cancer')]
```

The notebook loops per-sample: download patches, run UNI, save `{sample_id}_Patches_UNI.h5` to Drive, delete the raw patches to stay within Colab's ephemeral disk budget. Safe to resume — samples already cached on Drive are skipped.

## Data

HEST-1k (v1.3.0) subsets used:

| Organ   | Samples | disease_state | Species      |
|---------|---------|---------------|--------------|
| Breast  | 139     | Cancer        | Homo sapiens |
| Lung    | 11      | Cancer        | Homo sapiens |
