# SQL Window Functions - Summary Revision Notes

## 1. Window Functions

-   Perform calculations across related rows **without collapsing
    rows**.
-   Unlike `GROUP BY`, every original row remains in the output.
-   Syntax:

``` sql
FUNCTION(column) OVER(...)
```

## 2. OVER()

Turns an aggregate into a window function.

``` sql
SELECT salary,
       SUM(salary) OVER() AS total_salary
FROM employees;
```

Returns the grand total on every row.

## 3. ORDER BY in OVER()

Defines the order of calculation.

``` sql
SUM(duration_seconds)
OVER(ORDER BY start_time)
```

Creates a running total.

Without `ORDER BY`, every row gets the same partition total.

## 4. PARTITION BY

Splits data into independent groups.

``` sql
SUM(duration_seconds)
OVER(PARTITION BY start_terminal)
```

With `ORDER BY`, the running total restarts for each partition.

## 5. Aggregate Window Functions

``` sql
SUM() OVER(...)
COUNT() OVER(...)
AVG() OVER(...)
```

Example:

``` sql
SELECT dept,
       salary,
       SUM(salary) OVER(PARTITION BY dept) AS total_salary,
       COUNT(*) OVER(PARTITION BY dept) AS employee_count,
       AVG(salary) OVER(PARTITION BY dept) AS avg_salary
FROM employees;
```

## 6. ROW_NUMBER()

Assigns a unique sequential number.

``` sql
ROW_NUMBER() OVER(
PARTITION BY dept
ORDER BY salary DESC)
```

Even identical values receive different row numbers.

## 7. RANK()

Rows with equal values share the same rank.

Example:

    Salary   Rank
  -------- ------
      9000      1
      8000      2
      8000      2
      7000      4

Ranks are skipped after ties.

## 8. DENSE_RANK()

Same as RANK, but no skipped numbers.

    Salary   Dense Rank
  -------- ------------
      9000            1
      8000            2
      8000            2
      7000            3

## 9. Filtering Window Functions

Window functions cannot be filtered directly in `WHERE`.

Use a subquery:

``` sql
SELECT *
FROM (
    SELECT employee,
           RANK() OVER(
               PARTITION BY dept
               ORDER BY salary DESC
           ) AS rank
    FROM employees
) sub
WHERE sub.rank <= 5;
```

The inner query calculates `rank`; the outer query filters it.

## 10. NTILE()

Divides ordered rows into nearly equal buckets.

``` sql
NTILE(4)   -- Quartiles
NTILE(5)   -- Quintiles
NTILE(100) -- Percentiles
```

Example:

``` sql
SELECT salary,
       NTILE(4) OVER(ORDER BY salary) AS quartile
FROM employees;
```

Works on row positions, not values.

## 11. LAG()

Returns a value from a previous row.

``` sql
LAG(salary,1)
OVER(ORDER BY salary)
```

Difference example:

``` sql
salary - LAG(salary)
OVER(ORDER BY salary)
```

## 12. LEAD()

Returns a value from the following row.

``` sql
LEAD(salary,1)
OVER(ORDER BY salary)
```

Useful for comparing adjacent rows.

## 13. WINDOW Alias

Avoid repeating the same window definition.

``` sql
SELECT salary,
       RANK() OVER w,
       NTILE(4) OVER w
FROM employees
WINDOW w AS (
    PARTITION BY dept
    ORDER BY salary
);
```

## Key Takeaways

-   `OVER()` creates a window function.
-   `PARTITION BY` splits rows into groups.
-   `ORDER BY` defines calculation order.
-   `ROW_NUMBER` → unique numbering.
-   `RANK` → ties with gaps.
-   `DENSE_RANK` → ties without gaps.
-   `NTILE` → divide rows into buckets.
-   `LAG` → previous row.
-   `LEAD` → next row.
-   Use a subquery or CTE when filtering on window function results.
