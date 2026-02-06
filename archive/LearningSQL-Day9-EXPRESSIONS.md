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

SQL engines:

* Do math as numbers
* Do **not** assume display formatting
* Leave presentation decisions to you (or the UI layer)

---

# Ways to add the `%` symbol (from most correct → most situational)

## ✅ Option 1: Convert to text and append `%` (most common)

```sql
SELECT
  title,
  rating * 10 || '%' AS rating_percent
FROM movies
JOIN boxoffice
  ON movies.id = boxoffice.movie_id;
```

### What’s happening

* `rating * 10` → number
* `|| '%'` → string concatenation
* Result becomes **text**

Example output:

```
87%
92%
75%
```

### ⚠️ Important tradeoff

Once you do this:

* `rating_percent` is **no longer numeric**
* You can’t sort or do math on it cleanly

This is fine for:

* Reports
* Tables meant for humans
* Learning logs / GitHub screenshots

---

## ✅ Option 2: Keep numeric + add formatted column (professional pattern)

Best practice is often **two columns**:

```sql
SELECT
  title,
  rating * 10 AS rating_value,
  rating * 10 || '%' AS rating_display
FROM movies
JOIN boxoffice
  ON movies.id = boxoffice.movie_id;
```

Now you have:

* `rating_value` → numeric (sortable, computable)
* `rating_display` → human-friendly

This is extremely common in analytics work.

---

## ⚠️ Option 3: Formatting functions (database-specific)

> works well within SQLBolt lessons

Some databases support formatting functions:

```sql
SELECT
  title,
  printf('%.0f%%', rating * 10) AS rating_percent
FROM movies
JOIN boxoffice
  ON movies.id = boxoffice.movie_id;
```

This works in **SQLite**.

### Why this is less ideal for learning

* Not portable across databases
* Easy to forget syntax
* Overkill for early SQL

Good to know it exists — not essential yet.

---

## 🚫 What you should NOT do

```sql
rating * 10 % AS rating_percent
```

❌ `%` is **not** a formatting operator
❌ In SQL it means **modulo**, not percent display

---

## Mental model to keep

> **Numbers are for computers. Symbols are for humans.**

If you add `%`, you’re switching from:

* **data logic**
  → to
* **presentation logic**

That’s okay — just be intentional.

---

## Civic-tech framing (why this matters)

When publishing:

* Dashboards
* Public-facing tables
* Advocacy summaries

You often want:

* Human-readable values
* Clear units
* No ambiguity

But when **analyzing**:

* Keep values numeric
* Add formatting at the last step

You’re already thinking at that level, which is excellent.

---

# Expressions + Formatting (SQL Mini-Cheat Sheet)

> **Expressions change values.
> Formatting changes how humans see them.**

---

## 1️⃣ What is an expression?

An **expression** is anything that SQL evaluates to produce a value.

```sql
rating * 10
price - discount
domestic_sales + international_sales
```

Expressions can use:

* Columns
* Numbers
* Operators
* Functions

They are evaluated **row by row**.

---

## 2️⃣ Basic arithmetic expressions

| Expression | Meaning        |
| ---------- | -------------- |
| `a + b`    | Addition       |
| `a - b`    | Subtraction    |
| `a * b`    | Multiplication |
| `a / b`    | Division       |

Example:

```sql
SELECT title, rating * 10
FROM movies;
```

---

## 3️⃣ Aliasing expressions (`AS`)

Use `AS` to name the result of an expression.

```sql
SELECT
  title,
  rating * 10 AS rating_percent
FROM movies;
```

* Makes output readable
* Industry standard
* Essential for dashboards & reports

---

## 4️⃣ Turning numbers into human-readable text

### Concatenate text (`||` in SQLite)

```sql
SELECT
  rating * 10 || '%' AS rating_display
FROM movies;
```

| Operator | Meaning |   |                      |
| -------- | ------- | - | -------------------- |
| `        |         | ` | String concatenation |

⚠️ Result becomes **text**, not a number.

---

## 5️⃣ Best practice: numeric + display column

```sql
SELECT
  title,
  rating * 10 AS rating_value,
  rating * 10 || '%' AS rating_display
FROM movies;
```

Why this is professional:

* `rating_value` → sortable, computable
* `rating_display` → readable for humans

This pattern is **very common** in civic-tech and analytics.

---

## 6️⃣ Conditional expressions (`CASE`)

Use `CASE` to label or categorize data.

```sql
SELECT
  title,
  CASE
    WHEN rating >= 8 THEN 'High'
    WHEN rating >= 5 THEN 'Medium'
    ELSE 'Low'
  END AS rating_category
FROM movies;
```

Think:

> “If this condition is true, return that value.”

---

## 7️⃣ Handling NULLs in expressions

NULL breaks math.

```sql
-- ❌ returns NULL
rating * 10
```

Use `COALESCE`:

```sql
COALESCE(rating, 0) * 10
```

| Function         | Purpose                |
| ---------------- | ---------------------- |
| `COALESCE(a, b)` | Use `b` if `a` is NULL |

---

## 8️⃣ Formatting functions (SQLite-specific)

```sql
SELECT
  printf('%.0f%%', rating * 10) AS rating_percent
FROM movies;
```

* Formats numbers
* Adds symbols
* Not portable across databases

Good to know — not required early on.

---

## 9️⃣ What expressions do NOT do

❌ They do not:

* Change stored data
* Add units automatically
* Replace frontend formatting

Expressions only affect the **query result**.

---

## 🔑 Mental model (memorize this)

> **Expressions = math & logic
> Formatting = communication**

If you add `%`, `USD`, or labels:

* You’re in **presentation mode**
* That’s fine — just be intentional

---

## Civic-Tech Context

Expressions let you:

* Normalize metrics
* Compare outcomes
* Create fairness indicators
* Make raw data understandable to the public

They’re the bridge between **raw records** and **meaningful insight**.
