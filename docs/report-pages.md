# Report pages and business questions

The Sales Performance Dashboard consists of three pages: **Sales Dashboard**, **Products**, and **Customers**. Together, they provide an overview of revenue and profitability, product performance, and Internet customer purchasing behavior.

## 1. Sales Dashboard — Performance Overview

![Sales Performance Overview](../screenshots/Sales%20Performance%20Dashboard%20-%20Overview.png)

**Purpose:** Summarize sales performance, profitability, order activity, and year-over-year trends.

**Visuals**
- **KPI cards** — Total Sales, Profit, Profit Margin, Total Orders, and Average Order Value, with prior-year comparisons.
- **Monthly sales trends** — Sales performance over time compared with the previous year.
- **Sales by product category** — Revenue distribution across product categories.
- **Geographic sales analysis** — Sales performance across countries and territories.

**Business questions**
- How are sales, profitability, and order activity performing compared with the previous year?
- How does monthly sales performance change over time?
- Which product categories contribute the most revenue?
- How does sales performance vary across geographic markets?

**Metric definition:** Profit represents sales revenue minus product cost, rather than net income after operating expenses.

## 2. Products — Product Performance

![Product Performance](../screenshots/Sales%20Performance%20Dashboard%20-%20Products.png)

**Purpose:** Analyze sales, profitability, quantities sold, and pricing across products and product categories.

**Visuals**
- **Product performance KPIs** — Sales, profit, profit margin, quantity sold, and average selling price.
- **Sales by product category** — Comparison of revenue performance across product categories.
- **Top-performing products** — Product-level ranking based on sales performance.
- **Product performance details** — Product sales, quantities, pricing, and profitability comparisons.

**Business questions**
- Which products and categories generate the most revenue?
- Which products have the highest sales volume?
- How do average selling prices compare with the previous year?
- How does profitability vary across products and categories?

## 3. Customers — Internet Customer Analysis

![Customer Analysis](../screenshots/Sales%20Performance%20Dashboard%20-%20Customers.png)

**Purpose:** Analyze Internet customer purchasing behavior, purchase frequency, trends, and geographic distribution.

**Business questions**
- How many distinct Internet customers are active in the selected period?
- How much revenue does an Internet customer generate on average?
- How many customers placed exactly one order, versus two or more distinct orders?
- How is the number of active customers changing month by month and versus the previous year?
- Where are Internet customers located, and which customers generate the most sales?

**Page visuals**
- **KPI cards:** Total Customers, Sales per Customer, and Repeat Customer Rate, each with prior-year comparisons.
- **Customer Purchase Frequency:** Horizontal bars comparing One-Time Customers and Repeat Customers, showing customer counts.
- **Active Customers by Month:** Current-period customers versus the prior year.
- **Customers by Country:** Geographic distribution of customer activity.
- **Top 10 Customers:** Customer-level sales, order counts, and average order value.

**Key measures:** `Total Customers`, `Customer Sales`, `Customer Orders`, `Customer AOV`, `Sales per Customer`, `Repeat Customer Rate`, `One-Time Customers`, `Repeat Customers`, `Customers PY`, `Customers YoY %`, and `Sales per Customer YoY %`.

**Important metric definition:** Customer metrics use the **Internet** sales channel. One-time customers have exactly one distinct Internet order, while repeat customers have at least two, within the current filter context. This is purchase frequency within the selected period, not lifetime retention.

## Filtering and interpretation

The report uses date, product, geographic, and customer-related attributes for interactive breakdowns. Prior-year calculations use `'Date'[Date]` via `SAMEPERIODLASTYEAR`. Exact slicer interaction behavior is defined by the report configuration.

The report's profit metric means **sales minus product cost**, not net income after all operating expenses.

For implementation details, see [Data model and relationships](data-model.md) and [Selected DAX measures](dax-measures.md).
