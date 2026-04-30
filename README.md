# 🩸 Global Blood Donation Registry — Donor Outreach Scoring and Supply Stress

A practical, reproducible notebook for validating a synthetic blood-donor registry, analyzing donor behavior, and building a leak-safe baseline outreach score.

> **Important scope note:** This synthetic dataset is intended for exploratory analysis, ML baseline development, and portfolio demonstrations. It is not an official blood-bank registry and is not intended for medical, clinical, or donor-eligibility decisions.

Notebook: [`global-blood-donation-registry.ipynb`](global-blood-donation-registry.ipynb)  
Case study: [`CASE_STUDY.md`](CASE_STUDY.md)

---

## ✨ What this repo provides

- ✅ **Data validation checks** for schema, ranges, duplicate IDs, target consistency, recency logic, blood-type labels, and compatibility coverage
- 📊 **Focused EDA** for eligibility, donor behavior, donation history, recency, regions, and blood types
- 🧬 **Blood-type prevalence comparison** against population-weighted country distributions
- 🚦 **Compatibility stress analysis** using donor supply share versus recipient demand proxy
- 🤖 **Leak-safe baseline outreach model** trained only on eligible donors
- 🎯 **Budget-aware outreach policies** using Top-K selection and validation-based thresholds
- 📦 **Reusable artifacts** exported for dashboards, reports, or batch scoring workflows

---

## 🧠 Core idea

The notebook treats donor outreach as a ranking and resource-allocation problem:

```text
Outreach Score = P(donated_next_6m)
```

The score is then converted into operational lists:

- **Top-K outreach policies** for fixed outreach capacity
- **Threshold policy** selected on a policy-validation split, not tuned on the holdout set
- **Scored donor file** with reusable policy flags

---

## 📁 Repository structure

```text
global-blood-donation-registry/
├── global-blood-donation-registry.ipynb
├── README.md
├── CASE_STUDY.md
├── requirements.txt
├── LICENSE
├── data/
│   └── README.md
├── docs/
│   └── README.md
└── artifacts/
    └── README.md
```

---

## 📥 Expected dataset files

Place the source files in the local repository as follows:

```text
data/
├── blood_donation_registry_ml_ready.csv
├── blood_population_distribution.csv
└── blood_compatibility_lookup.csv

docs/
└── data_dictionary.csv
```

The notebook also supports Kaggle input layouts and a legacy `data/raw/` fallback.

---

## ⚙️ Quick start

```bash
python -m venv .venv
# Windows: .\.venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate

pip install -r requirements.txt
```

Then open and run:

```text
global-blood-donation-registry.ipynb
```

On Kaggle, attach the dataset and run:

```text
Run All → Save Version
```

---

## 📦 Exported artifacts

The notebook writes generated outputs to `./artifacts/`:

- `data_quality_report.csv` — validation checks and pass/fail details
- `donor_outreach_scored.csv` — donor-level outreach scores and policy flags
- `policy_summary.csv` — selected donor counts per outreach policy
- `holdout_decile_table.csv` — holdout ranking/decile performance
- `holdout_topk_policies.csv` — Top-K policy evaluation on the holdout split
- `blood_type_stress_index.csv` — synthetic compatibility stress analysis
- `model_bundle.joblib` — trained model, feature list, exclusions, and selected threshold metadata
- `metrics_holdout.json` — final metrics and threshold-policy details

---

## 🔒 Leakage controls

The workflow avoids common evaluation mistakes:

- trains only on currently eligible donors
- excludes post-period labels and direct target-derived fields
- excludes eligibility labels, deferral reason, and synthetic propensity score
- separates training, policy-validation, and holdout-test splits
- selects threshold policy on validation, then reports final behavior on holdout

---

## 📌 Suggested use cases

- Kaggle notebook publication
- Portfolio case study for decision-ready ML workflows
- Streamlit or BI dashboard prototype
- Batch scoring demo for donor outreach planning
- Data-quality and model-policy walkthrough

---

## ⚠️ Limitations

- The dataset is synthetic and should not be used for real donor eligibility or healthcare decisions.
- Cost/benefit values in the threshold section are illustrative and should be replaced for any real planning scenario.
- The baseline model is intentionally simple and reproducible; it is not presented as a final production model.

---

## 📄 License

MIT License for code and repository materials. Dataset licensing depends on the dataset source.
