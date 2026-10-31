# Data Model

The Sales Performance Dashboard uses a **star-schema-oriented data model**, with a central `Sales` fact table connected to dimensions for dates, products, customers, resellers, sales territories, sales orders, and currencies. Additional tables support currency-rate analysis and sales-reason associations.

![Sales Performance Dashboard data model](../screenshots/data-model.png)

Solid relationship lines indicate active relationships, while dashed lines represent inactive relationships used for alternative date associations.

## Fact table

| Table | Description |
| --- | --- |
| `Sales` | Stores sales transactions, including sales amounts, product costs, order quantities, and transaction keys. |

The `Sales` table is the central fact table used to calculate revenue, profitability, sales volume, and order-related metrics across the report.

## Dimensions

| Table | Role |
| --- | --- |
| `Date` | Provides calendar and fiscal attributes for time-based analysis and prior-year comparisons. |
| `Product` | Provides product information and categories for product performance analysis. |
| `Customer` | Provides customer attributes for Internet customer analysis. |
| `Reseller` | Provides reseller information for reseller sales analysis. |
| `Sales Territory` | Enables geographic analysis by sales territory and country. |
| `Sales Order` | Provides sales order identifiers and sales-channel classification. |
| `Currency` | Provides currency identifiers and related attributes. |

## Relationships

The central `Sales` fact table connects to its dimensions, primarily through one-to-many relationships. Additional relationships support date analysis, currency rates, and sales reasons.

| Related table | Connected table |
| --- | --- |
| `Sales` | `Date` |
| `Sales` | `Product` |
| `Sales` | `Customer` |
| `Sales` | `Reseller` |
| `Sales` | `Sales Territory` |
| `Sales` | `Sales Order` |
| `Sales` | `Currency` |
| `Currency Rate` | `Date` |
| `Currency Rate` | `Currency` |
| `Sales Reason Bridge` | `Sales Order` |
| `Sales Reason Bridge` | `Sales Reason` |

The model includes active and inactive relationships between `Sales` and `Date`, representing different sales-related date contexts. The sales-reason bridge supports associations between sales orders and their corresponding sales reasons.

The table summarizes relationships at the **table level**. Exact relationship columns, cardinalities, and cross-filter directions should be verified in Power BI Desktop before documenting them individually.

## Supporting tables

| Table | Purpose |
| --- | --- |
| `Currency Rate` | Stores exchange-rate information by date and currency. |
| `Sales Reason Bridge` | Associates sales orders with their corresponding sales reasons. |
| `Sales Reason` | Provides descriptions and classifications of sales reasons. |

The model also includes `_Measures`, a dedicated table used to organize DAX measures separately from the underlying sales and supporting tables.

## Calculation logic confirmed by TMDL

The exported `_Measures` TMDL supplies additional evidence about how the model is used:

- `Total Sales` sums `Sales[Sales Amount]`.
- `Total Cost` sums `Sales[Total Product Cost]`; `Profit` is sales minus this product cost.
- `Quantity Sold` sums `Sales[Order Quantity]`.
- `Total Orders` counts distinct `'Sales Order'[Sales Order]` identifiers.
- Prior-year measures use `SAMEPERIODLASTYEAR('Date'[Date])`.
- Customer measures explicitly apply `'Sales Order'[Channel] = "Internet"`.
- `Repeat Customer Rate` counts customers with two or more distinct Internet orders within the active filter context, then divides by `Total Customers`.

These details are important because **overall sales measures and Internet-channel customer measures do not necessarily represent the same population**.

For selected exact formulas, see [DAX measures and calculation logic](dax-measures.md).

## Modeling considerations

The sales transaction, order, customer, and exchange-rate tables operate at different levels of detail. Relationships, filter direction, and date context therefore matter when interpreting totals. The bridge table also needs attention to prevent order-level sales reasons from being mistaken for independent sales-line amounts.

**Scope:** The visual model and provided TMDL confirm the structure and listed calculations, but do not independently verify Power Query source connections, credentials, or all imported data.
