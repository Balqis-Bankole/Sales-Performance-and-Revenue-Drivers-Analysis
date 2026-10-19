# Sales Performance and Revenue Drivers Analysis

**SQL data warehousing and business analysis for a bicycle business**

This project brings customer, product and sales data from CRM and ERP sources into a structured warehouse to explore a practical business question: **what contributes to revenue, and where should the business focus its attention?**

I developed a workflow covering data ingestion, cleaning, integration, dimensional modelling and SQL analysis. The repository demonstrates how I organise operational data into a foundation for sales reporting and business decisions.

**Core skills:** SQL · ETL · Data cleaning · Data modelling · Window functions · Customer segmentation · KPI reporting · Data quality checks

## Explore the project

- [Sales trends and growth analysis](scripts/analysis/sales_performance.sql)
- [Product performance analysis](scripts/analysis/product_performance.sql)
- [Customer performance and segmentation](scripts/analysis/customer_performance.sql)
- [Warehouse model](scripts/gold/ddl_gold.sql)
- [Data catalogue](docs/data_catalog.md)

## Business questions

| Area | Questions explored | Decision supported |
| --- | --- | --- |
| Sales performance | How do revenue, orders, units sold and average order value change over time? | Monitor performance and investigate changes in demand. |
| Product performance | Which products, categories and product lines generate the most revenue? | Inform product prioritisation and merchandising. |
| Customer value | Who are the highest-value customers, and how does purchasing behaviour vary? | Support customer segmentation and retention planning. |
| Geographic markets | How do revenue and revenue per customer differ by country? | Compare market performance. |
| Revenue concentration | How dependent is revenue on leading products and customers? | Explore concentration and diversification opportunities. |

These are analytical use cases, rather than claims of measured business impact.

## What I built

### 1. A three-layer data warehouse

| Layer | Purpose | Implementation |
| --- | --- | --- |
| **Bronze** | Preserve source records for transformation. | CRM and ERP tables populated from CSV files through a loading procedure. |
| **Silver** | Clean and standardise source data. | Transformations for duplicate customer records, text formatting, category codes, identifiers, dates and sales values. |
| **Gold** | Organise data for analysis. | Customer and product dimension views linked to a sales fact view. |

![Data warehouse architecture](docs/data%20arch.drawio.png)

### 2. Data cleaning and integration

The transformation scripts include logic to:

- Retain the latest customer record using `ROW_NUMBER()`.
- Trim names and standardise gender, marital status, country and product-line values.
- Align customer identifiers across CRM and ERP sources.
- Separate product identifiers and category information.
- Handle invalid date values and derive product end dates using `LEAD()`.
- Recalculate inconsistent sales amounts and handle missing or non-positive prices.

The [Silver quality checks](tests/quality_checks_silver.sql) inspect duplicates, missing identifiers, unwanted spaces, inconsistent values and date ordering. Additional [Gold checks](tests/quality_checks_gold.sql) target dimension-key uniqueness.

### 3. A dimensional model

The Gold layer uses three analytical views:

- **`gold.dim_customers`** — customer identifiers, demographics and country.
- **`gold.dim_products`** — product attributes, categories, product lines and cost.
- **`gold.fact_sales`** — sales records with order dates, customer and product keys, sales amounts, quantities and prices.

![Sales data model](docs/Data%20Model.drawio.png)

See the [data catalogue](docs/data_catalog.md) for field descriptions.

### 4. Business-focused SQL analysis

The analysis scripts use joins, common table expressions, conditional aggregation and window functions to explore:

- Annual and monthly revenue trends.
- Year-over-year revenue growth.
- Cumulative revenue and moving averages.
- Top and bottom products by revenue.
- Revenue contribution by category and market.
- Customer purchasing frequency and repeat-customer rate.
- Customer segments based on observed purchasing lifespan and spending.
- Revenue concentration among leading customers and products.

Customer segmentation in the analysis defines **VIP** customers as those with at least 12 months between their first and last observed orders and spending above 5,000; **Regular** customers meet the same lifespan threshold with spending at or below 5,000. Other customers are labelled **New**. These are project-defined groups; “New” does not necessarily mean recently acquired.

## Key metrics

| Metric | Definition |
| --- | --- |
| Revenue | Sum of sales amounts. |
| Orders | Count of distinct order numbers. |
| Active customers | Distinct customers represented in sales records for the selected period. |
| Units sold | Sum of quantities sold. |
| Average order value | Revenue divided by distinct orders. |
| Year-over-year growth | Revenue change divided by the previous year's revenue, multiplied by 100. |
| Revenue contribution | A segment's revenue divided by the relevant total revenue, multiplied by 100. |
| Repeat-customer rate | Customers with more than one distinct order divided by active customers, multiplied by 100. |

## Tools

- **SQL** for data preparation, modelling and analysis.
- **Postgres** as the database engine used by the loading procedures and analytical queries.
- **Draw.io** for architecture, integration, data-flow and model diagrams.
- **GitHub** for project documentation and version control.

## Repository guide

| Location | Contents |
| --- | --- |
| [scripts/bronze](scripts/bronze) | Raw-table definitions and CSV loading procedure. |
| [scripts/silver](scripts/silver) | Cleaned-table definitions and transformation procedure. |
| [scripts/gold](scripts/gold) | Analytical dimension and fact views. |
| [scripts/exploratory_analysis](scripts/exploratory_analysis) | Exploratory SQL queries. |
| [scripts/analysis](scripts/analysis) | Sales, customer, product, geography and concentration analysis. |
| [scripts/reports](scripts/reports) | Reporting-view definitions. |
| [tests](tests) | SQL data quality checks. |
| [docs](docs) | Diagrams and data catalogue. |
| [datasets](datasets) | Placeholder for source data; CSV files are not currently included. |

## Reviewing and reproducing the work

The SQL and diagrams can be reviewed directly on GitHub without a database.

To reproduce the workflow, the required source files are `cust_info.csv`, `prd_info.csv`, `sales_details.csv`, `CUST_AZ12.csv`, `LOC_A101.csv` and `PX_CAT_G1V2.csv`. Update the file paths in the Bronze loading procedure to match a location accessible to the database server.

The intended execution order is:

1. Create the database and connect to it; create the Bronze, Silver and Gold schemas.
2. Create the Bronze tables and load the source CSVs.
3. Create the Silver tables and run the transformation procedure.
4. Review the Silver quality checks.
5. Create the Gold views and review their keys and relationships.
6. Run exploratory and business analysis queries, then create reporting views.

**Current status:** the repository contains the warehouse and analysis scripts, quality-check queries and design documentation. Source CSVs, saved analytical results and a dashboard link are not currently included. Some scripts also need syntax, identifier and join alignment before a complete rerun; for example, the setup script contains `GO` separators and the customer reporting view contains inconsistent column references. Numerical findings should be published after the workflow is reconciled and its outputs validated.

## Author

**Balqis Bankole**  
Data analytics portfolio focused on turning raw data into clear, useful business information.

[GitHub profile](https://github.com/Balqis-Bankole)

## Licence

This repository includes an [MIT licence](LICENSE).
