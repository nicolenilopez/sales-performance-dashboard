# Data model and relationships

## Source and scope

The Sales Performance Dashboard is described as an **Adventure Works sample-data** report. The PBIX contains a Power BI embedded data model and report definitions, but this document does not assume that all relationships or Power Query transformation steps have been independently verified.

## Entities confirmed from report visual references

| Entity | Role in report visuals |
| --- | --- |
| `Date` | Time-related analysis and fiscal year/month selections |
| `Product` | Product, category, and subcategory analysis |
| `Customer` | Customer metrics and segmentation |
| `Sales Territory` | Geographic comparison and country-level analysis |
| `Sales Order` | Order-related analysis |
| `Channel` | Sales channel comparisons |
| `_Measures` | Dedicated table containing report measures |

These are visual query references, not a full inventory of the imported model's tables.

## Key modeling considerations

- Consistent date filtering is important for prior-year and year-over-year calculations.
- Product and territory dimensions support group comparisons and drilldowns.
- Customer calculations, especially repeat customer rate, depend on the underlying definition of repeat purchase.
- Order counts, customer counts, and quantity sold may have different grains; validating the fact-table relationships is essential.

## Relationships to verify in Power BI Desktop

Open **Model view** and confirm the following before replacing this section with exact cardinalities and filter directions:

1. The sales fact table and its relationships to Date, Product, Customer, Sales Territory, and Sales Order.
2. How the Channel entity filters sales results.
3. The active date relationship and any alternative date roles.
4. Any many-to-many or bidirectional filtering relationships.
5. Whether `_Measures` is a measures-only table.

## Recommended diagram

Export a screenshot of **Model view** and save it as `screenshots/data-model.png`. This will allow readers to understand the physical relationships without relying on assumptions in the documentation.
