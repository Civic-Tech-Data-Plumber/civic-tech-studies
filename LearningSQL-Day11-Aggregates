> Learning log day 11  
> Aggregates are a pretty complex topic, so I combined a couple of lessons onto this one page for simplicity.

# SQL Aggregates, GROUP BY, ORDER BY, HAVING, DISTINCT — Cheat Sheet

> **Goal:** Summarize aggregation, grouping, ordering, and filtering in one place.

---

## 1️⃣ Aggregate Functions

Aggregate functions take multiple rows and return **one summarized value**.
They are essential for reporting, civic data analysis, and large datasets.

| Function              | Description                   | Example                                           |
| --------------------- | ----------------------------- | ------------------------------------------------- |
| `COUNT(*)`            | Count of rows                 | `SELECT COUNT(*) FROM arrests;`                   |
| `COUNT(DISTINCT col)` | Count of **unique** values    | `SELECT COUNT(DISTINCT officer_id) FROM arrests;` |
| `SUM(col)`            | Total sum of a numeric column | `SELECT SUM(fine_amount) FROM citations;`         |
| `AVG(col)`            | Average value                 | `SELECT AVG(sentence_years) FROM convictions;`    |
| `MIN(col)`            | Minimum value                 | `SELECT MIN(date_filed) FROM cases;`              |
| `MAX(col)`            | Maximum value                 | `SELECT MAX(date_filed) FROM cases;`              |

> **Tip:** Always consider NULLs — most aggregates ignore NULLs automatically.

---

## 2️⃣ GROUP BY

`GROUP BY` allows **aggregation per category**.

```sql
SELECT officer_id, COUNT(*) AS arrests_count
FROM arrests
GROUP BY officer_id;
```

* Groups rows by `officer_id`
* Returns **one row per officer**
* `COUNT(*)` shows arrests per officer

### Common patterns

| Goal                     | Example                     |
| ------------------------ | --------------------------- |
| Group by category        | `GROUP BY department`       |
| Group by multiple fields | `GROUP BY department, year` |

> ✅ Always include **aggregate functions** in SELECT when using GROUP BY.

---

## 3️⃣ HAVING

* `WHERE` filters **before aggregation**
* `HAVING` filters **after aggregation**

```sql
SELECT officer_id, COUNT(*) AS arrests_count
FROM arrests
GROUP BY officer_id
HAVING COUNT(*) > 10;
```

* Keeps only officers with **more than 10 arrests**
* Think: WHERE = row-level, HAVING = group-level

---

## 4️⃣ ORDER BY

Controls the order of returned rows.

```sql
SELECT officer_id, COUNT(*) AS arrests_count
FROM arrests
GROUP BY officer_id
ORDER BY arrests_count DESC;
```

* `ASC` → ascending (default)
* `DESC` → descending

### Combined example

```sql
SELECT department, AVG(sentence_years) AS avg_sentence
FROM convictions
GROUP BY department
HAVING AVG(sentence_years) > 2
ORDER BY avg_sentence DESC;
```

* Average sentences **per department**
* Only departments with **avg > 2**
* Results sorted **highest to lowest**

---

## 5️⃣ DISTINCT

* Ensures only **unique values** are counted or returned
* Works with aggregates too

```sql
SELECT COUNT(DISTINCT officer_id) FROM arrests;
```

* Counts **how many different officers** made arrests

```sql
SELECT DISTINCT department FROM arrests;
```

* Returns each department **once**, no duplicates

> **Tip:** Use DISTINCT to prevent overcounting when joining tables or working with large datasets.

---

## 6️⃣ Putting it all together: Civic-Tech Examples

### A. Count arrests by officer, only officers with >5 arrests

```sql
SELECT officer_id, COUNT(*) AS arrests_count
FROM arrests
GROUP BY officer_id
HAVING COUNT(*) > 5
ORDER BY arrests_count DESC;
```

### B. Average fine per violation type, only if average > $100

```sql
SELECT violation_type, AVG(fine_amount) AS avg_fine
FROM citations
GROUP BY violation_type
HAVING AVG(fine_amount) > 100
ORDER BY avg_fine DESC;
```

### C. Distinct facilities reporting cases in 2025

```sql
SELECT COUNT(DISTINCT facility_id) AS num_facilities
FROM cases
WHERE date_filed BETWEEN '2025-01-01' AND '2025-12-31';
```

---

## 7️⃣ Quick mental map

1. **WHERE** → filter **rows** before aggregation
2. **GROUP BY** → create **buckets**
3. **Aggregates** → summarize each bucket
4. **HAVING** → filter **buckets** after summarization
5. **ORDER BY** → sort the result set
6. **DISTINCT** → remove duplicates

> ✅ Pro Tip: **WHERE filters row-level data, HAVING filters group-level data.**

---

## 8️⃣ Common Mistakes / Gotchas

| Mistake                                | Why it fails                  | Fix                                         |
| -------------------------------------- | ----------------------------- | ------------------------------------------- |
| Using column in SELECT not in GROUP BY | SQL error (unless aggregated) | Add to GROUP BY or wrap in aggregate        |
| Confusing WHERE & HAVING               | Wrong counts                  | Use WHERE before GROUP BY, HAVING after     |
| Forgetting DISTINCT in counts          | Overcount duplicates          | `COUNT(DISTINCT col)`                       |
| Misordering ORDER BY                   | Unexpected results            | Ensure column exists in SELECT or use alias |
