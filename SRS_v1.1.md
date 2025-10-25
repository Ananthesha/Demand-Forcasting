# SRS v1.1 — Demand Forecasting System (DFS) (trimmed MVP)

**Project:** ForeSight — Demand Forecasting System  
**Version:** 1.1  
**Authors:** Team (MS Ananthesha, Kolluri Nithin, Kanniyappa Ranjith, Karthik)  
**Date:** 2025-09-30  
**Status:** Draft (updates: INVEST user stories)

---

## Summary
This file contains the updated SRS user-stories following the INVEST principle, plus brief context for the trimmed MVP (CSV-only ingestion, local SQLite, two model types, dashboard, export).

---

## MVP Context (short)
- CSV upload + preview (required columns: date, value)  
- Preprocessing: missing-value handling, simple outlier flagging  
- Feature engineering: date features + exogenous columns in CSV  
- Modeling pipeline: at least one classical and one ML regressor model, CV and metrics (RMSE, MAPE)  
- Forecast generation + CSV/XLSX export  
- Model registry (local files / SQLite) and basic audit logs  
- Security: CSV sanitization; secrets via env vars

---

## INVEST-compliant User Stories (for JIRA import)

**US-01 — Upload CSV**  
- **As a** Planner  
- **I want to** upload a CSV file containing historical sales data  
- **So that** I can provide input data for modeling  
- **Acceptance Criteria (AC):**  
  1. UI accepts `.csv` files up to 10 MB.  
  2. On upload, system shows a preview of first 100 rows and detected columns in a table.  
  3. If required columns (`date`, `value`) are missing, show a clear error message.  
- **Story points:** 3

**US-02 — Preview & Validate Data**  
- **As a** Planner  
- **I want to** preview and validate uploaded data schema before ingest  
- **So that** I can confirm data correctness  
- **AC:**  
  1. Preview shows detected date column, value column, and number of rows.  
  2. Validator flags invalid date formats and missing required columns.  
  3. Validator rejects or sanitizes suspicious CSV cells that start with `=`, `+`, `-`, `@`.  
- **Story points:** 2

**US-03 — Missing-value handling**  
- **As a** Data Scientist  
- **I want to** apply a missing-value strategy (interpolate / forward-fill / drop)  
- **So that** the modeling pipeline receives consistent series  
- **AC:**  
  1. UI allows selection of strategy and preview of result.  
  2. After applying, preview updates and no nulls remain where strategy applied.  
  3. Operation is reversible for the current session (undo).  
- **Story points:** 3

**US-04 — Train baseline models**  
- **As a** Data Scientist  
- **I want to** train at least two model types (classical + ML regressor) on the dataset  
- **So that** I can compare performance  
- **AC:**  
  1. User picks model type and basic hyperparameters in UI.  
  2. Training job completes and returns metrics (RMSE, MAPE).  
  3. Trained model saved to local registry with `modelId`.  
- **Story points:** 5

**US-05 — Cross-validation & metrics**  
- **As a** Data Scientist  
- **I want to** run rolling-window cross-validation and view fold metrics  
- **So that** I can evaluate model stability  
- **AC:**  
  1. CV runs with user-defined parameters (folds/window).  
  2. UI displays per-fold RMSE/MAPE and aggregate metrics.  
  3. CV report exportable as CSV.  
- **Story points:** 3

**US-06 — Generate forecast & export**  
- **As a** Planner  
- **I want to** generate a forecast for a horizon and export it as CSV/XLSX  
- **So that** I can use forecasts in reports  
- **AC:**  
  1. Forecast endpoint returns predictions for requested horizon.  
  2. UI displays forecast plot with history and prediction intervals (if supported).  
  3. Export button downloads CSV/XLSX with timestamp and `modelId`.  
- **Story points:** 3

**US-07 — Model save & reload**  
- **As a** Data Scientist  
- **I want to** save trained models and reload them later for forecasting  
- **So that** I don't need to retrain every time  
- **AC:**  
  1. Saved models appear in Model Registry with metadata (type, date, datasetId, metrics).  
  2. Selecting a `modelId` and forecasting uses the saved model.  
- **Story points:** 2

**US-08 — Dashboard visualization**  
- **As a** Planner  
- **I want to** view an interactive dashboard with historicals, forecast, and metrics  
- **So that** I can inspect results quickly  
- **AC:**  
  1. Dashboard shows historical series, forecast line, toggles for CI and overlays.  
  2. Dashboard supports panning/zoom and exporting chart as PNG.  
- **Story points:** 3

**US-09 — Basic audit logs**  
- **As an** Admin  
- **I want to** view recent audit events (upload/train/export)  
- **So that** I can track system usage  
- **AC:**  
  1. Each upload/train/export creates a log entry with timestamp, user, action, and resource id.  
  2. Audit view lists entries (searchable).  
- **Story points:** 2

**TS-10 — CSV sanitization (Tech Spike)**  
- **Type:** Technical task (spans sprints)  
- **Goal:** Escape leading `=,+,-,@` in CSV cells on export and sanitize previews to prevent formula injection.  
- **Story points:** 2

---

## Notes
- These stories are INVEST-compliant: independent, negotiable, valuable, estimable, small enough, and testable.  
- After creating this file, create a branch `docs/update-srs` and open a PR titled `docs: SRS v1.1 — update user stories (INVEST)`.

