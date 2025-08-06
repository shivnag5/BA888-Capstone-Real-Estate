# Real-Estate – Project Overview

## Comprehensive Project Overview

This project automates the ingestion, enrichment, and storage of Real Estate Investment Trust (REIT) data using Python, Snowflake, and Yahoo Finance. It consists of modular scripts that handle securities, holdings, product classification, benchmark performance, and portfolio analytics.

The pipeline supports:
- Real-time price and dividend enrichment using Yahoo Finance
- Classification of REITs and their associated portfolios/products
- Automated benchmark matching and performance analysis
- Storage in Snowflake for enterprise-grade accessibility and reporting

---

## Scripts and Corresponding Snowflake Tables

| Script Name                     | Snowflake Table                   | Description                                         |
|--------------------------------|-----------------------------------|-----------------------------------------------------|
| `security_master.py`           | `SECURITY_MASTER`                 | Loads validated security metadata                   |
| `Holdings Details.py`          | `HOLDINGSDETAILS`                 | Generates enriched REIT holdings                    |
| `pm.py`                        | `ProductMaster`                   | Classifies and loads product metadata               |
| `pgi.py`                       | `PortfolioGeneralInformation`     | Creates structured portfolio metadata               |
| `bgi.py`                       | `BenchmarkGeneralInformation`     | Loads benchmark definitions (e.g., VNQ, IYR)        |
| `bp.py`                        | `BenchmarkPerformance`            | Loads benchmark price-based performance             |
| `pba.py`                       | `PortfolioBenchmarkAssociation`   | Maps portfolios to appropriate benchmarks           |
| `benchmarkcharacteristics.py`  | `BenchmarkCharacteristics_New`   | Uploads monthly benchmark metrics                   |
| `portfolioperformance.py`      | `PortfolioPerformance_New`        | Computes and uploads daily REIT portfolio returns   |

---

## Setup Instructions

### 1. Install Required Python Packages

```
pip install pandas numpy yfinance snowflake-connector-python python-dotenv
```

### 2. Environment Configuration

```
SNOWFLAKE_USER=your_username
SNOWFLAKE_PASSWORD=your_password
SNOWFLAKE_ACCOUNT=your_account_id
SNOWFLAKE_ROLE=AST_REALESTATE_DB_RW
SNOWFLAKE_WAREHOUSE=AST_BU_WH
SNOWFLAKE_DATABASE=AST_REALESTATE_DB
SNOWFLAKE_SCHEMA=DBO
```

---

## Usage Guidelines

**Metadata Setup:**
- security_master.py: Load base security metadata
- bgi.py: Load benchmark codes (e.g., VNQ, IYR)
- pm.py: Classify and load products (Institutional, Retail)
- pgi.py: Define REIT portfolios and metadata

**Holdings & Performance:**
- Holdings Details.py: Build enriched REIT holdings from SECURITY_MASTER
- bp.py: Load normalized benchmark performance (Total Return Index)
- portfolioperformance.py: Calculate market-cap-weighted portfolio returns
- benchmarkcharacteristics.py: Upload performance stats (Sharpe, Volatility, Drawdown)

**Mapping Logic:**
- pba.py: Match REIT portfolios to relevant benchmarks via name matching
