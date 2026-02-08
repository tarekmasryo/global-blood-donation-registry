# Case Study — Global Blood Donation Registry (Decision-Grade Outreach)

## Overview
This repository provides a decision-ready workflow for donor outreach:
a calibrated score for eligible donors, plus policies that convert scores into actionable outreach under real budgets.

It is designed for **operators**: the outcome is not a model metric, but a clear list of who to contact and why.

## The real problem
Outreach is a resource allocation problem:
- outreach capacity is limited (call center hours, SMS budget, staff time)
- false positives waste budget and create donor fatigue
- false negatives miss potential donations, especially during shortages

A model score is not enough. You need a policy that respects constraints and makes trade-offs explicit.

## Goals (definition of done)
**Functional goals**
- score eligible donors with a calibrated probability: `P(donated_next_6m)`
- produce outreach actions:
  - **Top‑K** outreach for a given budget
  - a **net‑benefit threshold** that maximizes expected value

**Engineering goals**
- leak‑safe evaluation (train/validation/test with time awareness if available)
- export a reusable artifact bundle (model + threshold policy + metadata)

## Approach
### 1) Eligibility gating
Scores are computed only for donors who are eligible to donate (based on your business rules).
This avoids misleading actions on ineligible donors.

### 2) Calibration
Probabilities are calibrated so they can be treated as probabilities.
This makes threshold decisions meaningful and stable.

### 3) Policies, not just scores
Two production-style operating policies are produced:

- **Top‑K outreach:** contact the highest‑scoring K donors, where K is derived from outreach capacity.
- **Net‑benefit threshold:** choose a threshold that maximizes:
  `expected_net = benefit_per_donation * TP − cost_per_contact * contacted`

Both policies are exported with the model so inference stays consistent.

## Outputs
The notebook exports artifacts under `./artifacts/`:
- `donor_outreach_scored.csv` (scored donors + policy labels)
- `model_bundle.joblib` (pipeline + calibration + policy + metadata)
- `metrics_holdout.json` (evaluation metrics on the holdout window)

## Limitations
- Policies are only as good as eligibility rules and target definition.
- Calibration can drift with geography, seasonality, and operational changes.
- Costs/benefits must be set realistically; thresholds change when economics change.

## Next steps
- Add time-window monitoring (weekly baselines, calibration drift).
- Add cost sensitivity analysis (how actions change as budgets change).
- Integrate a lightweight “release gate” (minimum slice performance + max outreach budget).
