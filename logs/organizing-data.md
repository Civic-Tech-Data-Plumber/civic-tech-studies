> **Learning Log** - Organizing Data  
> **Date:** 2026-02-04  
> **Source / Exercise:** ChatGPT  
> **Summary:** Ways to **organize and refine your query results**.

### Quick Reference
- [`DISTINCT`](#distinct)
- [`GROUP BY`](#group-by)
- [`ORDER BY`](#order-by)
- [`LIMIT`](#limit)
- [`OFFSET`](#offset)
- [Putting it all together](#putting-it-all-together)
---

💡 **Quick mental model**

```
DISTINCT → Remove duplicates
GROUP BY → Aggregate by category
ORDER BY → Arrange the results
ASC/DESC → Choose direction
LIMIT → How many to see
OFFSET → Skip first N
```

## `DISTINCT` 
**– Remove duplicates**

* **Purpose:** Only return unique values from a column or combination of columns.
* **Example:**

```sql
SELECT DISTINCT jurisdiction
FROM court_cases;
```

📌 **Tip:** Think “filter out repeats” before analyzing or aggregating data.

---

## `GROUP BY` 
**– Aggregate data**

* **Purpose:** Combine rows with the same value(s) in one or more columns and perform aggregate functions (`COUNT`, `SUM`, `AVG`, etc.)
* **Example:**

```sql
SELECT jurisdiction, COUNT(*) AS case_count
FROM court_cases
GROUP BY jurisdiction;
```

📌 **Tip:** Always pair `GROUP BY` with an **aggregate function**, or your query will error.

---

## `ORDER BY` 
**– Sort results**

* **Purpose:** Arrange your result set by one or more columns.
* **Ascending (ASC) / Descending (DESC)**

```sql
SELECT jurisdiction, COUNT(*) AS case_count
FROM court_cases
GROUP BY jurisdiction
ORDER BY case_count DESC;
```

📌 **Tip:** Default is `ASC` (ascending).

---

## `LIMIT` 
**– Control the number of rows returned**

* **Purpose:** Return only the first N rows from your query.
* **Example:**

```sql
SELECT *
FROM court_cases
ORDER BY date_filed DESC
LIMIT 10;
```

📌 **Tip:** Essential for previewing large datasets without overwhelming your system.

---

## `OFFSET` 
**– Skip rows**

* **Purpose:** Skip the first N rows before returning results.
* **Example:**

```sql
SELECT *
FROM court_cases
ORDER BY date_filed DESC
LIMIT 10 OFFSET 20;
```

📌 **Tip:** Useful for paging through results in chunks.

---

## Putting it all together

```sql
SELECT jurisdiction, COUNT(*) AS case_count
FROM court_cases
WHERE outcome IS NOT NULL
GROUP BY jurisdiction
ORDER BY case_count DESC
LIMIT 5 OFFSET 0;
```

**Explanation:**

1. Filter out rows with missing outcomes.
2. Aggregate by jurisdiction.
3. Count cases per jurisdiction.
4. Sort from most cases to fewest.
5. Return only the top 5 jurisdictions.
