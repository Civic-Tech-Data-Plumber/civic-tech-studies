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
