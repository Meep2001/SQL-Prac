# SQL Window Functions Complete Revision Notes

See sections:

-   What are Window Functions
-   OVER, PARTITION BY, ORDER BY
-   SUM/COUNT/AVG OVER
-   ROW_NUMBER, RANK, DENSE_RANK
-   NTILE (Quartiles, Quintiles, Percentiles)
-   LAG & LEAD
-   WINDOW aliases
-   Top N per group using subqueries

## Top N Example

``` sql
SELECT * FROM(
SELECT dept,employee,salary,
RANK() OVER(PARTITION BY dept ORDER BY salary DESC) rank
FROM employees
) sub
WHERE sub.rank<=3;
```

## NTILE Example

``` sql
SELECT salary,
NTILE(4) OVER(ORDER BY salary) quartile
FROM employees;
```

## LAG Example

``` sql
SELECT salary,
salary-LAG(salary) OVER(ORDER BY salary) diff
FROM employees;
```
