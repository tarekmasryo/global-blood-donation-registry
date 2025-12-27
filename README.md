# 🩸 Global Blood Donation Registry — Decision-Grade Outreach Policy

A production-style notebook that transforms the provided data into **calibrated donor scores** and **budgeted outreach decisions**.

**Notebook:** `global-blood-donation-registry.ipynb`

**Core output:** a calibrated **Outreach Score** for **eligible** donors  
`Outreach Score = P(donated_next_6m)`  
Then converts scores into **Top-K outreach actions** and a **net-benefit optimal threshold**.

---

## What you get (end-to-end)

### 1) Calibrated scoring (eligible donors only)
- Calibrated probability: `P(donated_next_6m)`
- Reliability check: calibration curve (holdout)

### 2) Budget-to-action policy
- Top **5% / 10% / 20%** outreach policies
- Best **threshold** chosen by **net benefit** (value vs cost)

### 3) Ops layer (optional)
If `blood_compatibility_lookup.csv` exists:
- Compatibility-based **demand vs supply**
- **Stress index** per recipient blood type
- Compatibility matrix (donor → recipient)

### 4) Exported artifacts (`artifacts/`)
- `donor_outreach_scored.csv`
- `model_bundle.joblib`
- `metrics_holdout.json`

---


## Inputs (expected filenames)

**Required**
- `blood_donation_registry_ml_ready.csv`
- `blood_population_distribution.csv`

**Optional**
- `blood_compatibility_lookup.csv`

> Keep filenames identical for plug-and-run.

---

## Quickstart

### Local (recommended)

```bash
python -m venv .venv
# Windows:
# .venv\Scripts\activate
# Linux/Mac:
# source .venv/bin/activate

pip install -U pip
pip install -r requirements.txt
jupyter notebook
```

Place inputs in either:
- `./data/` (recommended), or
- next to the notebook

Optional: point the notebook to a custom folder via env var:

```bash
# Windows PowerShell
$env:BLOODS_DATA_DIR="D:\path\to\data"

# Linux/Mac
export BLOODS_DATA_DIR="/path/to/data"
```

### Kaggle
- Attach the dataset with the same filenames.
- The notebook can load from `/kaggle/input` automatically.

---

## What the notebook does (minimal map)

1. **Load + validate** (data contract checks)
   - required columns
   - `donor_id` uniqueness
   - basic range checks (e.g., recency)
2. **Decision visuals**
   - Recency × Frequency response surface (eligible only)
   - Uplift vs eligible baseline
   - Donors vs population-weighted blood type distribution (+ gap)
3. **Optional ops stress** (compatibility file)
4. **Model**
   - train on **eligible donors only**
   - `sklearn` pipeline (impute → encode/scale → LogisticRegression)
   - isotonic calibration (train → calibrate → test)
5. **Policy conversion**
   - deciles + gains/lift + frontier
   - net-benefit threshold selection
6. **Export artifacts** to `artifacts/`

---

## Artifacts

### `artifacts/donor_outreach_scored.csv`
Includes:
- `outreach_score` (eligible donors only; `NaN` for ineligible donors)
- Policy flags:
  - `policy_top5pct_eligible`
  - `policy_top10pct_eligible`
  - `policy_top20pct_eligible`
  - `policy_threshold_best`

### `artifacts/model_bundle.joblib`
Reusable bundle:
- trained `sklearn` pipeline
- isotonic calibrator
- seed + metadata

**Example usage:**

```python
import joblib
import numpy as np
import pandas as pd

bundle = joblib.load("artifacts/model_bundle.joblib")
pipe = bundle["pipeline"]
cal = bundle["calibrator"]

df = pd.read_csv("data/blood_donation_registry_ml_ready.csv")

elig_mask = df["eligible_to_donate"].astype(int).to_numpy() == 1
X = df.drop(columns=["donor_id", "eligible_to_donate", "donated_next_6m"], errors="ignore")

scores = np.full(len(df), np.nan, dtype=float)
p_raw = pipe.predict_proba(X.loc[elig_mask])[:, 1]
scores[elig_mask] = cal.transform(p_raw)

df["outreach_score"] = scores
```

### `artifacts/metrics_holdout.json`
- ROC-AUC / PR-AUC / Brier
- best threshold details (TP/FP/FN/TN, contacted, net)
- decision parameters (`benefit_tp`, `cost_contact`)

---

## Decision parameters (net benefit)
The notebook selects a threshold that maximizes:

`net = benefit_per_success * TP - cost_per_contact * (TP + FP)`

You can tune:
- `BENEFIT_TP` (value of a successful donation)
- `COST_CONTACT` (outreach cost per contacted donor)

---

## Assumptions & limitations
- Scores are only meaningful for **eligible donors**.
- The policy depends on stable data definitions (target window, eligibility logic).
- Calibration quality can shift with time, geography, or operational changes.
- This is a decision-support tool; real deployment should add monitoring (drift, calibration, outcomes).

---

## License
- Code: MIT (recommended for this repo)
- Data: CC BY 4.0 (dataset package)

**Author:** Tarek Masryo
