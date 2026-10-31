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

## Tables and roles

| Table | Role |
| --- | --- |
| `Currency Rate` | Exchange-rate information by date and currency |
| `Sales Reason Bridge` | Bridge table associating sales orders with sales reasons |
| `Sales Reason` | Sales-reason descriptions and classifications |
| `_Measures` | Dedicated table containing DAX measures used throughout the report |

## Visible relationship structure

- **Central sales model:** Product, Customer, Reseller, Sales Territory, Sales Order, and Currency connect to `Sales`. The diagram shows one-to-many markers for the primary dimension-to-fact relationships.
- **Multiple date roles:** `Date` has multiple relationships with `Sales`, including inactive paths. This allows the model to represent different sales-related dates, subject to the DAX and relationship configuration.
- **Currency rates:** `Currency Rate` connects into the Date and Currency area of the model and has a different grain from sales transactions.
- **Sales reasons:** `Sales Reason Bridge` associates `Sales Order` with `Sales Reason`, rather than treating each reason as a single attribute on a sales line.
- **Measure organization:** `_Measures` appears separately, without physical relationships.

The screenshot establishes the table layout and visible relationship paths. It does **not** reveal every linked key column, relationship filter direction, or other configuration; those details should be verified in Power BI Desktop before making more specific claims.

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
