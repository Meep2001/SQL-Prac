# SQL Performance Tuning -- Revision Notes

## What is SQL Performance Tuning?

Improve SQL queries to: - Return results faster - Use fewer CPU/Memory
resources - Reduce database workload

------------------------------------------------------------------------

# Factors Affecting Query Performance

### 1. Large Tables

More rows = more work.

``` sql
SELECT * FROM orders;
```

Better:

``` sql
SELECT *
FROM orders
WHERE order_date >= '2025-01-01'
  AND order_date < '2025-02-01';
```

------------------------------------------------------------------------

### 2. Joins

Joins compare rows between tables.

``` sql
SELECT *
FROM customers c
JOIN orders o
ON c.customer_id=o.customer_id;
```

Large tables = expensive joins.

------------------------------------------------------------------------

### 3. Aggregations

Aggregations require extra computation.

``` sql
SELECT department,
       COUNT(*)
FROM employees
GROUP BY department;
```

------------------------------------------------------------------------

# Reduce Table Size Early

Filter as early as possible.

``` sql
SELECT *
FROM events
WHERE event_date BETWEEN '2025-01-01' AND '2025-01-31';
```

Good for testing:

``` sql
SELECT *
FROM events
LIMIT 100;
```

------------------------------------------------------------------------

# LIMIT with Aggregates

❌ Doesn't help:

``` sql
SELECT COUNT(*)
FROM events
LIMIT 100;
```

Reason: 1. COUNT scans table. 2. LIMIT applied afterwards.

Better for testing only:

``` sql
SELECT COUNT(*)
FROM(
    SELECT *
    FROM events
    LIMIT 100
) sub;
```

⚠️ Produces incorrect totals---use only to test query logic.

------------------------------------------------------------------------

# Reduce Before JOIN

Slow:

``` sql
SELECT t.conference,
       p.school_name,
       COUNT(*)
FROM players p
JOIN teams t
ON p.school_name=t.school_name
GROUP BY t.conference,p.school_name;
```

Better:

``` sql
SELECT t.conference,
       sub.*
FROM(
    SELECT school_name,
           COUNT(*) AS players
    FROM players
    GROUP BY school_name
) sub
JOIN teams t
ON sub.school_name=t.school_name;
```

Why? - Aggregate first - Fewer rows enter the join - Less computation

------------------------------------------------------------------------

# EXPLAIN

Shows execution plan.

``` sql
EXPLAIN
SELECT *
FROM events
WHERE event_date>='2025-01-01'
LIMIT 100;
```

Use EXPLAIN to: - Find expensive operations - Compare query versions -
Optimize based on cost

Higher cost = slower query.

------------------------------------------------------------------------

# Performance Tips

✅ Filter early with WHERE

✅ Reduce rows before JOIN

✅ Aggregate before JOIN when possible

✅ LIMIT for testing (not final results)

✅ Use EXPLAIN before and after optimization

------------------------------------------------------------------------

# Good vs Bad

Bad:

``` sql
SELECT COUNT(*)
FROM huge_table;
```

Better for testing:

``` sql
SELECT COUNT(*)
FROM(
SELECT *
FROM huge_table
LIMIT 100
) t;
```

Bad:

``` sql
JOIN large_table1
JOIN large_table2
```

Better:

``` sql
Aggregate
→ Filter
→ Join
```

------------------------------------------------------------------------

# Cheat Sheet

  Technique               Benefit
  ----------------------- ------------------------
  WHERE                   Reduce rows early
  LIMIT                   Fast testing
  Subquery + LIMIT        Limit before aggregate
  Aggregate before JOIN   Smaller joins
  EXPLAIN                 View query plan
  Filter before JOIN      Less computation

## Key Takeaways

-   Performance = fewer rows + fewer calculations.
-   Apply filters as early as possible.
-   LIMIT is for development/testing.
-   Reduce datasets before joins.
-   Use EXPLAIN to inspect execution plans.
