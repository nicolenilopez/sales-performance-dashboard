# Report pages and business questions

The Power BI report contains three pages: **Sales Dashboard**, **Products**, and **Customers**. Each provides a different view of sales performance using the measures defined in the semantic model.

## 1. Sales Dashboard — Performance Overview

![Sales Performance Overview](../screenshots/Sales%20Performance%20Dashboard%20-%20Overview.png)

**Purpose:** Provide a high-level picture of sales, profitability, and sales activity.

**Business questions**
- How are total sales, profit, and order volume performing?
- How do these metrics and average order value compare with the prior year?
- What is the profit margin?
- How does sales performance differ across periods and geographic markets?

**Key measures:** `Total Sales`, `Profit`, `Profit Margin %`, `Total Orders`, `Average Order Value`, `Sales YoY %`, `Profit YoY %`, `Orders YoY %`, and `AOV YoY %`.

## 2. Products — Product Performance

![Product Performance](../screenshots/Sales%20Performance%20Dashboard%20-%20Products.png)

**Purpose:** Identify which products and product groups contribute to commercial results.

**Business questions**
- Which products and categories generate the most sales?
- How many units are sold?
- How does average selling price compare with the previous year?
- How do profit and margin vary across product groups?

**Key measures:** `Total Sales`, `Profit`, `Profit Margin %`, `Quantity Sold`, `Average Selling Price`, `Quantity Sold YoY %`, and `Average Selling Price YoY %`.

## 3. Customers — Customer Analysis

![Customer Analysis](../screenshots/Sales%20Performance%20Dashboard%20-%20Customers.png)

**Purpose:** Analyze customer purchasing behavior and performance.

**Business questions**
- How many Internet-channel customers made qualifying purchases?
- How much sales revenue is generated per customer?
- What percentage of customers placed at least two distinct orders within the selected context?
- How do customer-related metrics compare with the prior year?

**Key measures:** `Total Customers`, `Customer Sales`, `Customer Orders`, `Customer AOV`, `Sales per Customer`, `Repeat Customer Rate`, `Customers YoY %`, and `Sales per Customer YoY %`.

**Important metric definition:** In the supplied DAX, the customer measures above are restricted to the **Internet** channel. The repeat customer rate is based on two or more distinct Internet orders within the current filter context; it is not a lifetime customer retention rate.

## Filtering and interpretation

The report uses date, product, geographic, and customer-related attributes for interactive breakdowns. Prior-year calculations use `'Date'[Date]` via `SAMEPERIODLASTYEAR`. Exact slicer interaction behavior is defined by the report configuration.

The report's profit metric means **sales minus product cost**, not net income after all operating expenses.

For implementation details, see [Data model and relationships](data-model.md) and [Selected DAX measures](dax-measures.md).
