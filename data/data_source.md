# Data Source

## Dataset
**Global Electronics Retailer** — Maven Analytics

A synthetic dataset simulating a global electronics retail chain, covering sales transactions,
store locations, product catalog, customer records, and currency exchange rates.

## Source
[Maven Analytics Data Playground](https://mavenanalytics.io/data-playground/global-electronics-retailer)

*Raw data files are not included in this repository. Download directly from the source above.*

## Tables Used in This Project

| Table | Rows | Used For |
|---|---|---|
| `Stores` | 67 (66 physical + 1 online) | Store dimension — Country, Square Meters, Open Date |
| `Sales` | ~62,884 | Fact table — Quantity, StoreKey, ProductKey |
| `Products` | ~2,500 | Product dimension — Unit Price USD |

**Not used in this project:** `Customers`, `Exchange_Rates` (see note below).

## Key Data Notes

- **StoreKey 0 = Online store.** It's the only store with a populated `Delivery Date` in `Sales`
  and has a blank `Square Meters` value in `Stores` — confirming it's not a physical location.
  Excluded from this analysis, which is scoped to physical store footprint only.

- **Exchange_Rates table intentionally not used.** `Products.Unit_Price_USD` is already a fixed
  catalog price in USD — `Sales` has no separate local-currency price field. No currency
  conversion step is needed for revenue calculations in this project.

- **9 of 66 physical stores have zero recorded sales** in the dataset's full time range
  (1/1/2016 – 9/9/2020). This is a genuine data pattern, not a data quality issue — confirmed
  via anti-join checks (no orphaned StoreKeys between `Sales` and `Stores`).

## Views Created for This Project

```sql
CREATE VIEW vw_Stores AS
SELECT StoreKey, Country, Square_Meters, Open_Date
FROM Stores WHERE StoreKey <> 0;

CREATE VIEW vw_Sales AS
SELECT StoreKey, ProductKey, Quantity
FROM Sales WHERE StoreKey <> 0;

CREATE VIEW vw_Products AS
SELECT ProductKey, Unit_Price_USD
FROM Products;
```

These three lean views were imported into Power BI (Import mode, not DirectQuery) and form the
star schema for the dashboard.
