> **Learning Log** - DISTINCT Keyword  
> **Date:** 2026-02-05  
> **Source / Exercise:** SQLBolt / "SQL In Easy Steps" by Mike McGrath / ChatGPT  
> **Summary:**Returning unique rows in query results by removing exact duplicates using the `DISTINCT` keyword.

# Quick Reference
- [Distinct Keyword](#distinct-in-sql "Remove duplicate rows from query results")
- [What DISTINCT Is (and Isn’t)](#what-distinction-is "Core definition and misconceptions")
- [Why Duplicates Happen](#why-duplicates-happen-in-the-first-place "Why repeats appear in results")
- [DISTINCT on Multiple Columns](#distinct-works-on-the-entire-selected-row "Row-level uniqueness")
- [DISTINCT vs GROUP BY](#group-by "Choosing the right tool")
- [DISTINCT with JOINs](#distinct-with-joins "Common real-world usage and warnings")
- [Civic-Tech Examples](#civic-tech-examples "Applied use cases")
- [Performance Notes](#performance-note "When DISTINCT can be costly")

---

# `DISTINCT` in SQL

**What it does, what it doesn’t do, and when to use it**

## What Distinction Is

> **`DISTINCT` removes duplicate rows from the result set — not from the table.**

```sql
SELECT DISTINCT jurisdiction
FROM cases;
```

Result:

```
CA
NY
```

That’s it.
No counting.
No grouping.
Just **unique values**.

### DISTINCT does NOT choose “the best row”

This is a critical misconception.

```sql
SELECT DISTINCT defendant_name
FROM cases;
```

SQL does **not**:

* Choose the most recent case
* Choose the most severe outcome
* Choose a representative row

It just removes exact duplicates.

If you need logic, you need:

* `GROUP BY`
* `MAX()`, `MIN()`
* window functions (later)

`DISTINCT` does **not**:

* Change data
* “Group” data
* Pick one row per entity intelligently

It only affects **what is returned** by the `SELECT`.

---

## Why Duplicates Happen in the First Place

Duplicates usually appear when:

* A table naturally contains repeated values (e.g., many rows share the same state)
* You use `JOIN`s (very common)
* You select columns at the wrong level of detail

Example table: `cases`

| case_id | jurisdiction | outcome |
| ------- | ------------ | ------- |
| 1       | CA           | Guilty  |
| 2       | CA           | Plea    |
| 3       | NY           | Guilty  |
| 4       | CA           | Guilty  |

### Without DISTINCT

```sql
SELECT jurisdiction
FROM cases;
```

Result:

```
CA
CA
NY
CA
```

---


## DISTINCT Works on the Entire Selected Row

This is the most common confusion. Two rows are considered duplicates only if every selected column value matches exactly.

```sql
SELECT DISTINCT jurisdiction, outcome
FROM cases;
```

SQL is asking:

> “Are **both** columns identical?”

Result:

```
CA | Guilty
CA | Plea
NY | Guilty
```

Even though `CA` repeats, the **row combination** is different, so it stays.

⚠️ `DISTINCT` does **not** mean “distinct on one column unless you ask for only that column.”

---


### GROUP BY 
**aggregation & analysis**

```sql
SELECT jurisdiction, COUNT(*) AS case_count
FROM cases
GROUP BY jurisdiction;
```

Use when:

* You want counts, sums, averages
* You want **one row per group**

Think:

| Tool       | Purpose           |
| ---------- | ----------------- |
| `DISTINCT` | Remove duplicates |
| `GROUP BY` | Analyze groups    |

---

## DISTINCT with JOINs

JOINs are the #1 reason people reach for DISTINCT.

Example:

* One movie
* Many box office rows (different regions)

```sql
SELECT movies.title
FROM movies
JOIN boxoffice
  ON movies.id = boxoffice.movie_id;
```

Result might show:

```
Toy Story
Toy Story
Toy Story
```

To fix:

```sql
SELECT DISTINCT movies.title
FROM movies
JOIN boxoffice
  ON movies.id = boxoffice.movie_id;
```

⚠️ This is sometimes a **warning sign**:
If you need `DISTINCT` after a JOIN, ask:

> “Did I join at the right level?”  
> If you frequently need DISTINCT after JOINs, it often indicates a cardinality mismatch.
> Sometimes `GROUP BY` is the better fix.

---

## DISTINCT with WHERE

```sql
SELECT DISTINCT jurisdiction
FROM cases
WHERE outcome = 'Guilty';
```

Meaning:

> “Show me all jurisdictions that have **at least one guilty case**.”

This is a powerful pattern in civic-tech analysis.

---

## Performance Note 

* `DISTINCT` can be expensive on large datasets
* Especially with many columns
* It forces SQL to compare rows

Rule of thumb:

* Use `DISTINCT` intentionally
* Avoid it as a “band-aid” for bad joins

---

## Mental models that stick

### 🧠 Think of DISTINCT as:

* “Remove duplicate lines”
* “Show me the unique answers”
* “Collapse identical rows”

### ❌ Not:

* “Group my data”
* “Summarize”
* “Pick one per entity”

---

## Civic-tech Examples

### Unique agencies involved in cases

```sql
SELECT DISTINCT agency_name
FROM cases;
```

### Jurisdictions with ICE-related mentions

```sql
SELECT DISTINCT jurisdiction
FROM cases
WHERE summary LIKE '%ICE%';
```

### Courts that issued sentences above 20 years

```sql
SELECT DISTINCT court_name
FROM cases
WHERE sentence_years > 20;
```

Each of these answers:

> “Which ones exist at least once?”

---

## 📌 Mental Model to Remember Forever

> **If you’re asking “what unique values exist?”, use `DISTINCT`.
> If you’re asking “how many / how much / how often?”, use `GROUP BY`.**
