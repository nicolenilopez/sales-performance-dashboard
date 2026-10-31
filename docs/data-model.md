# Data model and relationships

## Model overview

The Sales Performance Dashboard uses a central **`Sales` fact table** surrounded by dimension tables, with supporting currency-rate and sales-reason structures.

![Sales Performance Dashboard data model](../screenshots/data-model.png)

The diagram is from Power BI Desktop's Model view. Solid lines indicate active relationships and dashed lines indicate inactive relationships.

## Tables and roles

| Table | Role |
| --- | --- |
| `Sales` | Central sales fact table, including sales amounts, product costs, order quantities, and transaction keys |
| `Date` | Calendar and fiscal attributes used for time analysis |
| `Product` | Product attributes and groupings |
| `Customer` | Customer identifiers and descriptive attributes |
| `Reseller` | Reseller-related attributes |
| `Sales Territory` | Geographic groupings |
| `Sales Order` | Order identifiers and channel classification |
| `Currency` | Currency information |
| `Currency Rate` | Exchange-rate values by date and currency |
| `Sales Reason Bridge` | Associative table linking sales orders to sales reasons |
| `Sales Reason` | Sales-reason descriptions |
| `_Measures` | Dedicated DAX measures table |

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
