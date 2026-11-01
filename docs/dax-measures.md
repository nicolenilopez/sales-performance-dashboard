# DAX Measures & Calculation Logic

This document highlights selected DAX measures used across the Sales Performance Dashboard's three report pages: **Overview**, **Products**, and **Customers**. The examples cover sales and profitability KPIs, time intelligence, product performance, Internet customer purchasing behavior, and dynamic KPI formatting.

## Overview — Sales performance

The Overview page uses revenue, profitability, order metrics, and time intelligence to summarize sales performance.

### Total Sales

Calculates total revenue within the current filter context.

```dax
Total Sales = SUM(Sales[Sales Amount])
```

### Total Cost

Aggregates product costs used in the profit calculation.

```dax
Total Cost = SUM(Sales[Total Product Cost])
```

### Profit

Calculates sales revenue minus product costs. This represents profit before other operating expenses.

```dax
Profit = [Total Sales] - [Total Cost]
```

### Profit Margin %

Expresses profit as a percentage of total sales. `DIVIDE` returns blank for zero or blank denominators.

```dax
Profit Margin % =
DIVIDE([Profit], [Total Sales])
```

### Total Orders

Counts distinct sales orders within the current filter context.

```dax
Total Orders = DISTINCTCOUNT('Sales Order'[Sales Order])
```

### Average Order Value

Calculates average revenue per order.

```dax
Average Order Value =
DIVIDE([Total Sales], [Total Orders])
```

### Sales LY

Evaluates total sales for the corresponding period in the previous year.

```dax
Sales LY =
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR('Date'[Date])
)
```

The exported model also includes `Sales PY` with the same DAX expression as `Sales LY`.

### Sales YoY %

Calculates the percentage change in sales compared with the previous year.

```dax
Sales YoY % =
DIVIDE(
    [Total Sales] - [Sales LY],
    [Sales LY]
)
```

### Orders PY and Orders YoY %

`Orders PY` calculates distinct sales orders for the corresponding period in the previous year. `Orders YoY %` measures the percentage change compared with that period.

```dax
Orders PY =
CALCULATE(
    [Total Orders],
    SAMEPERIODLASTYEAR('Date'[Date])
)

Orders YoY % =
DIVIDE(
    [Total Orders] - [Orders PY],
    [Orders PY]
)
```

Other prior-year and year-over-year measures follow this pattern for profit, average order value, quantity sold, average selling price, customer counts, and sales per customer.

## Products — Product performance

The Products page uses sales, quantity, pricing, and profitability measures to evaluate product and category performance.

### Quantity Sold

Calculates the total number of units sold within the current filter context.

```dax
Quantity Sold = SUM(Sales[Order Quantity])
```

### Average Selling Price

Calculates average sales revenue per unit sold, rather than an unweighted average of product list prices.

```dax
Average Selling Price =
DIVIDE(
    [Total Sales],
    [Quantity Sold]
)
```

Product profitability is evaluated using the existing `Profit` and `Profit Margin %` measures defined in the Overview section. Prior-year comparisons follow the same time-intelligence pattern.

## Customers — Internet customer analysis

The Customers page uses Internet-channel sales measures to analyze customer activity, purchasing behavior, and repeat purchases. These calculations are restricted to Internet sales and may differ from the overall sales KPIs.

### Total Customers

Counts distinct customers with nonblank customer keys in the Internet sales channel.

```dax
Total Customers =
CALCULATE(
    DISTINCTCOUNT(Sales[CustomerKey]),
    'Sales Order'[Channel] = "Internet",
    NOT ISBLANK(Sales[CustomerKey])
)
```

### Customer Sales and Customer Orders

`Customer Sales` calculates Internet-channel revenue, while `Customer Orders` counts distinct Internet sales orders.

```dax
Customer Sales =
CALCULATE(
    [Total Sales],
    'Sales Order'[Channel] = "Internet"
)

Customer Orders =
CALCULATE(
    DISTINCTCOUNT('Sales Order'[Sales Order]),
    'Sales Order'[Channel] = "Internet"
)
```

### Sales per Customer and Customer AOV

`Sales per Customer` calculates average Internet sales revenue per customer. `Customer AOV` calculates average Internet revenue per order.

```dax
Sales per Customer =
DIVIDE(
    [Customer Sales],
    [Total Customers]
)

Customer AOV =
DIVIDE(
    [Customer Sales],
    [Customer Orders]
)
```

### Repeat Customer Rate

Calculates the percentage of Internet customers with at least two distinct orders in the current filter context.

```dax
Repeat Customer Rate =
VAR CustomerOrders =
    ADDCOLUMNS(
        CALCULATETABLE(
            VALUES(Sales[CustomerKey]),
            'Sales Order'[Channel] = "Internet",
            NOT ISBLANK(Sales[CustomerKey])
        ),
        "@Orders",
            CALCULATE(
                DISTINCTCOUNT('Sales Order'[Sales Order]),
                'Sales Order'[Channel] = "Internet"
            )
    )
VAR RepeatCustomers =
    COUNTROWS(
        FILTER(
            CustomerOrders,
            [@Orders] >= 2
        )
    )
RETURN
DIVIDE(
    RepeatCustomers,
    [Total Customers]
)
```

The denominator is the Internet-channel `Total Customers` measure. This reflects purchase frequency within the selected period, not necessarily lifetime customer retention.

### Customer purchase frequency

The **Customer Purchase Frequency** chart separates Internet customers by their number of distinct orders in the current filter context. The following two formulas were supplied during report development and were **not part of the earlier exported TMDL**; they should be reconfirmed against an updated Power BI model export.

### One-Time Customers

Counts Internet customers with exactly one distinct order in the active date and filter context.

```dax
One-Time Customers =
VAR CustomerOrders =
    ADDCOLUMNS(
        CALCULATETABLE(
            VALUES(Sales[CustomerKey]),
            'Sales Order'[Channel] = "Internet",
            NOT ISBLANK(Sales[CustomerKey])
        ),
        "@Orders",
            CALCULATE(
                DISTINCTCOUNT('Sales Order'[Sales Order]),
                'Sales Order'[Channel] = "Internet"
            )
    )
RETURN
    COUNTROWS(
        FILTER(CustomerOrders, [@Orders] = 1)
    )
```

### Repeat Customers

Counts Internet customers with two or more distinct orders in the selected context, consistent with the numerator logic of `Repeat Customer Rate`.

```dax
Repeat Customers =
VAR CustomerOrders =
    ADDCOLUMNS(
        CALCULATETABLE(
            VALUES(Sales[CustomerKey]),
            'Sales Order'[Channel] = "Internet",
            NOT ISBLANK(Sales[CustomerKey])
        ),
        "@Orders",
            CALCULATE(
                DISTINCTCOUNT('Sales Order'[Sales Order]),
                'Sales Order'[Channel] = "Internet"
            )
    )
RETURN
    COUNTROWS(
        FILTER(CustomerOrders, [@Orders] >= 2)
    )
```

### Validation

The measures should satisfy the following checks under the same filters:

```dax
Customer Count Check =
[One-Time Customers] + [Repeat Customers] - [Total Customers]

Repeat Rate Check =
DIVIDE([Repeat Customers], [Total Customers]) - [Repeat Customer Rate]
```

Both checks should normally return zero (allowing for floating-point precision in the rate). These are optional temporary validation measures, not required dashboard metrics. The chart uses the two counts as separate bar values and displays actual customer counts.

## Dynamic formatting

DAX measures also control how KPI comparisons are displayed, using directional arrows and formatted values to highlight changes in performance.

### Profit Margin Change

Calculates the difference between the current profit margin and its prior-year value.

```dax
Profit Margin Change =
[Profit Margin %] - [Profit Margin PY]
```

### Profit Margin Change Display

Displays the change in profit margin in percentage points, with directional arrows indicating increases and decreases.

```dax
Profit Margin Change Display =
VAR Change = [Profit Margin Change]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(Change), BLANK(),
    Change > 0, "▲ " & FORMAT(Change * 100, "0.0") & " pp",
    Change < 0, "▼ " & FORMAT(ABS(Change) * 100, "0.0") & " pp",
    "– 0.0 pp"
)
```

Unlike a relative year-over-year percentage, this margin difference is expressed in **percentage points (pp)**. The report uses comparable presentation logic for `Repeat Customer Rate Display`.

### Sales YoY Display

Formats year-over-year sales changes as text with directional arrows, highlighting increases and decreases.

```dax
Sales YoY Display =
VAR Change = [Sales YoY %]
RETURN
SWITCH(
    TRUE(),
    ISBLANK(Change), BLANK(),
    Change > 0, "▲ " & FORMAT(Change, "0.0%"),
    Change < 0, "▼ " & FORMAT(ABS(Change), "0.0%"),
    "– 0.0%"
)
```

These display measures return formatted **text** for presentation, while the underlying numeric measures remain available for calculations and visual analysis.
