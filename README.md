# Sales Performance Dashboard

An interactive **Power BI sales performance dashboard** for analyzing revenue, profitability, product performance, and customer purchasing behavior across different markets.

Built using the **Adventure Works sample dataset** for portfolio demonstration. All results are illustrative and intended to showcase interactive reporting, data modeling, and DAX-based business analysis.

## Dashboard preview

![Sales Performance Overview](screenshots/Sales%20Performance%20Dashboard%20-%20Overview.png)

The **Overview** brings together Total Sales, Profit, Profit Margin, Total Orders, and Average Order Value KPIs, with year-over-year comparisons, monthly sales trends, product category performance, and geographic analysis.

## Business objectives

- Monitor Total Sales, Profit, Profit Margin, Total Orders, and Average Order Value.
- Track monthly sales performance and compare key metrics against the previous year.
- Analyze sales and profitability across product categories and geographic markets.
- Identify top-performing products and evaluate their contribution to revenue.
- Understand Internet customer purchasing behavior, repeat purchases, and geographic distribution.

## Report pages

| Page | What it helps analyze |
| --- | --- |
| **Overview** | Sales performance, profitability, order trends, year-over-year comparisons, and geographic analysis. |
| **Products** | Product and category sales, quantity sold, average selling price, and profit margins. |
| **Customers** | Internet customer behavior, purchase frequency, monthly activity, and geographic distribution. |

### Products — Product Performance

![Product Performance](screenshots/Sales%20Performance%20Dashboard%20-%20Products.png)

Analyze product and category sales, quantities sold, average selling prices, and profit margins to identify top-performing products.

### Customers — Internet Customer Analysis

![Customer Analysis](screenshots/Sales%20Performance%20Dashboard%20-%20Customers.png)

Analyze Internet customer activity through purchasing frequency, monthly trends, geographic distribution, and top customers. One-time and repeat customer counts highlight purchasing behavior within the selected period.

For the purpose and business questions behind each page, see [Report pages and business questions](docs/report-pages.md).

## Technical implementation

- **Power BI Desktop:** Three-page interactive report featuring KPI cards, time-series comparisons, product analysis, customer insights, and geographic visualizations.
- **Data modeling:** Central `Sales` fact table connected to shared dimensions, with supporting tables for currency conversion and sales-reason analysis.
- **DAX:** Sales and profitability measures, year-over-year comparisons, order metrics, and Internet customer purchasing and repeat-order calculations.
- **Model organization:** Dedicated `_Measures` table for calculations, with separate `Currency Rate` and `Sales Reason Bridge` structures.

For a closer look at the model, see [Data model and relationships](docs/data-model.md).

## Key metrics

Total Sales · Profit · Profit Margin % · Total Orders · Average Order Value · Quantity Sold · Average Selling Price · Total Customers · Sales per Customer · Repeat Customer Rate · One-Time Customers · Repeat Customers · Sales YoY %

## Technical documentation

- [Data model and relationships](docs/data-model.md)
- [Selected DAX measures and calculation logic](docs/dax-measures.md)
- [Report pages and business questions](docs/report-pages.md)

## Data and scope

The dashboard uses the **Adventure Works sample dataset** for portfolio demonstration. Sales figures and business results are illustrative and do not represent the performance of a real organization.
