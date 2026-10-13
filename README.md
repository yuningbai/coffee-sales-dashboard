# Coffee Sales Dashboard — team15

**Status:** Deployed on Streamlit Community Cloud.

**Live app:** https://coffee-sales-dashboard-fdtb7ksaxdhrqpptjj3zr2.streamlit.app/
**Team members:** Chen, Junxian; Yu, Boyan; Bai, Yuning; Qian, Hongyi.

Each student will publish their own repository, README, and app.

## 1. Dataset and decision-maker

We use **`index_1.csv`** from [Coffee Sales by ihelon on Kaggle](https://www.kaggle.com/datasets/ihelon/coffee-sales). The author describes real coffee vending-machine sales. **One row is one recorded transaction.**

We use only index_1.csv because its year-long transaction history is sufficient to examine demand trends and drink mix. We keep the dashboard focused on this single data source; index_2.csv is outside the selected scope and is not included in any calculations.

| Property | Value |
|---|---|
| Actual file period | March 1, 2024–March 23, 2025 |
| Size | 3,636 rows; six columns; 279,369 bytes (about 0.28 MB) |
| Fields | `date`, `datetime`, `cash_type`, `card`, `money`, `coffee_name` |
| Amount and identifier | `money`: Ukrainian hryvnias (UAH); `card`: anonymous card identifier |
| Preparation | Original small CSV; no sampling or aggregation needed |
| Dataset license | CC0 Public Domain on Kaggle; an MIT license for our code does not change this source license |

The decision-maker is **a coffee vending-machine operations manager**. The following operating scenario is our proposed use case, not an account of an interview with the dataset owner.

## 2. Why a dashboard?

- **Who and how often?** The manager checks performance each morning and reviews drink demand weekly before allocating replenishment supplies.
- **What decision?** If a drink's transaction volume and share increase persistently, the manager will review its ingredient allocation for the next replenishment cycle. Recipes and current stock determine actual quantities. If overall transactions fall sharply, the manager will contact the site to investigate.
- **Without the dashboard:** In our scenario, the manager repeatedly exports sales and creates spreadsheet summaries. This costs preparation time, risks inconsistent date selections, and can delay action. We have not measured a specific time or monetary saving.
- **Why this format?** These are recurring questions for the same person. Date filtering and drink comparisons help investigate changes. A fixed weekly email offers less flexibility; an alert flags a problem without its context; a one-off report does not support continuing operations.

## 3. EDA and display choices

The Pandas and Matplotlib notebook, [`coffee-sales-eda.ipynb`](coffee-sales-eda.ipynb), checks shape, types, missing values, duplicates, date coverage, amount distributions, drink demand, and changes over time.

**Findings that inform the design:**

- Dates, amounts, and drink categories have no missing values; there are no fully duplicated rows. Only `card` has missing values (89), all corresponding to cash payments. These transactions remain in operating totals because a missing identifier does not invalidate a sale.
- There are eight drink types. Americano with Milk and Latte account for **44.2%** of transactions, making drink counts and shares useful for allocation discussions.
- Seven dates have no records. March 2025 ends on the 23rd, so its total is not comparable with a full month. We need explicit boundaries and gap warnings.
- Amounts range from **18.12 to 40.00 UAH** and vary within drink types. Revenue reflects more than purchase volume, supporting transactions as the leading metric.

### Headline metrics

| Metric | Definition | Decision value |
|---|---|---|
| **Transactions — most important** | Recorded row count in the selected period | Available measure of purchase activity, less directly affected by prices than revenue |
| Revenue (UAH) | Sum of transaction amounts | Financial scale of activity |
| Average transaction amount (UAH) | Revenue / transaction count | Helps separate volume changes from average spending changes; affected by prices and drink mix |

### Page order

1. **Context:** Team title, audience and purpose, snapshot period, intended refresh, and a sidebar date-range filter, initially set to the full snapshot. One machine means no useful store selector.
2. **Top:** Three side-by-side headline metrics using `st.columns` and `st.metric`, with transactions first. Any period comparison must use comparable reporting coverage.
3. **Middle:** A daily transaction trend shows when activity changes. A horizontal chart of transactions by drink, labeled with shares, shows the demand mix behind replenishment choices.
4. **Bottom:** Brief data limitations. No raw transaction table: the planned decisions require aggregate demand, not individual records.

This page order puts overall performance first, then explanations, so the manager can understand the main message quickly. The exercise also asks the team to sketch this order on paper before app development.

**Two deliberate exclusions:** Payment-method charts do not directly support ingredient allocation. Anonymous card identifiers and customer-level charts are also excluded: they do not support the chosen decisions and cannot provide a complete customer count, especially for cash purchases.

### Misleading interpretations and handling

- **Missing dates:** No rows do not prove zero sales. Preserve gaps and flag uncertain coverage instead of silently filling zeros. Label partial periods.
- **Missing business fields:** There is no quantity, inventory, recipe, or cost data. Say *transactions*, not cups; do not calculate profit, claim a stockout, or prescribe exact replenishment quantities. Declining sales prompt investigation rather than prove a machine fault.
- **Historical data:** Clearly label the CSV's actual period and snapshot status. A changing Kaggle description does not make this file a live feed.

## 4. Refresh, review, and action

**Refresh nightly; review the next morning; adjust ingredient allocation weekly.** Nightly imports support the next review without the integration and monitoring cost of minute-by-minute refresh. Immediate machine faults should use separate device alerts.

Track weekly transaction count. As a **provisional business rule**, a decline of **more than 20%** from the previous complete Monday–Sunday week prompts a call to the site. First verify complete reporting and consider holidays and opening schedules; then inspect machine availability and actual inventory. This threshold is a proposed starting point, not a validated finding, and requires calibration with operating history.

A **one-day delay** can postpone investigation. A **one-week delay** can allow a problem to persist and lead to replenishment based on outdated demand, increasing the risk of shortages or unnecessary stock.

The dashboard displays the actual data period, **Kaggle snapshot**, and **Intended production refresh: nightly**. This project uses a static CSV; the production refresh describes the proposed business process.

## 5. Project files and deployment

| File | Purpose |
|---|---|
| `app.py` | Streamlit dashboard entrypoint |
| `index_1.csv` | Original Kaggle data, beside the app |
| `requirements.txt` | Streamlit, Pandas, and Matplotlib dependencies |
| `coffee-sales-eda.ipynb` | Exploratory analysis, outputs, and design evidence |
| `README.md` | Business justification and project instructions |

Upload the files to the root of your own public GitHub repository. In Streamlit Community Cloud, select that repository, branch `main`, and entrypoint `app.py`. Select **Python 3.14** in Advanced settings, as required by the exercise, then deploy.

After deployment, add the live app link here and verify that another person can open it. Submit the live app URL and your own GitHub repository URL on Canvas.
