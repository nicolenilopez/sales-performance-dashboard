# Sales Performance Dashboard

An interactive **Power BI sales analytics dashboard** for exploring sales performance, profitability, product results, and customer purchasing behavior through three report pages.

Built with **Adventure Works sample data** for portfolio demonstration. The report is intended to showcase analytical reporting and Power BI development rather than a real organization's financial performance.

## Dashboard preview

Screenshots of the three report pages will be added to the `screenshots/` folder.

## Business objectives

- Monitor sales, profit, order volume, and average order value over time.
- Compare key performance indicators against the previous year.
- Explore sales and profitability across geographic markets.
- Identify high-performing product categories, subcategories, and products.
- Analyze customer activity, repeat purchasing, and sales-channel performance.

## Report pages

| Page | What it helps analyze |
| --- | --- |
| **Sales Dashboard** | Overall sales, profitability, orders, average order value, yearly comparisons, and geographic performance. |
| **Products** | Product and category results, quantity sold, average selling price, and profitability. |
| **Customers** | Customer counts, sales per customer, repeat customer rate, customer orders, channels, and geographic patterns. |

For the purpose and business questions behind each page, see [Report pages and business questions](docs/report-pages.md).

## Technical implementation

- **Power BI Desktop:** Three-page interactive report with KPI measures and category, geography, time, and customer breakdowns.
- **DAX:** Sales, profit, year-over-year comparisons, average order value, average selling price, and customer activity metrics.
- **Data model:** Report visuals reference Date, Product, Customer, Sales Territory, Sales Order, and Channel entities, with calculations organized under `_Measures`.

See [Data model and relationships](docs/data-model.md) for the model entities confirmed from report definitions and the relationships still requiring validation.

## Key metrics

Total Sales · Profit · Profit Margin % · Total Orders · Average Order Value · Quantity Sold · Average Selling Price · Total Customers · Sales per Customer · Repeat Customer Rate · Year-over-Year %

## Technical documentation

- [Data model and relationships](docs/data-model.md)
- [Selected DAX measures and calculation logic](docs/dax-measures.md)
- [Report pages and business questions](docs/report-pages.md)

## Data and scope

The report is presented as an **Adventure Works sample-data** portfolio project. Its report definitions contain references to published Power BI artifacts; review the PBIX's Power Query connections, embedded data, and privacy implications before uploading it publicly.

The downloadable PBIX and actual report screenshots can be added after the source and privacy checks are complete.
