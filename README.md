# 🩸 Global Blood Donation Registry — Decision-Grade Outreach Policy

A production-style notebook that converts a donor registry into **calibrated outreach scores** and **budgeted outreach decisions**.

Notebook: `global-blood-donation-registry.ipynb`  
Case study: `CASE_STUDY.md`

---

## What this repository includes
- A single notebook: `global-blood-donation-registry.ipynb`
- Exported artifacts under `./artifacts/` (scored donors + model bundle + metrics)

---

## Core output
A calibrated probability for eligible donors:

`Outreach Score = P(donated_next_6m)`

Then the notebook turns scores into actions:
- **Top‑K outreach** for a a set outreach capacity
- **Net‑benefit threshold** that maximizes expected value under simple costs/benefits

---

## Dataset
The notebook expects these CSV files:

Required
- `blood_donation_registry_ml_ready.csv`
- `blood_population_distribution.csv`
- `blood_compatibility_lookup.csv`
- `data_dictionary.csv`

### Local (recommended)
1) Download the dataset files.
2) Place them under:
`data/raw/`

See `data/raw/README.md`.

### Kaggle
If local files are not present, the notebook falls back to the Kaggle input paths.

---

## Getting started

### 1) Install
```bash
python -m venv .venv
# Windows: .\.venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate

pip install -r requirements.txt
```

### 2) Run the notebook
Open and run:
- `global-blood-donation-registry.ipynb`

The notebook will export:
- `artifacts/donor_outreach_scored.csv`
- `artifacts/model_bundle.joblib`
- `artifacts/metrics_holdout.json`

See `artifacts/README.md`.

---

## Methodology notes
- Scores are computed only for **eligible** donors.
- Calibration makes thresholds usable for decision policies.
- Threshold policies make trade-offs explicit (contact cost vs expected donation benefit).

---

## License
MIT (code). Dataset licensing depends on the dataset source you download.
