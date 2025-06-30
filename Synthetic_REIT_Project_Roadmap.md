
# Synthetic REIT Data Project — Detailed Checklist & Execution Roadmap

This document defines every step required to generate, validate, load, and use synthetic REIT data inside Snowflake, culminating in an automated fact‑sheet workflow.

---

## Step 1 Synthetic‑Data Generation & Validation (Python)

- [ ] **Seed definition**
  - Configure 5 initial synthetic REIT portfolios (account code, name, inception date, benchmark mapping).
  - Intake **new‑account seed files** (CSV or JSON) dropped into a watched `/seeds` folder; parse and append to the master account list.

- [ ] **Per‑holding simulation**
  - Generate 10 holdings per portfolio:
    - Price, shares, EPS (current & 5 y‑ago), BV/Share, sector.
  - Compute derived fields (market value, P/E, P/B, ROE, 5‑year earnings‑growth).

- [ ] **Time‑series simulation**
  - Create 30 years of **daily** NAV and benchmark series per portfolio.
  - Create 30 years of **annual** fund‑level metrics (weighted P/E, P/B, ROE, AUM, return).

- [ ] **Validation layer**
  - Schema checks: verify column names, dtypes, and null policies against a `schema.yaml`.
  - Business‑rule checks:
    - Weights per portfolio sum to 1.00 (±0.001 tolerance).
    - No negative prices, NAVs, or book values.
  - Log validation results to a `generation_audit` table (Snowflake) with status, row counts, min/max, and checksum.

- [ ] **Performance logging**
  - Record execution time, row counts written, and memory usage in a `performance_log` table.
  - Raise alerts (stdout + log) if generation deviates >20 % from expected size.

- [ ] **Error handling**
  - Write Python exceptions to an `error_log` table; include stack trace, timestamp, account code.

- [ ] **Output targets**
  - Write cleansed DataFrames directly to Snowflake staging tables using `snowflake.connector` or `write_pandas` (no intermediate Excel).

---

## Step 2 Snowflake Load & Table Population

- [ ] **Staging tables**
  - `stg_holdings`
  - `stg_portfolio_metrics_annual`
  - `stg_nav_daily`
  - `stg_benchmark_daily`
  - `stg_sector_breakdown`
  - `stg_qualitative_content`

- [ ] **Transform / merge**
  - Use Snowflake SQL (or Snowpark) to upsert from staging into final analytic tables:

| Final Table | Populated From |
|-------------|---------------|
| `PortfolioGeneralInformation` | `stg_portfolio_metrics_annual` (first load) | 
| `PortfolioAttributes` | `stg_portfolio_metrics_annual` |
| `ProductMaster` | distinct account and benchmark codes | 
| `HoldingsDetails` | `stg_holdings` |
| `BenchmarkCharacteristics` | static benchmark metadata file |
| `BenchmarkGeneralInformation` | static |
| `BenchmarkPerformance` | `stg_benchmark_daily` |

- [ ] **Integrity checks**
  - After each INSERT/MERGE, compare row counts with staging and log to `load_audit`.
- See Excel file for example of how we will populate the necessary tables

---

## Step 3 Bring into Assette Environment

- [ ] ?

---

## Step 4 Fact‑Sheet Assembly (Python‑Snowflake)

- [ ] Connect to Snowflake from Python using service account.
- [ ] Pull required data via SQL views or Snowpark:
  - Daily NAV & benchmark series.
  - Latest annual metrics.
  - Top‑10 holdings snapshot.
  - Sector weights.
  - Qualitative text & disclosures (from `stg_qualitative_content`).
- [ ] Compute presentation‑level tables and charts in memory (Matplotlib/Plotly, no Excel output).
- [ ] Render the fact sheet (HTML or PDF) using a template engine.
- [ ] Store rendered output in Snowflake external stage for downstream access.
