> **Learning Log** – Aggregates, GROUP BY & HAVING  
> **Date:** 2026-02-05  
> **Source / Exercise:** SQLBolt / ChatGPT  
> **Summary:** Aggregates, grouping, filtering, and formatting for civic-tech data analysis

# Quick Reference

* [Expressions](#expressions "Transform or calculate values in your queries.")
* [Formatting & Human-Readable Output](#formatting--human-readable-output "Tips for readable query output.")
* [Aggregate Functions](#aggregate-functions "Summarize multiple rows into one value.")
* [GROUP BY & HAVING](#group-by--having "Grouping rows and filtering after aggregation.")
* [ORDER BY](#order-by "Control output row order.")
* [DISTINCT](#distinct "Get unique values or counts.")
* [Expressions in WHERE / HAVING](#expressions-in-where--having "Filter rows or groups using expressions.")
* [Combining Aggregates & Expressions](#combining-aggregates--expressions "Compute while aggregating.")
* [Civic-Tech Examples](#civic-tech-examples "Practical SQL examples using civic datasets.")
* [Quick Mental Map](#quick-mental-map "Stepwise summary of row vs group operations.")
* [Pro Tips](#pro-tips "Efficiency and readability tips for SQL queries.")

---



## Expressions

Expressions transform or calculate values in your queries.


| Type               | Example                                               | Description        |
| ------------------ | ----------------------------------------------------- | ------------- |
| Arithmetic         | `rating * 10 AS rating_percent`                       | Multiply rating by 10   |
| Concatenation      | `name, ", ", state, ", ", judge, ", ", verdict` | Combine columns into one |
| Modulo             | `year % 2 = 0`                                        | Filter rows where year is even |
| Conditional (CASE) | `CASE WHEN score > 90 THEN 'A' ELSE 'B' END AS grade` | Conditional output based on rules |

> **Tip:** Always alias your expressions (`AS`) for readable output.

---

## Formatting & Human-Readable Output

```sql
SELECT title, rating * 10 || '%' AS rating_percent
FROM movies;
```

* `||` concatenates strings (SQLite syntax)
* Adds `%` to numeric output for easier interpretation

---

## Aggregate Functions

Summarize multiple rows into one value.

| Function              | Description           | Example                                           |
| --------------------- | --------------------- | ------------------------------------------------- |
| `COUNT(*)`            | Count all rows        | `SELECT COUNT(*) FROM arrests;`                   |
| `COUNT(DISTINCT col)` | Count unique values   | `SELECT COUNT(DISTINCT officer_id) FROM arrests;` |
| `SUM(col)`            | Sum of numeric column | `SELECT SUM(fine_amount) FROM citations;`         |
| `AVG(col)`            | Average value         | `SELECT AVG(sentence_years) FROM convictions;`    |
| `MIN(col)`            | Minimum value         | `SELECT MIN(date_filed) FROM cases;`              |
| `MAX(col)`            | Maximum value         | `SELECT MAX(date_filed) FROM cases;`              |

> ⚠️ Nulls are ignored in aggregates automatically.

---

## GROUP BY & HAVING

`GROUP BY` → create **buckets** for aggregation
`HAVING` → filter buckets **after aggregation**

```sql
SELECT officer_id, COUNT(*) AS arrests_count
FROM arrests
GROUP BY officer_id
HAVING COUNT(*) > 5
ORDER BY arrests_count DESC;
```

**Stepwise process:**

1. `WHERE` → filters **rows** before aggregation
2. `GROUP BY` → forms buckets
3. Aggregate → summarize each bucket
4. `HAVING` → filters **buckets**

---

## ORDER BY

Control output row order:

```sql
SELECT department, AVG(sentence_years) AS avg_sentence
FROM convictions
GROUP BY department
HAVING AVG(sentence_years) > 2
ORDER BY avg_sentence DESC;
```

* `ASC` → ascending (default)
* `DESC` → descending

---

## DISTINCT

Return **unique values** or **count unique items**:

```sql
SELECT DISTINCT department FROM arrests;
SELECT COUNT(DISTINCT officer_id) FROM arrests;
```

> Useful to prevent overcounting or eliminate duplicates.

---

## Expressions in WHERE / HAVING

Expressions can filter rows or groups:

```sql
-- Even years
SELECT title, year
FROM movies
WHERE year % 2 = 0;

-- High-average sentences
SELECT department, AVG(sentence_years) AS avg_sentence
FROM convictions
GROUP BY department
HAVING AVG(sentence_years) > 2;
```

* `%` (modulo) checks divisibility
* Expressions can be numeric, string, or conditional

---

## Combining Aggregates & Expressions

```sql
SELECT department, SUM(fine_amount) * 1.1 AS adjusted_fines
FROM citations
GROUP BY department
HAVING SUM(fine_amount) > 1000
ORDER BY adjusted_fines DESC;
```

* Aggregate functions can participate in calculations (`* 1.1`)
* Alias results for clarity (`AS adjusted_fines`)

---

## Civic-Tech Examples

1. **Officers with >10 arrests, sorted by total arrests**

```sql
SELECT officer_id, COUNT(*) AS total_arrests
FROM arrests
GROUP BY officer_id
HAVING COUNT(*) > 10
ORDER BY total_arrests DESC;
```

2. **Movies released in even years, scaled rating**

```sql
SELECT title, year, rating * 10 || '%' AS rating_percent
FROM movies
WHERE year % 2 = 0;
```

3. **Average fines per violation type over $100**

```sql
SELECT violation_type, '$' || ROUND(AVG(fine_amount),2) AS avg_fine
FROM citations
GROUP BY violation_type
HAVING AVG(fine_amount) > 100
ORDER BY avg_fine DESC;
```

---

## Quick Mental Map

| Step        | Action                                  |
| ----------- | --------------------------------------- |
| WHERE       | Filter rows before aggregation          |
| GROUP BY    | Bucket rows for aggregation             |
| Aggregates  | COUNT, SUM, AVG, MIN, MAX               |
| HAVING      | Filter aggregated buckets               |
| ORDER BY    | Sort results                            |
| DISTINCT    | Remove duplicates                       |
| Expressions | Transform, calculate, or format results |

---

## Pro Tips

* Alias expressions for **clarity** (`AS`)
* `%` (modulo) helps identify even/odd, cycles, or patterns
* WHERE = **row-level filter**, HAVING = **group-level filter**
* Combine aggregates and expressions for **report-ready results**
* Format numeric or string outputs for human-friendly tables (%, $, rounding, concatenation)
