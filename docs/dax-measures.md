# DAX Measures & Calculation Logic

This document highlights selected DAX measures used across the Sales Performance Dashboard's three report pages: **Sales Dashboard**, **Products**, and **Customers**. The examples cover sales and profitability KPIs, time intelligence, product performance, Internet customer purchasing behavior, and dynamic KPI formatting.

## Core sales and profitability

### Total Sales

```dax
Total Sales = SUM(Sales[Sales Amount])
```

Sums sales amounts in the current report filter context.

### Total Cost

```dax
Total Cost = SUM(Sales[Total Product Cost])
```

### Profit and Profit Margin %

```dax
Profit = [Total Sales] - [Total Cost]

Profit Margin % =
DIVIDE(
    [Profit],
    [Total Sales]
)
```

Profit represents sales less product cost, rather than a complete income-statement net profit. `DIVIDE` returns blank when the denominator is zero or blank unless an alternate result is specified.

## Orders and product performance

### Total Orders and Average Order Value

```dax
Total Orders = DISTINCTCOUNT('Sales Order'[Sales Order])

Average Order Value =
DIVIDE(
    [Total Sales],
    [Total Orders]
)
```

Order counts use distinct `Sales Order` identifiers.

### Quantity Sold and Average Selling Price

```dax
Quantity Sold = SUM(Sales[Order Quantity])

Average Selling Price =
DIVIDE(
    [Total Sales],
    [Quantity Sold]
)
```

`Average Selling Price` is a sales-weighted amount per unit, not an unweighted average of product list prices.

## Prior-year and year-over-year comparisons

### Sales LY and Sales YoY %

```dax
Sales LY =
CALCULATE(
    [Total Sales],
    SAMEPERIODLASTYEAR('Date'[Date])
)

Sales YoY % =
DIVIDE(
    [Total Sales] - [Sales LY],
    [Sales LY]
)
```

`SAMEPERIODLASTYEAR` shifts the current date context by one year. The export also includes a `Sales PY` measure with the same expression as `Sales LY`.

### Orders PY and Orders YoY %

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

Other prior-year and YoY measures follow this pattern for profit, average order value, quantity sold, average selling price, customer counts, and sales per customer.

## Customer analysis: Internet channel

The exported customer measures intentionally apply the **Internet** channel filter. This is important when interpreting customer KPIs alongside overall sales figures.

### Total Customers

```dax
Total Customers =
CALCULATE(
    DISTINCTCOUNT(Sales[CustomerKey]),
    'Sales Order'[Channel] = "Internet",
    NOT ISBLANK(Sales[CustomerKey])
)
```

Counts distinct, nonblank customer keys within Internet-channel sales.

### Customer Sales and Customer Orders

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

This measure considers an Internet-channel customer **repeat** if they have at least **two distinct sales orders** in the current filter context. The denominator is the Internet-channel `Total Customers` measure. It is not necessarily a lifetime retention metric.

## Customer purchase frequency

The revised Customers page adds a **Customer Purchase Frequency** chart that separates Internet customers by their number of distinct orders in the current filter context. These two formulas reflect the measures supplied during report development; unlike the preceding measures, they were **not part of the earlier exported TMDL** and should be reconfirmed against the updated Power BI model after saving.

### One-Time Customers

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

Counts Internet customers with **exactly one** distinct order in the active date and filter context.

### Repeat Customers

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

Counts Internet customers with **two or more** distinct orders in the selected context, matching the numerator logic of `Repeat Customer Rate`.

### Validation

The measures should satisfy the following checks under the same filters:

```dax
Customer Count Check =
[One-Time Customers] + [Repeat Customers] - [Total Customers]

Repeat Rate Check =
DIVIDE([Repeat Customers], [Total Customers]) - [Repeat Customer Rate]
```

Both checks should normally return zero (allowing for floating-point precision in the rate). These are optional temporary validation measures, not required dashboard metrics. The chart uses the two counts as separate bar values and displays actual customer counts.

## Percentage-point changes and display measures

### Profit Margin Change

```dax
Profit Margin Change =
[Profit Margin %] - [Profit Margin PY]
```

### Profit Margin Change Display

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

Unlike a relative YoY percentage, this margin difference is displayed in **percentage points (pp)**. The report uses comparable presentation logic for `Repeat Customer Rate Display`.

### Sales YoY Display

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

This measure returns formatted **text** with directional arrows, useful for cards but not interchangeable with the underlying numeric `Sales YoY %` measure.

## Implementation notes

- The TMDL uses **US-dollar format strings** for monetary measures.
- The year-over-year calculations use `'Date'[Date]`, so the relationship and calendar context matter.
- `Profit` is based only on `Total Sales` and `Total Cost`.
- `Total Customers`, `Customer Sales`, `Customer Orders`, and `Repeat Customer Rate` apply the Internet-channel condition.
- Both `Sales LY` and `Sales PY` are present with equivalent DAX in the provided export.
- The exported TMDL also defines prior-year, YoY, and display measures for AOV, selling price, quantity, profit, and customer metrics.

**Sources:** The earlier `_Measures` TMDL export and the two subsequently supplied customer purchase-frequency formulas. Reconfirm the latter in an updated export after saving the report. Relationship settings and Power Query transformations are outside the scope of the measures export.
