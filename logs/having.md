> **Learning Log** - HAVING Keyword  
> **Date:** 2026-02-05  
> **Source / Exercise:** "SQL In Easy Steps" by Mike McGrath / ChatGPT  
> **Summary:** Filtering grouped results with the `HAVING` keyword  

# Quick Reference

* [HAVING Keyword](#having-keyword "Filtering grouped results after aggregation.")
* [Filtering Groups](#filtering-groups "How to filter groups using aggregate functions.")
* [Difference Between WHERE and HAVING](#difference-between-where-and-having "Explaining the difference in use case.")
* [Civic Tech Example](#civic-tech-example "Applying HAVING in civic-tech data analysis.")
* [Best Practices](#best-practices "Tips for using HAVING effectively and safely.")

---

## HAVING Keyword

The `HAVING` keyword is used to **filter groups of rows** created by a `GROUP BY` clause.

> Unlike `WHERE`, which filters **individual rows**, `HAVING` applies after aggregation to filter **entire groups**.

---

## Filtering Groups

**Example:** Count court cases per jurisdiction and only show jurisdictions with more than 10 cases:

```sql
-- Count cases per jurisdiction
SELECT jurisdiction, COUNT(*) AS case_count
FROM court_cases
GROUP BY jurisdiction
HAVING COUNT(*) > 10;  -- Only show jurisdictions with more than 10 cases
```

**What happens:**

1. `GROUP BY jurisdiction` groups all rows by jurisdiction.
2. `COUNT(*)` calculates the number of cases in each group.
3. `HAVING COUNT(*) > 10` filters out groups with fewer than 11 cases.

> 💡 Think of `HAVING` as a “WHERE clause for groups.”

---

## Difference Between WHERE and HAVING

| Keyword | Filters         | When Applied    | Works With                                               |
| ------- | --------------- | --------------- | -------------------------------------------------------- |
| WHERE   | Individual rows | Before grouping | Any column                                               |
| HAVING  | Groups          | After grouping  | Usually with aggregate functions (COUNT, SUM, AVG, etc.) |

**Example of WHERE vs HAVING:**

```sql
-- Filter rows first, then group
SELECT jurisdiction, COUNT(*) AS case_count
FROM court_cases
WHERE date_filed >= '2025-01-01'  -- row-level filter
GROUP BY jurisdiction
HAVING COUNT(*) > 10;             -- group-level filter
```

> ✅ Use WHERE for raw row conditions, HAVING for group conditions.

---

## Civic Tech Example

Suppose you’re analyzing ICE-related court cases in Los Angeles and want to focus only on jurisdictions with high case volume:

```sql
SELECT court_name, COUNT(*) AS ice_cases
FROM court_cases
WHERE summary LIKE '%ICE%'
GROUP BY court_name
HAVING COUNT(*) >= 5;  -- Focus only on courts with 5+ ICE cases
```

**Why this is useful:**

* Quickly identifies hotspots or patterns in civic data.
* Prevents getting lost in low-activity areas (data noise).
* Makes dashboards, reports, and visualizations more meaningful.

---

## Best Practices

1. **Always use `GROUP BY` with `HAVING`.**

   * HAVING without aggregation is unusual and can confuse readers.

2. **Combine WHERE and HAVING carefully.**

   * Filter irrelevant rows with WHERE **first**, then apply HAVING for meaningful groups.

3. **Readable aggregates:**

   * Give aggregates aliases using `AS` for clarity, e.g., `COUNT(*) AS case_count`.

4. **Avoid unnecessary calculations in HAVING.**

   * Aggregate once in SELECT, then reference the alias in HAVING when your DBMS allows.

```sql
SELECT jurisdiction, COUNT(*) AS case_count
FROM court_cases
GROUP BY jurisdiction
HAVING case_count > 10;  -- Use alias if supported
```

---

### Quick Mental Model

* **WHERE** → “Which rows should I consider?”
* **GROUP BY** → “Put these rows into buckets.”
* **HAVING** → “Which buckets do I care about?”

> For a civic tech data engineer, HAVING is key for identifying meaningful trends in large datasets — for example, where social services are most needed or which areas have recurring legal issues.
