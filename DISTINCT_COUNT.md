# Count Distinct Values

Use the `COUNT(DISTINCT column_name)` function to count the number of unique values in a column.

## Query

```sql
SELECT COUNT(DISTINCT month) AS unique_months
FROM tutorial.aapl_historical_stock_price;
```

## Explanation

- `COUNT(DISTINCT month)` counts only unique month values.
- `AS unique_months` assigns a readable alias to the result column.
- The query returns the total number of distinct months present in the `month` column of the `aapl_historical_stock_price` table.

## Example Output

| unique_months |
|---------------|
| 12            |

> The actual result depends on the data available in the table.