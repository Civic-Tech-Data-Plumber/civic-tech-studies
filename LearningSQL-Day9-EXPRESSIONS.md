> Learning log Day 9  
> Expressions are calculations or transformations applied to data that produce a value in a query result.
> They let you compute, transform, or derive new values from existing data inside a SQL query.

# SQL Expressions — a proper lesson

## 1️⃣ What an expression *is* (plain English)

An **expression** is anything that:

* Evaluates to a value
* Per row
* At query time

Examples of values:

* A number
* A string
* A boolean (TRUE / FALSE)
* A date
* NULL

If SQL can **evaluate it**, it’s an expression.

Key difference:

* Not just *formatting*
* Not just *output*
* They **produce values** SQL can select, filter, sort, or group by

---

## 2️⃣ Simplest expressions (you should already know these)

### Column reference

```sql
SELECT sentence_years
FROM cases;
```

`sentece_years` is an expression.
SQL evaluates it for each row.

---

### Literal values

```sql
SELECT 5;
SELECT 'CA';
SELECT 3.14;
```

These are expressions too.
(No table needed.)

---

## 3️⃣ Arithmetic expressions (very common)

```sql
SELECT
  sentence_years,
  sentence_years * 12 AS sentence_months
FROM cases;
```

Expression:

```sql
sentence_years * 12
```

SQL evaluates this **per row**, then returns the result.

Civic-tech example:

> “Convert years to months so comparisons are easier.”

---

## 4️⃣ String expressions

```sql
SELECT
  first_name || ' ' || last_name AS full_name
FROM defendants;
```

Expression:

```sql
first_name || ' ' || last_name
```

Creates a **derived value**.

---

## 5️⃣ Comparison expressions (produce TRUE / FALSE)

You’ve been using these already.

```sql
sentence_years > 10
```

This is an expression that evaluates to:

* TRUE
* FALSE
* NULL

Used in:

```sql
WHERE sentence_years > 10
```

Or even:

```sql
SELECT
  case_id,
  sentence_years > 10 AS long_sentence
FROM cases;
```

Yes — you can **SELECT booleans**.

---

## 6️⃣ Conditional expressions (CASE)

This is where expressions get powerful.

```sql
SELECT
  case_id,
  CASE
    WHEN sentence_years >= 20 THEN 'Severe'
    WHEN sentence_years >= 10 THEN 'Long'
    ELSE 'Short'
  END AS sentence_category
FROM cases;
```

This is a **multi-branch expression**.

Think:

> “If this, return that; otherwise return something else.”

Used constantly in:

* Reports
* Dashboards
* Civic summaries

---

## 7️⃣ NULL-handling expressions

### COALESCE (professional essential)

```sql
SELECT
  COALESCE(outcome, 'Unknown') AS outcome_display
FROM cases;
```

Expression:

```sql
COALESCE(outcome, 'Unknown')
```

Meaning:

> “If outcome is NULL, show ‘Unknown’.”

---

## 8️⃣ Expressions in ORDER BY (important subtlety)

You can sort by expressions:

```sql
ORDER BY sentence_years * 12 DESC;
```

Or by alias:

```sql
ORDER BY sentence_months DESC;
```

Same logic:

* Expression evaluated
* Then used to sort

---

## 9️⃣ Expressions vs functions (clarifying jargon)

| Term       | Meaning                               |
| ---------- | ------------------------------------- |
| Expression | Any computation that produces a value |
| Function   | A named expression provided by SQL    |

Example:

```sql
UPPER(jurisdiction)
```

* `UPPER()` = function
* Entire thing = expression

All functions are expressions.
Not all expressions are functions.

---

## 📌 Where expressions can appear (important map)

Expressions are allowed in:

* `SELECT`
* `WHERE`
* `ORDER BY`
* `GROUP BY`
* `HAVING`
* `JOIN ON`

Example:

```sql
SELECT
  case_id,
  sentence_years * 12 AS months
FROM cases
WHERE sentence_years * 12 > 120
ORDER BY sentence_years * 12 DESC;
```

Same expression, three places.

---

## ⛔ What expressions are *not*

❌ They are not:

* Stored data (unless you save them)
* Permanent changes
* Table modifications

Expressions are **query-time logic**.

---

## 📝 Why expressions matter in civic-tech

Expressions let you:

* Normalize data (years → months)
* Categorize outcomes
* Flag extreme cases
* Humanize NULLs
* Create indicators for analysis

Without altering source data.

That’s critical for:

* Transparency
* Reproducibility
* Ethical analysis

---

## A one-sentence mental model worth keeping

> **Expressions let you ask “what if this data were transformed?” without changing the data itself.**
