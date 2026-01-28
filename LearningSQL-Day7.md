> Learning log - Day 3  
> Today weLearned how INNER JOIN combines related tables using shared keys. Observed how unmatched rows are excluded, which has important implications when analyzing incomplete or underreported civic data.

---

# Big picture: why JOINS matter (especially in civic tech)

In civic tech, data is **normalized** (split across tables) for good reasons:

* agencies are reused,
* people interact with programs many times,
* services are tracked separately from outcomes,
* budgets, locations, and timelines live in different systems.

JOINS let you **reconstruct reality** from those pieces.

Without JOINS, you only see *silos*.
With JOINS, you see *systems*.

---

## Mental model: INNER JOIN in plain language

An `INNER JOIN` answers this question:

> “Show me rows where **this table** and **that table** have matching values.”

If there is **no match**, the row is excluded.

Think of it as **overlap only**.

---

## A civic-tech flavored example

### Example tables

#### `detentions`

| detention_id | person_id | facility_id | date       |
| ------------ | --------- | ----------- | ---------- |
| 1            | 101       | 5           | 2024-03-01 |
| 2            | 102       | 7           | 2024-03-02 |
| 3            | 103       | 5           | 2024-03-03 |

#### `facilities`

| facility_id | name      | city      |
| ----------- | --------- | --------- |
| 5           | Otay Mesa | San Diego |
| 6           | Adelanto  | Adelanto  |

Notice:

* Facility `7` does **not** exist in `facilities`.

---

### INNER JOIN query

```sql
SELECT
  d.detention_id,
  d.person_id,
  f.name AS facility_name,
  f.city
FROM detentions d
INNER JOIN facilities f
  ON d.facility_id = f.facility_id;
```

---

### Result set

| detention_id | person_id | facility_name | city      |
| ------------ | --------- | ------------- | --------- |
| 1            | 101       | Otay Mesa     | San Diego |
| 3            | 103       | Otay Mesa     | San Diego |

🚨 **What disappeared?**

* Detention with `facility_id = 7`
* Because there was **no matching row** in `facilities`

That is *exactly* what `INNER JOIN` does.

---

## Why INNER JOIN is powerful (and dangerous)

### ✅ Strengths

* Ensures data integrity
* Prevents showing incomplete or orphaned records
* Ideal when you *require* relationships to exist

### ⚠️ Risks (very relevant in civic data)

* Missing reference data = silent exclusion
* Can **hide failures**, gaps, or negligence
* Can undercount affected populations

Example:

> “Show me all detainees and their facilities”
> → INNER JOIN will **drop detainees assigned to unknown or undocumented facilities**

That’s not a SQL bug.
That’s a **policy implication**.

---

## JOIN anatomy (syntax breakdown)

```sql
FROM table_a
INNER JOIN table_b
  ON table_a.shared_key = table_b.shared_key
```

Key ideas:

* `ON` defines *how rows relate*
* Keys should be **stable identifiers** (IDs, not names)
* Aliases (`d`, `f`) improve readability and prevent ambiguity

---

## Why may lessons start with INNER JOIN

Pedagogically, INNER JOIN:

* is the simplest join to reason about,
* reinforces relational thinking,
* forces you to understand keys,
* avoids null-handling complexity (for now).

It’s the **foundation** for every other join.

---

## Preview: what’s coming next (so you’re oriented)

You’ll likely encounter:

| JOIN type       | What it answers                                |
| --------------- | ---------------------------------------------- |
| INNER JOIN      | “Where do both tables agree?”                  |
| LEFT JOIN       | “Show everything from A, even if B is missing” |
| RIGHT JOIN      | “Show everything from B” (less common)         |
| FULL OUTER JOIN | “Show everything from both sides”              |

For civic tech:

* **LEFT JOIN** is often the most ethically revealing
* INNER JOIN is often used for *clean reporting*
* Knowing when *not* to use INNER JOIN is a skill

We’ll get there methodically.

---

## What is a table alias?

A **table alias** is a *temporary nickname* for a table **within a single query**.

### TL;DR

> *Table aliases are temporary shorthand names for tables used within a query. They improve readability, prevent column ambiguity, and are essential for multi-table queries. Aliases are an industry standard and expected in professional SQL.*

```sql
FROM detentions d
```

Here:

* `detentions` = real table name
* `d` = alias

For the rest of *that query only*, `d` **means** `detentions`.

---

## Why aliases exist (the real reasons)

### 1. Prevent ambiguity (this is non-negotiable)

When two tables share column names (very common):

```sql
detentions.person_id
people.person_id
```

Without aliases:

```sql
SELECT person_id
FROM detentions
INNER JOIN people
ON detentions.person_id = people.person_id;
```

❌ SQL error: *“column reference 'person_id' is ambiguous”*

With aliases:

```sql
SELECT d.person_id
FROM detentions d
INNER JOIN people p
ON d.person_id = p.person_id;
```

✔️ Clear. Explicit. Unambiguous.

---

### 2. Improve readability (especially in long queries)

Compare:

```sql
SELECT detentions.detention_id, facilities.name
FROM detentions
INNER JOIN facilities
ON detentions.facility_id = facilities.facility_id;
```

vs.

```sql
SELECT d.detention_id, f.name
FROM detentions d
INNER JOIN facilities f
ON d.facility_id = f.facility_id;
```

Professionals overwhelmingly prefer the second version.

---

### 3. Enable self-joins (advanced but critical)

Sometimes a table joins **to itself** (e.g., supervisors in an employees table).

Aliases make this possible:

```sql
FROM employees e
INNER JOIN employees manager
ON e.manager_id = manager.employee_id;
```

Without aliases, SQL cannot distinguish the two roles.

---

## How aliases are assigned

### Basic syntax

```sql
FROM table_name alias
```

or (optional keyword):

```sql
FROM table_name AS alias
```

Both are valid.
Most SQL professionals **omit `AS` for tables**:

```sql
FROM detentions d
```

For **column aliases**, `AS` is more commonly used:

```sql
SELECT f.name AS facility_name
```

---

## Where aliases can be used

Once defined, you use them **everywhere** in the query:

```sql
SELECT
  d.detention_id,
  p.full_name,
  f.name AS facility_name
FROM detentions d
INNER JOIN people p
  ON d.person_id = p.person_id
INNER JOIN facilities f
  ON d.facility_id = f.facility_id;
```

Aliases apply to:

* `SELECT`
* `ON`
* `WHERE`
* `GROUP BY`
* `ORDER BY`

---

## Are aliases an industry standard?

**Yes. Unequivocally.**

In practice:

* ✔️ Used in almost every multi-table query
* ✔️ Expected in code reviews
* ✔️ Essential for maintainability
* ✔️ Required in complex joins and analytics

If you submit SQL **without aliases** in real-world work:

* it will be flagged as novice or careless,
* or outright rejected in production contexts.

---

## Common alias conventions

| Convention    | Example               | When used          |
| ------------- | --------------------- | ------------------ |
| Single letter | `d`, `f`, `p`         | Small queries      |
| Abbreviations | `det`, `fac`          | Medium queries     |
| Role-based    | `origin`, `dest`      | Self-joins         |
| Semantic      | `arrests`, `officers` | Analytical clarity |

For civic tech, clarity > brevity:

```sql
FROM detentions d
FROM shelters s
FROM services svc
```

---

## One subtle but important rule

Aliases are **not persistent**.
They:

* exist only inside the query,
* do not rename the table,
* do not affect the database schema.

Think of them as **variables**, not renames.

---

## Why your textbook may not have introduced them yet

Intro texts often delay aliases to:

* reduce cognitive load,
* avoid introducing ambiguity too early,
* keep examples visually simple.

But **once JOINS appear**, aliases are no longer optional.

You’re encountering them at exactly the right time.

---

## What `ON` does (exactly)

The `ON` clause defines **how rows from two tables are matched** during a `JOIN`.

At its simplest:

> **`ON` specifies the condition that relates one table’s rows to another table’s rows.**

Most commonly, that relationship is **equality between key columns**.

---

## The canonical example

```sql
FROM employees e
INNER JOIN facilities f
ON e.facility_id = f.facility_id
```

This reads as:

> “Join each employee row to the facility row **where the facility IDs match**.”

That’s the most common pattern you’ll see in the real world.

---

## Why this is usually IDs

Relational databases are designed around **keys**:

* **Primary key**: uniquely identifies a row
* **Foreign key**: references another table’s primary key

Example:

| Table        | Column        | Role        |
| ------------ | ------------- | ----------- |
| `facilities` | `facility_id` | Primary key |
| `employees`  | `facility_id` | Foreign key |

So this join:

```sql
ON e.facility_id = f.facility_id
```

is saying:

> “Link employees to the facility they belong to.”

---

## Important clarification: `ON` is not just `WHERE`

Although it *looks* similar, `ON` and `WHERE` are **not interchangeable**.

### `ON`

* Defines **row matching between tables**
* Applied **during the join operation**
* Determines which rows are paired together

### `WHERE`

* Filters the **final result set**
* Applied **after joins are performed**

This distinction matters a lot once you get to `LEFT JOIN` (you will).

---

## Example showing the difference

```sql
FROM detentions d
LEFT JOIN facilities f
ON d.facility_id = f.facility_id
WHERE f.state = 'CA';
```

This:

* Joins detentions to facilities
* Then removes rows where no CA facility exists

Move the condition into `ON`:

```sql
FROM detentions d
LEFT JOIN facilities f
ON d.facility_id = f.facility_id
AND f.state = 'CA';
```

Now:

* All detentions remain
* Facility data only appears if the facility is in CA

Same tables. Very different results.

---

## `ON` can be more than equality

Although equality joins are most common, `ON` supports **any condition**:

```sql
ON o.order_date BETWEEN p.start_date AND p.end_date
```

```sql
ON LOWER(a.name) = LOWER(b.name)
```

```sql
ON e.salary > g.minimum_salary
```

But for **civic-tech datasets**, expect mostly:

* ID-based joins
* date-based joins
* jurisdiction-based joins

---

## Mental model (use this)

> **`ON` answers: “Which rows from table A should be paired with which rows from table B?”**
> **`WHERE` answers: “Which of those paired rows do I keep?”**

If you internalize that distinction now, you’ll avoid one of the most common SQL failure modes later.

---

# The core difference of how ON and WHERE are used

> **`ON` controls how rows are matched.  
> `WHERE` controls which matched rows survive.**

The confusion comes from the fact that SQL *reads* top-to-bottom but *executes* in a different order.

---

# SQL’s actual execution order (simplified)

When you write:

```sql
SELECT ...
FROM A
LEFT JOIN B
  ON condition
WHERE filter;
```

SQL does this internally:

1. **FROM** table A
2. **JOIN** table B using the `ON` condition
   → rows are matched (or padded with NULLs)
3. **WHERE** filters the result
   → rows are kept or discarded
4. **SELECT** columns

That timing difference is everything.

---

# Let’s use a concrete civic-tech example

### Tables

**detentions**

| detention_id | facility_id |
| ------------ | ----------- |
| 1            | 10          |
| 2            | 20          |
| 3            | 30          |

**facilities**

| facility_id | state |
| ----------- | ----- |
| 10          | CA    |
| 20          | TX    |

Note:

* Facility `30` **does not exist**
* Detention `3` still exists

---

# Case 1 — condition in `ON` (safe)

```sql
SELECT d.detention_id, f.state
FROM detentions d
LEFT JOIN facilities f
  ON d.facility_id = f.facility_id
 AND f.state = 'CA';
```

### Step-by-step

**JOIN step (`ON`)**

| detention_id | state |
| ------------ | ----- |
| 1            | CA    |
| 2            | NULL  |
| 3            | NULL  |

Why?

* Detention 1 → matches CA facility
* Detention 2 → facility exists but not CA → no match → NULL
* Detention 3 → no facility → NULL

**No WHERE filter**, so all rows survive.

✅ **All detentions preserved**

---

# Case 2 — condition in `WHERE` (danger)

```sql
SELECT d.detention_id, f.state
FROM detentions d
LEFT JOIN facilities f
  ON d.facility_id = f.facility_id
WHERE f.state = 'CA';
```

### Step-by-step

**JOIN step (`ON`)**

| detention_id | state |
| ------------ | ----- |
| 1            | CA    |
| 2            | TX    |
| 3            | NULL  |

**WHERE filter**

```sql
WHERE f.state = 'CA'
```

* Row 1 → CA → kept
* Row 2 → TX → removed
* Row 3 → NULL → removed (NULL ≠ 'CA')

### Final result

| detention_id | state |
| ------------ | ----- |
| 1            | CA    |

❌ **Detentions lost**

---

# The invisible trap

This is the key insight:

> **`WHERE` filters out NULLs created by a `LEFT JOIN`.**

So the moment you reference the joined table in `WHERE`, you often turn:

```sql
LEFT JOIN
```

into:

```sql
INNER JOIN
```

without realizing it.

---

# Why this doesn’t matter for INNER JOIN

With `INNER JOIN`, unmatched rows are already discarded.

So:

```sql
INNER JOIN ... ON condition
WHERE other_condition
```

vs

```sql
INNER JOIN ... ON condition AND other_condition
```

Often produce the same result.

That’s why this confusion survives so long — it only bites you once `LEFT JOIN` enters the picture.

---

# Mental model that finally sticks

### Think in phases:

#### `ON`

> “Which rows are allowed to **shake hands**?”

#### `LEFT JOIN`

> “If no handshake happens, keep the left row anyway and pad with NULLs.”

#### `WHERE`

> “Now throw away any rows that don’t meet my final criteria.”

---

# Rule you can safely write in your learning log

> **Put relationship logic in `ON`.
> Put filtering logic in `WHERE`.
> If using `LEFT JOIN`, be extremely careful referencing the joined table in `WHERE`.**

---

# One-liner summary

> `ON` affects **matching**.
> `WHERE` affects **survival**.

--

# Understanding `NULL`

> This is where **technical correctness meets human communication**, and the answer depends on *who* the results are for and *where* they are shown.

---

## 🧠 The industry rule of thumb

> **Keep `NULL` in the database.
> Translate `NULL` at the presentation layer.**

That separation is considered best practice in data engineering, analytics, and civic-tech.

---

## Why `NULL` should stay `NULL` (in data storage)

`NULL` is not a value — it is **the absence of a value**.

Replacing it with strings like `"N/A"` or `"Unknown"` inside the database causes real problems:

### ❌ Problems with replacing NULL in tables

* Breaks numeric calculations (`AVG`, `SUM`, `COUNT`)
* Requires special-case logic everywhere
* Loses semantic meaning (unknown vs not applicable vs missing)
* Makes joins and filters less reliable
* Pollutes datasets irreversibly

Example:

```sql
AVG(outcome_length)
```

Works with NULLs
❌ Fails if `"N/A"` is stored instead

So in **storage, raw query outputs, and analysis pipelines**:
✅ **NULL stays NULL**

---

## Where human-friendly labels belong

### 1️⃣ Reports, dashboards, and exports

This is where translation happens.

Example:

```sql
SELECT
  case_id,
  COALESCE(outcome, 'Unknown') AS outcome
FROM cases;
```

Or:

```sql
CASE
  WHEN outcome IS NULL THEN 'Not recorded'
  ELSE outcome
END AS outcome
```

The underlying data remains intact.
Only the **display** changes.

---

## 📌 Common human-friendly mappings (by intent)

This is subtle but important.

| Display Label         | When to use it                         |
| --------------------- | -------------------------------------- |
| `Unknown`             | Value should exist but wasn’t recorded |
| `Not Available (N/A)` | Data doesn’t apply in this context     |
| `Pending`             | Value expected in the future           |
| `Not Disclosed`       | Value intentionally withheld           |
| `Missing`             | Data loss or ingestion failure         |

**Civic-tech note:**
Using the *wrong* label can accidentally mislead.

---

## Civic-tech example (why this matters)

Imagine a report on detention outcomes:

| Outcome  |
| -------- |
| Released |
| Removed  |
| Unknown  |

If `Unknown` really means:

* no reporting requirement
* sealed case
* system error

…those are **very different realities**.

So in civic-tech:

* Store NULL
* Document what NULL means
* Translate carefully depending on audience

---

## 📌 Where people *do* keep literal `NULL`

| Context                    | Keep `NULL`? |
| -------------------------- | ------------ |
| Raw SQL query results      | ✅ Yes        |
| ETL pipelines              | ✅ Yes        |
| Analytics notebooks        | ✅ Yes        |
| API responses (JSON)       | Often `null` |
| Internal engineering tools | ✅ Yes        |

---

## 📌 Where people almost never show `NULL`

| Context                  | Replace it? |
| ------------------------ | ----------- |
| Public dashboards        | ✅ Yes       |
| CSVs for journalists     | Usually     |
| Reports for policymakers | Yes         |
| UI tables                | Yes         |

---

## The safe pattern you can write in your journal

> **NULL is a data-state, not a user interface choice.**
> Preserve it in storage; interpret it at the edge.

---

## Bonus tips: the SQL tools you’ll see everywhere

| Tool             | Purpose              |
| ---------------- | -------------------- |
| `COALESCE(a, b)` | First non-null value |
| `CASE WHEN`      | Conditional labeling |
| `IS NULL`        | Precise filtering    |
| `IS NOT NULL`    | Completeness checks  |
