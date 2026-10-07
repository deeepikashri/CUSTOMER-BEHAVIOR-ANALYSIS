# Customer Behavior Analysis

End-to-end retail analytics workflow: clean and enrich customer shopping data with **pandas**, load it into **PostgreSQL**, and explore it in an interactive **Power BI** dashboard.

```
CSV  →  pandas (clean + feature engineering)  →  PostgreSQL  →  Power BI dashboard
```

## Dashboard

`dashboard/customer_behavior_analysis.pbix` is a single-page report built on the `customer` table:

- **KPI cards:** number of customers, average purchase amount, average review rating
- **Subscription mix:** share of customers by subscription status (donut chart)
- **Category performance:** revenue and sales by product category
- **Demographics:** revenue and sales by age group
- **Interactive filters:** subscription status, gender, category, shipping type

> Add a screenshot of the report here (e.g. `docs/dashboard.png`) so visitors can preview it without opening Power BI. GitHub can't render `.pbix` files.

## Data preparation (`notebooks/pandas_customer_analysis.ipynb`)

| Step | What it does |
|---|---|
| Explore | `info()` and `describe()` to profile the data |
| Missing values | Fills missing `Review Rating` values with the median rating of the same category |
| Standardize | Lowercase snake_case column names; `purchase_amount_(usd)` → `purchase_amount` |
| Age groups | Quartile-based `age_group`: young adult, adult, middle-aged, senior |
| Purchase frequency | Converts text frequencies (weekly, monthly, ...) into `purchase_frequency_days` |
| Redundancy check | Confirms `promo_code_used` duplicates `discount_applied`, then drops it |
| Load | Writes the cleaned table to PostgreSQL (`customer`) for Power BI |

## Requirements

- Python 3.9+
- [PostgreSQL](https://www.postgresql.org/download/) (only needed for the load step and Power BI)
- [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (Windows) to open the dashboard
- The dataset (see [`data/README.md`](data/README.md))

## Setup

```bash
git clone https://github.com/<your-username>/customer-behavior-analysis.git
cd customer-behavior-analysis

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

Put `customer_shopping_behavior.csv` in `data/`, then:

```bash
cd notebooks
jupyter lab
```

Open `pandas_customer_analysis.ipynb` and run all cells.

### Loading into PostgreSQL

Create an empty database (default name `customer_behavior`) and set credentials as environment variables **before** launching Jupyter. No credentials are stored in the notebook.

```bash
export DB_PASSWORD="your-password"   # required for the load step
export DB_USER="postgres"            # optional, default: postgres
export DB_HOST="localhost"           # optional, default: localhost
export DB_PORT="5432"                # optional, default: 5432
export DB_NAME="customer_behavior"   # optional, default: customer_behavior
```

PowerShell: `$env:DB_PASSWORD = "your-password"`. If `DB_PASSWORD` isn't set, the notebook still runs and just skips the database step.

### Opening the dashboard

Open `dashboard/customer_behavior_analysis.pbix` in Power BI Desktop. The report reads the `public.customer` table, so run the notebook first. If your PostgreSQL connection differs from the original, update it under **Home → Transform data → Data source settings**, then **Refresh**.

## Project structure

```
customer-behavior-analysis/
├── notebooks/
│   └── pandas_customer_analysis.ipynb   # data cleaning + PostgreSQL load
├── dashboard/
│   └── customer_behavior_analysis.pbix  # Power BI report
├── data/                                # place the CSV here (git-ignored)
├── requirements.txt
└── README.md
```

## Roadmap

- [ ] Dashboard screenshot in the README
- [ ] Exploratory analysis section with charts and key findings
- [ ] SQL queries for the main business questions
- [ ] Move the cleaning logic into a reusable script

## License

MIT, see [LICENSE](LICENSE).
