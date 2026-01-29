> LearningSQL-Day12-Civic-Tech-Cheatsheet  
> Merging our Expressions + Formatting + Modulo Logic material with the Aggregates + GROUP BY + ORDER BY + HAVING + DISTINCT material into one compact, professional **cheat sheet** specific to Civic-Tech

## 1️⃣ Expressions

Expressions transform or calculate values in your queries.

| Type               | Example                                               | Description                                  |     |   |                         |                          |
| ------------------ | ----------------------------------------------------- | -------------------------------------------- | --- | - | ----------------------- | ------------------------ |
| Arithmetic         | `rating * 10 AS rating_percent`                       | Multiply rating by 10                        |     |   |                         |                          |
| Concatenation      | `first_name                                           |                                              | ' ' |   | last_name AS full_name` | Combine columns into one |
| Modulo             | `year % 2 = 0`                                        | Filter rows where year is even               |     |   |                         |                          |
| Conditional (CASE) | `CASE WHEN score > 90 THEN 'A' ELSE 'B' END AS grade` | Return different outputs based on conditions |     |   |                         |                          |

> **Tip:** Alias your expressions (`AS`) for readable output.

---

### Adding formatting (human-friendly)

```sql
SELECT title, rating * 10 || '%' AS rating_percent
FROM movies;
```

* `||` concatenates strings (SQLite syntax)
* Converts numeric output into more interpretable forms (like adding `%`)

---

## 2️⃣ Aggregate Functions

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

## 3️⃣ GROUP BY & HAVING

`GROUP BY` creates **buckets** for aggregation.
`HAVING` filters those buckets **after aggregation**.

```sql
SELECT officer_id, COUNT(*) AS arrests_count
FROM arrests
GROUP BY officer_id
HAVING COUNT(*) > 5
ORDER BY arrests_count DESC;
```

**Rule of thumb:**

1. `WHERE` → filters **rows** before aggregation
2. `GROUP BY` → forms buckets
3. Aggregate → summarize each bucket
4. `HAVING` → filters **buckets** after aggregation

---

## 4️⃣ ORDER BY

Control row output order:

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

## 5️⃣ DISTINCT

Return **unique values** or **count unique items**:

```sql
SELECT DISTINCT department FROM arrests;
SELECT COUNT(DISTINCT officer_id) FROM arrests;
```

> Use DISTINCT to prevent overcounting or to eliminate duplicates.

---

## 6️⃣ Expressions in WHERE / HAVING

Expressions can also **filter rows or groups**:

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
* Expressions can be **numeric, string, or conditional**
* Combine with **WHERE / HAVING / ORDER BY** to refine results

---

## 7️⃣ Combining Aggregates & Expressions

You can transform or calculate while aggregating:

```sql
SELECT department, SUM(fine_amount) * 1.1 AS adjusted_fines
FROM citations
GROUP BY department
HAVING SUM(fine_amount) > 1000
ORDER BY adjusted_fines DESC;
```

* Aggregates (`SUM`) can be part of expressions (`* 1.1`)
* Alias results for clarity (`AS adjusted_fines`)

---

## 8️⃣ Quick Civic-Tech Examples

1. **Officers with more than 10 arrests, sorted by total arrests**

```sql
SELECT officer_id, COUNT(*) AS total_arrests
FROM arrests
GROUP BY officer_id
HAVING COUNT(*) > 10
ORDER BY total_arrests DESC;
```

2. **Movies released on even years with rating scaled to percentage**

```sql
SELECT title, year, rating * 10 || '%' AS rating_percent
FROM movies
WHERE year % 2 = 0;
```

3. **Average fines per violation type over $100, formatted as currency**

```sql
SELECT violation_type, '$' || ROUND(AVG(fine_amount),2) AS avg_fine
FROM citations
GROUP BY violation_type
HAVING AVG(fine_amount) > 100
ORDER BY avg_fine DESC;
```

---

## 9️⃣ Quick Mental Map

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

## 🔟 Pro Tips

* **Always alias expressions** (`AS`) → clean output and readability
* **Modulo (%)** is great for identifying even/odd, cycles, or periodic data
* **HAVING** is for **groups**, WHERE is for **rows**
* Combine expressions and aggregates to produce **report-ready results**
* Format numeric or string outputs for human-friendly tables (%, $, rounding, concatenation)
