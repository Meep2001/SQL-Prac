# What Are JOINS?

**JOINS** let you combine rows from two (or more) tables based on a related column.

Think of them as **"connecting the dots"** between datasets.

---

## 📊 Example Tables

### Students

```sql
student_id | name
-----------|-------
1          | Alice
2          | Bob
3          | Charlie
```

### Courses

```sql
course_id | student_id | course_name
----------|------------|------------
101       | 1          | Math
102       | 2          | Science
103       | 1          | History
104       | NULL       | Art
```

---

## 🔗 Types of JOINS

| JOIN Type | What It Returns | Example Result |
|------------|----------------|----------------|
| **INNER JOIN** | Only rows with matching values in both tables | Alice → Math, Alice → History, Bob → Science |
| **LEFT JOIN** | All rows from the left table + matching rows from the right table (NULL if no match) | Adds Charlie → NULL |
| **RIGHT JOIN** | All rows from the right table + matching rows from the left table (NULL if no match) | Adds Art → NULL |
| **FULL OUTER JOIN** | All rows from both tables, matched or not (NULL where no match exists) | Shows Charlie → NULL and Art → NULL |
| **CROSS JOIN** | Cartesian product: every row from table A combined with every row from table B | 3 × 4 = 12 rows |

---

## 🧑‍💻 Example Queries

### INNER JOIN

```sql
SELECT s.name, c.course_name
FROM Students s
INNER JOIN Courses c
ON s.student_id = c.student_id;
```

**Output**

| name | course_name |
|------|------------|
| Alice | Math |
| Alice | History |
| Bob | Science |

---

### LEFT JOIN

```sql
SELECT s.name, c.course_name
FROM Students s
LEFT JOIN Courses c
ON s.student_id = c.student_id;
```

**Output**

| name | course_name |
|------|------------|
| Alice | Math |
| Alice | History |
| Bob | Science |
| Charlie | NULL |

---

### FULL OUTER JOIN

```sql
SELECT s.name, c.course_name
FROM Students s
FULL OUTER JOIN Courses c
ON s.student_id = c.student_id;
```

**Output**

| name | course_name |
|------|------------|
| Alice | Math |
| Alice | History |
| Bob | Science |
| Charlie | NULL |
| NULL | Art |

---

### CROSS JOIN

```sql
SELECT s.name, c.course_name
FROM Students s
CROSS JOIN Courses c;
```

**Output**

Every student is paired with every course:

| name | course_name |
|------|------------|
| Alice | Math |
| Alice | Science |
| Alice | History |
| Alice | Art |
| Bob | Math |
| Bob | Science |
| Bob | History |
| Bob | Art |
| Charlie | Math |
| Charlie | Science |
| Charlie | History |
| Charlie | Art |

---

## 🎯 Quick Summary

- **INNER JOIN** → Matching rows only
- **LEFT JOIN** → All rows from the left table + matches
- **RIGHT JOIN** → All rows from the right table + matches
- **FULL OUTER JOIN** → All rows from both tables
- **CROSS JOIN** → Every possible combination of rows

> 💡 **Tip:** The most commonly used joins in real-world SQL are **INNER JOIN** and **LEFT JOIN**.

# SQL Joins with Comparison Operators: Date Ranges & More

## Key Idea
- Normally, JOINS match rows **exactly** (e.g., `ON tableA.id = tableB.id`).
- But you can use **comparison operators** (`>`, `<`, `>=`, `<=`, `BETWEEN`) inside the `ON` clause.
- This allows JOINS based on **conditions** like date ranges or numeric thresholds.

---

## Example: Investments After 5 Years
```sql
SELECT companies.permalink,
       companies.name,
       companies.status,
       COUNT(investments.investor_permalink) AS investors
FROM tutorial.crunchbase_companies companies
LEFT JOIN tutorial.crunchbase_investments_part1 investments
  ON companies.permalink = investments.company_permalink
 AND investments.funded_year > companies.founded_year + 5
GROUP BY 1,2,3;
