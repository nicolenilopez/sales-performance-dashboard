# Selected DAX measures and calculation logic

## Scope of this documentation

This document lists the measure names **confirmed in the report visual definitions** and explains the business role of each one. The underlying DAX expressions are not included here because the embedded model's measure definitions have not yet been extracted and validated. The formulas should not be presented as exact source code until verified in Power BI Desktop.

## Sales and profitability

| Measure | Purpose |
| --- | --- |
| `Total Sales` | Primary sales amount used in KPI and breakdown visuals |
| `Profit` | Profitability amount |
| `Profit Margin %` | Profit expressed relative to sales |
| `Sales LY` | Previous-year sales comparison |
| `Sales YoY %` | Relative change in sales versus the previous year |
| `Profit YoY %` | Relative change in profit versus the previous year |

## Orders and products

| Measure | Purpose |
| --- | --- |
| `Total Orders` | Order volume |
| `Average Order Value` | Sales value per order |
| `Orders YoY %` | Year-over-year order change |
| `AOV YoY %` | Year-over-year change in average order value |
| `Quantity Sold` | Sales units |
| `Quantity Sold YoY %` | Year-over-year change in units sold |
| `Average Selling Price` | Average realized price per unit |
| `Average Selling Price YoY %` | Year-over-year change in average selling price |

## Customers

| Measure | Purpose |
| --- | --- |
| `Total Customers` | Distinct customer activity according to the model definition |
| `Customer Sales` | Sales in customer-level context |
| `Customer Orders` | Orders in customer-level context |
| `Customer AOV` | Average order value in customer-level context |
| `Sales per Customer` | Sales normalized by customer count |
| `Repeat Customer Rate` | Customer repeat-purchase indicator |
| `Customers PY` | Prior-year customer comparison |
| `Customers YoY %` | Year-over-year customer change |
| `Sales per Customer YoY %` | Year-over-year change in sales per customer |

## Display-focused measures

The report also references measures such as `Sales YoY Display`, `Profit YoY Display`, `Orders YoY Display`, `Profit Margin Change Display`, `AOV YoY Display`, `Customers YoY Display`, and `Repeat Customer Rate Display`.

These appear intended for presentation, but their exact formatting and calculation logic should be verified rather than inferred from their names.

## How to add exact DAX definitions

In Power BI Desktop, select a measure in the **Data** pane and copy its expression from the formula bar. For a portfolio-ready technical write-up, prioritize:

1. `Total Sales` and `Profit`
2. `Profit Margin %`
3. `Sales LY` and `Sales YoY %`
4. `Average Order Value`
5. `Repeat Customer Rate`
6. `Sales per Customer`

For each expression, describe its calculation context, any time-intelligence assumptions, and behavior when the denominator is blank or zero.
