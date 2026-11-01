# Data Model

The Sales Performance Dashboard uses a **star-schema-oriented data model**, with a central `Sales` fact table connected to dimensions for dates, products, customers, resellers, sales territories, sales orders, and currencies. The model also includes supporting tables for exchange rates and sales-reason associations.

![Sales Performance Dashboard data model](../screenshots/data-model.png)

Solid relationship lines indicate active relationships, while dashed lines represent inactive relationships used for alternative date associations.

## Fact table

| Table | Description |
| --- | --- |
| `Sales` | Stores sales transactions, including sales amounts, product costs, order quantities, and transaction identifiers. |

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

The model contains 11 active relationships connecting the `Sales` fact table, dimensions, and supporting tables.

| From column | To column |
| --- | --- |
| `Sales[SalesTerritoryKey]` | `Sales Territory[SalesTerritoryKey]` |
| `Sales[ProductKey]` | `Product[ProductKey]` |
| `Sales[SalesOrderLineKey]` | `Sales Order[SalesOrderLineKey]` |
| `Sales[CustomerKey]` | `Customer[CustomerKey]` |
| `Sales[OrderDateKey]` | `Date[DateKey]` |
| `Sales[ResellerKey]` | `Reseller[ResellerKey]` |
| `Sales[CurrencyKey]` | `Currency[CurrencyKey]` |
| `Currency Rate[CurrencyKey]` | `Currency[CurrencyKey]` |
| `Currency Rate[DateKey]` | `Date[DateKey]` |
| `Sales Reason Bridge[SalesReasonKey]` | `Sales Reason[SalesReasonKey]` |
| `Sales Reason Bridge[SalesOrderLineKey]` | `Sales Order[SalesOrderLineKey]` |

The `Sales` table also has two inactive relationships with the `Date` dimension, through `DueDateKey` and `ShipDateKey`. These provide alternative date paths when required.

The relationships involving `Sales Order` use bidirectional cross-filtering, with the `Sales`–`Sales Order` relationship configured as one-to-one.

## Supporting tables

| Table | Purpose |
| --- | --- |
| `Currency Rate` | Stores exchange-rate information by date and currency. |
| `Sales Reason Bridge` | Associates sales orders with their corresponding sales reasons. |
| `Sales Reason` | Provides descriptions and classifications of sales reasons. |

The model also includes `_Measures`, a dedicated table used to organize DAX measures separately from the underlying sales and supporting tables.

## Calculation approach

Base measures aggregate sales amounts, product costs, order quantities, and distinct sales orders. Additional DAX measures calculate profitability, profit margins, average order value, and year-over-year comparisons.

Customer measures are restricted to the Internet sales channel, supporting customer activity and repeat-purchase analysis. This distinction ensures that customer-specific KPIs are interpreted separately from overall sales performance.

See [DAX Measures & Calculation Logic](dax-measures.md) for selected measure definitions.
