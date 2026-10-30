# Selected DAX measures and calculation logic

## Source and scope

The formulas below are transcribed from the project's exported **`_Measures` TMDL**. They describe the actual semantic-model definitions supplied for this project—not generic examples. Only selected measures are shown to keep this portfolio documentation focused.

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

**Source:** `_Measures` TMDL exported from the Sales Performance Dashboard semantic model. The relationship settings and underlying Power Query transformations are outside the scope of this measure export.
