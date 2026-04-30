# Case Study — Global Blood Donation Registry

## Problem

Blood-donor outreach is a constrained prioritization problem. A registry can contain many potential donors, but outreach capacity is limited by staff time, messaging budget, donor fatigue, and operational timing.

This project turns a synthetic donor registry into a practical workflow for validating data, understanding donor behavior, and creating reusable outreach-scoring artifacts.

## Goals

The notebook focuses on five concrete tasks:

- Validate the dataset structure, expected ranges, and internal consistency.
- Analyze donor behavior, return likelihood, blood-type distribution, and outreach-relevant segments.
- Compare the donor pool with population-weighted blood-type prevalence.
- Build a leak-safe baseline model for next-six-month donation likelihood.
- Convert model scores into budget-aware outreach lists using validation-based thresholds.

## Approach

### 1. Data validation

The workflow checks schema, duplicate donor IDs, target consistency, binary flags, eligibility/deferral alignment, recency-date logic, broad numeric ranges, population prevalence sums, and RBC compatibility coverage.

### 2. Focused exploratory analysis

The EDA is designed around operational questions: eligibility rates, next-six-month donation rate, regional differences, blood-type segments, donation history, and recency/frequency behavior.

### 3. Synthetic supply-stress view

The notebook compares donor supply share with a population-weighted recipient demand proxy using the compatibility lookup. This produces a synthetic stress index for scenario analysis, not medical allocation decisions.

### 4. Leak-safe outreach model

The baseline model is trained only on currently eligible donors. It excludes fields that would leak target information or shortcut the task, including future donation counts, eligibility status, deferral reason, and the synthetic propensity score.

The split design separates:

- training data for fitting the model
- policy-validation data for selecting illustrative thresholds
- holdout-test data for final reporting

### 5. Policy conversion

The score is converted into outreach decisions through:

- Top-K policies for fixed outreach capacity
- validation-selected threshold policy for expected-value tradeoffs
- donor-level policy flags exported for downstream reuse

## Outputs

The notebook exports reusable files under `./artifacts/`, including a data-quality report, scored donor file, policy summaries, holdout evaluation tables, a model bundle, and metrics JSON.

## Limitations

This is a synthetic dataset case study. It is suitable for exploratory analysis, ML baseline development, dashboard prototypes, and portfolio demonstration. It is not an official blood-bank registry and is not intended for medical, clinical, or donor-eligibility decisions.

The model is deliberately simple and reproducible. The value of the project is the end-to-end workflow: validation, analysis, leakage controls, policy framing, and reusable artifacts.

## Next steps

- Connect `donor_outreach_scored.csv` to a Streamlit or BI dashboard.
- Replace illustrative policy economics with realistic outreach assumptions.
- Add monitoring for score drift and policy precision across future synthetic snapshots.
- Add a batch scoring wrapper if the workflow is reused outside Kaggle.
