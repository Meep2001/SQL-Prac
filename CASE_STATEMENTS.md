# Case Statements in SQL

## Basic CASE Statement

```sql
SELECT
    player_name,   -- comma important after the column list
    state,
    CASE
        WHEN state = 'CA' THEN 'YES'
        ELSE 'NO'
    END AS is_from_california
FROM benn_college_football_players
ORDER BY is_from_california DESC;
```

---

## Multiple Conditions in CASE

```sql
SELECT
    player_name,
    CASE
        WHEN height > 57 THEN 'above avg height'
        WHEN height < 57 THEN 'less than avg height'
        WHEN height = 57 THEN 'avg height'
        ELSE 'NA'
    END AS height_stats
FROM benn_college_football_players;
```

---

## CASE with AND Condition

```sql
SELECT
    player_name,
    CASE
        WHEN year = 'FR' AND position = 'WR' THEN 'frosh_wr'
        ELSE NULL
    END AS sample_case_statement
FROM benn.college_football_players;
```

---

## CASE with OR Condition

```sql
SELECT
    *,
    CASE
        WHEN year = 'SR' OR year = 'JR'
            THEN UPPER(player_name)
        ELSE NULL
    END AS player_name_upper_case
FROM benn_college_football_players
ORDER BY player_name_upper_case;
```

---

# Counting Rows Satisfying Multiple Conditions

## Example 1

```sql
SELECT
    CASE
        WHEN year = 'FR' THEN 'FR'
        WHEN year = 'SO' THEN 'SO'
        WHEN year = 'JR' THEN 'JR'
        WHEN year = 'SR' THEN 'SR'
        ELSE 'No Year Data'
    END AS year_group,
    COUNT(1) AS count
FROM benn.college_football_players
GROUP BY year_group;
```

---

## Example 2

```sql
SELECT
    CASE
        WHEN state IN ('CA', 'OR', 'WA') THEN 'West Coast'
        WHEN state = 'TX' THEN 'Texas'
        ELSE 'Other'
    END AS arbitrary_regional_designation,
    COUNT(1) AS players
FROM benn.college_football_players
WHERE weight >= 300
GROUP BY 1;
```

---

# Pivoting a Table Using CASE

## Vertical Output

```sql
SELECT
    CASE
        WHEN year = 'FR' THEN 'FR'
        WHEN year = 'SO' THEN 'SO'
        WHEN year = 'JR' THEN 'JR'
        WHEN year = 'SR' THEN 'SR'
        ELSE 'No Year Data'
    END AS year_group,
    COUNT(1) AS count
FROM benn.college_football_players
GROUP BY 1;
```

### Sample Output

| year_group | count |
|------------|-------|
| FR | 120 |
| SO | 105 |
| JR | 98 |
| SR | 110 |

---

## Horizontal Output (Pivot Style)

```sql
SELECT
    COUNT(CASE WHEN year = 'FR' THEN 1 ELSE NULL END) AS fr_count,
    COUNT(CASE WHEN year = 'SO' THEN 1 ELSE NULL END) AS so_count,
    COUNT(CASE WHEN year = 'JR' THEN 1 ELSE NULL END) AS jr_count,
    COUNT(CASE WHEN year = 'SR' THEN 1 ELSE NULL END) AS sr_count
FROM benn.college_football_players;
```

### Sample Output

| fr_count | so_count | jr_count | sr_count |
|-----------|-----------|-----------|-----------|
| 120 | 105 | 98 | 110 |

---

# Notes

- `ELSE` is completely optional in a `CASE` statement.
- `GROUP BY 1` refers to the first column that appears after the `SELECT` keyword.
- `COUNT(1)` and `COUNT(*)` produce the same result.
- `CASE` statements are evaluated from top to bottom. The first matching condition is returned.
- You generally cannot place aggregate functions such as `COUNT()`, `SUM()`, or `AVG()` directly inside a `CASE` condition. Instead, apply the aggregate outside the `CASE` expression.
- `COUNT(CASE WHEN condition THEN 1 END)` is a common pattern for conditional aggregation and pivoting.