# Power BI Analysis: E-Commerce Sales Dashboard

This project documents the creation of a Power BI dashboard for e-commerce sales analysis, using a star schema, DAX measures, and RFM segmentation.

## Data Preparation

Imported six CSV files:

- `orders.csv`
- `products.csv`
- `customers.csv`
- `order_details.csv`
- `categories.csv`
- `rfm_segments.csv`

## Building the Model: Star Schema

Structured the data in Power BI using a star schema with the following relationships:

- **Order_Details** to **Orders** via `order_id` (many-to-one).
- **Order_Details** to **Products** via `product_id` (many-to-one).
- **Orders** to **Customers** via `customer_id` (many-to-one).
- **Products** to **Categories** via `category_id` (many-to-one).
- **Customers** to **RFM_Segments** via `customer_id` (one-to-one).
- **Orders** to **Date** via `order_date` (many-to-one).

### Date Table

Created a Date table for time-based analysis:

```dax
Date = CALENDAR(DATE(2010, 1, 1), DATE(2011, 12, 31))
```

### Optimization

Hid unused columns (e.g., `StockCode` in `products`) to streamline the model.

## DAX Measures

Defined key KPIs using DAX:

### Total Revenue

```dax
Total Revenue = SUMX(Order_Details, Order_Details[quantity] * Order_Details[unit_price])
```

### Customer Retention Rate

```dax
Customer Retention Rate = 
DIVIDE(
    CALCULATE(COUNTROWS(Orders), Orders[customer_id] IN VALUES(Orders[customer_id])),
    COUNTROWS(DISTINCT(Customers[customer_id])),
    0
)
```

### Average Order Value

```dax
Average Order Value = DIVIDE([Total Revenue], COUNTROWS(Orders), 0)
```

### Year-over-Year Revenue Growth

```dax
YoY Revenue Growth = 
VAR CurrentYear = [Total Revenue]
VAR PreviousYear = CALCULATE([Total Revenue], PREVIOUSYEAR('Date'[Date]))
RETURN DIVIDE(CurrentYear - PreviousYear, PreviousYear, 0)
```

## Dashboard Visualizations

Designed a Power BI dashboard with:

- **Cards**: Total Revenue, Customer Retention Rate, Average Order Value.
- **Line Chart**: Total Revenue over time by RFM segment.
- **Bar Chart**: Total Revenue by category.
- **Map**: Total Revenue by country.
- **Slicers**: Filter by category, RFM segment, and year.

### Stakeholder-Specific Visuals

#### Top 5 Products by Quantity

Measure:

```dax
Total Quantity = CALCULATE(SUM(order_details[quantity]), order_details[quantity] > 0)
```

Displayed in a bar chart, filtered to top 5 products.

#### Top 5 Customers by Spend

Measure:

```dax
Total Spend = CALCULATE(SUMX(order_details, order_details[quantity] * order_details[unit_price]), order_details[quantity] > 0)
```

Displayed in a bar chart, filtered to top 5 customers.

## RFM Segmentation

Segmented customers by Recency, Frequency, and Monetary value:

- **High Value**: Low recency, high frequency, high monetary (Segment 0).
- **Medium Value**: Moderate metrics (Segment 1).
- **Low Value**: High recency, low frequency, low monetary (Segment 2).

### RFM Visuals

- **Line Chart**: Total Revenue by RFM segment over time.
- **Table**: Total Revenue by RFM segment.
- **Bar Chart**: Customer distribution by RFM segment.
- **Slicer**: Filter by RFM segment.