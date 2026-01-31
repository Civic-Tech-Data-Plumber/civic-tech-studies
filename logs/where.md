Learning Log - Understand `WHERE`  Date: 2026-01-31
Source / Exercise: SQLBolt / ChatGPT  
Summary: Explain how the SQL `WHERE` clause filters data.

> ❗ STILL NEEDS EDITING AND QUICK REFERENCE FIX!

### Quick Reference

- [WHERE Clause Basics](#where-clause-basics "What the WHERE clause does")
- [Comparison Operators](#comparison-operators "Operators like =, !=, >, <, etc.")
- [Pattern Matching (LIKE / NOT LIKE)](#pattern-matching-like--not-like "Using LIKE with wildcards")
- [Range Filters (BETWEEN / NOT BETWEEN)](#range-filters-between--not-between "Using inclusive ranges")
- [List Filters (IN / NOT IN)](#list-filters-in--not-in "Filtering against a list")
- [NULL Handling](#null-handling "How to filter NULLs correctly")
- [Common Mistakes](#common-mistakes "Failure modes and gotchas")
- [Examples](#examples "Example queries using WHERE")

---

# WHERE Clause Basics

> **One-Sentence Mental Model (Memorize This)**: it filters rows by testing conditions. 
> Decide *what kind of data*, *how precise*, and *what to include or exclude*, then choose the operator.

```sql
SELECT *
FROM table_name
WHERE condition;
```

## Common Operators

List and descripions of operators:

| Operator / Keyword              | Condition Description                                 | SQL Example                    |
| ------------------------------- | ----------------------------------------------------- | -------------------------------|
| `=`, `!=`, `<`, `<=`, `>`, `>=` | Standard comparison operators (equality & inequality) | `col_name != 4`                |
| `BETWEEN ... AND ...`           | Value is within a range (inclusive)                   | `col_name BETWEEN 1.5 AND 10.5`|
| `NOT BETWEEN ... AND ...`       | Value is outside a range (inclusive)                  | `col_name NOT BETWEEN 1 AND 10`|
| `IN (...)`                      | Value exists in a specified list                      | `col_name IN (2, 4, 6)`        |
| `NOT IN (...)`                  | Value does not exist in a specified list              | `col_name NOT IN (1, 3, 5)`    |
| LIKE, NOT LIKE                  | Value exists (or not) in a text strings               | ``summary LIKE '%ICE%'`        |
- `BETWEEN` is inclusive of both boundary values.
- `IN` and `NOT IN` are often clearer and faster than chaining many OR conditions.
- These operators are commonly combined with `AND` / `OR` for more complex filters.
- `LIKE` uses wildcards `%` to mach any number of characters, and `_` to match exactly one character

| Operator | Meaning                  | Example                 |
| -------- | ------------------------ | ----------------------- |
| `=`      | Equal to                 | `age = 18`              |
| `!=`     | Not equal to             | `status != 'Dismissed'` |
| `<`      | Less than                | `score < 50`            |
| `<=`     | Less than or equal to    | `age <= 65`             |
| `>`      | Greater than             | `salary > 50000`        |
| `>=`     | Greater than or equal to | `height >= 170`         |

---

## LIKE & NOT LIKE 

We're going to cover ==`LIKE` and `NOT LIKE` in depth because they're essential in the civic-tech field, and that's my goal, as you may have become aware.

```sql
WHERE name LIKE 'San%'
```

| Operator / Keyword | Condition Description                                  | SQL Example                         |
|-------------------|--------------------------------------------------------|-------------------------------------|
| `LIKE`            | Text matches a pattern using wildcards                 | `summary LIKE '%ICE%'`              |
| `NOT LIKE`        | Text does not match a pattern                          | `summary NOT LIKE '%detention%'`    |

### LIKE:

- Is case-sensitive or not depending on the database (SQLite is usually case-insensitive)
- Can be slow on large datasets
- Is often replaced later with:
  - Full-text search
  - Regex
  - Indexed search columns
But for learning, exploration, and civic analysis — it’s exactly the right tool.

---

### What `LIKE` and `NOT LIKE` Do (Plain English)

`LIKE` and `NOT LIKE` are **pattern-matching operators** used in a `WHERE` clause to filter **text (strings)**.

They answer questions like:

* “Does this text **contain** a word or phrase?”
* “Does this value **start with** or **end with** certain characters?”

They are essential anytime you’re working with:

* Case summaries
* Descriptions
* Names
* Notes
* Free-text fields


### How `LIKE` Works

`LIKE` uses **wildcards**:

| Wildcard | Meaning                          |
| -------- | -------------------------------- |
| `%`      | Matches any number of characters |
| `_`      | Matches exactly one character    |

  * Wildcards
  * Partial matches
  * Performance considerations

### Common Patterns

```sql
summary LIKE '%ICE%'
```

➡ Contains “ICE” anywhere

```sql
name LIKE 'San%'
```

➡ Starts with “San”

```sql
code LIKE '%CA'
```

➡ Ends with “CA”

---

### What `NOT LIKE` Does

`NOT LIKE` is simply the inverse:

```sql
summary NOT LIKE '%detention%'
```

➡ Excludes rows that mention “detention”

This is extremely useful for:

* Removing noise
* Excluding boilerplate text
* Narrowing analysis

---

### One Important Professional Note ⚠️

`LIKE`:

- Is case-sensitive or not depending on the database (SQLite is usually case-insensitive)
- Can be slow on large datasets
- Is often replaced later with:
  - Full-text search
  - Regex
  - Indexed search columns

---

# WHERE Keyword Decision Guide

*(How to decide what to filter, and how)*


## Step 1: What kind of data am I filtering?

**It's not about syntax — it’s about intent.**

Ask this **first**, always.

| Data Type      | Ask Yourself                         | Likely Operator          |
| -------------- | ------------------------------------ | ------------------------ |
| Number / Date  | Am I comparing amounts or time?      | `=`, `>`, `<`, `BETWEEN` |
| Category       | Is this one of a known set?          | `IN`, `NOT IN`           |
| Text           | Am I searching for words or phrases? | `LIKE`, `NOT LIKE`       |
| Boolean        | Is this true or false?               | `= TRUE / FALSE`         |
| Missing values | Could this be empty?                 | `IS NULL`, `IS NOT NULL` |

---

## Step 2: Am I **including** or **excluding**?

This single question prevents half of beginner mistakes.

| Goal                 | Use                                       |
| -------------------- | ----------------------------------------- |
| Keep matching rows   | `=`, `IN`, `LIKE`, `BETWEEN`              |
| Remove matching rows | `!=`, `NOT IN`, `NOT LIKE`, `NOT BETWEEN` |

> If your result set is “too big,” you probably need **exclusion(s)**.

---

## Step 3: How precise do I need to be?

| Precision Level | When to Use              | Example                                      |
| --------------- | ------------------------ | -------------------------------------------- |
| Exact           | IDs, codes, fixed values | `court_id = 123`                             |
| Range           | Dates, ages, scores      | `date BETWEEN '2025-01-01' AND '2025-07-01'` |
| Fuzzy           | Human-written text       | `summary LIKE '%use of force%'`              |

**Rule of thumb:**
The more human the data, the fuzzier the filter.

---

## Step 4: How many conditions am I combining?

### Single condition

```sql
WHERE outcome = 'Dismissed'
```

### Multiple conditions (all must be true)

```sql
WHERE jurisdiction = 'Los Angeles, CA'
  AND outcome = 'Guilty'
```

### Multiple conditions (any can be true)

```sql
WHERE outcome = 'Guilty'
   OR outcome = 'Plea'
```

---

## Step 5: AND / OR — Do I need parentheses?

If you are mixing `AND` and `OR`, the answer is **yes**.

### ❌ Ambiguous (dangerous)

```sql
WHERE jurisdiction = 'CA'
  AND summary LIKE '%ICE%'
   OR summary LIKE '%detention%'
```

### ✅ Explicit (professional)

```sql
WHERE jurisdiction = 'CA'
  AND (
        summary LIKE '%ICE%'
     OR summary LIKE '%detention%'
  )
```

> **SQL reads `AND` before `OR`.**
> Parentheses make your intent unmistakable.

---

## Step 6: Did I consider NULLs?

`NULL` is not equal to anything — even itself.

### ❌ Wrong

```sql
WHERE outcome = NULL
```

### ✅ Correct

```sql
WHERE outcome IS NULL
```

### Or exclude missing data:

```sql
WHERE outcome IS NOT NULL
```

If your results seem “wrong,” check for NULLs first.

---

## Step 7: Sanity-check your query

Before trusting results, ask:

* Am I accidentally excluding records?
* Did I mean **AND** or **OR**?
* Is my text filter too broad?
* Are there NULLs hiding data?

A good habit:

```sql
SELECT COUNT(*) ...
```

before running full queries.

---

## Real Civic-Tech Example (Your Domain)

```sql
WHERE jurisdiction = 'Los Angeles, CA'
  AND date_filed BETWEEN '2025-01-01' AND '2025-07-31'
  AND outcome IS NOT NULL
  AND (
        summary LIKE '%ICE%'
     OR summary LIKE '%Immigration and Customs Enforcement%'
     OR summary LIKE '%detention%'
     OR summary LIKE '%use of force%'
  )
```

This shows:

* Exact filtering (jurisdiction)
* Range filtering (dates)
* Data hygiene (NULL check)
* Fuzzy text matching (real-world language)

That’s **analyst-grade SQL**.

---

# `AND`, `OR`, and Parentheses

*(How SQL actually decides what rows survive)*

---

## 1️⃣ The single most important rule

> **SQL evaluates `AND` before `OR`.**

This is not optional.
This is not stylistic.
This is the root of many bad analyses.

**Order of evaluation (simplified):**

1. `()`
2. `AND`
3. `OR`

---

## 2️⃣ What `AND` really means

`AND` means **all conditions must be true** for a row to survive.

```sql
WHERE jurisdiction = 'Los Angeles, CA'
  AND outcome = 'Guilty'
```

Think:

> “Keep rows that satisfy *this* **and** *that*.”

This **narrows** your result set.

---

## 3️⃣ What OR really means

`OR` means **at least one condition must be true**.

```sql
WHERE outcome = 'Guilty'
   OR outcome = 'Plea'
```

Think:

> “Keep rows that satisfy *this* **or** *that* (or both).”

This **widens** your result set.

---

## 4️⃣ The danger zone: mixing `AND` + `OR`

### ❌ Looks reasonable, but is wrong

```sql
WHERE jurisdiction = 'CA'
  AND summary LIKE '%ICE%'
   OR summary LIKE '%detention%'
```

### What SQL actually reads

```sql
WHERE (jurisdiction = 'CA' AND summary LIKE '%ICE%')
   OR summary LIKE '%detention%'
```

### Translation:

> “Give me:
>
> * CA cases mentioning ICE
>   **OR**
> * ANY case mentioning detention (anywhere in the country)”

This is **not** what most people intend.

---

## 5️⃣ Parentheses = intent made explicit

### ✅ Correct and professional

```sql
WHERE jurisdiction = 'CA'
  AND (
        summary LIKE '%ICE%'
     OR summary LIKE '%detention%'
  )
```
 
### Translation:

> “Only CA cases, and within those, keep ones that mention ICE or detention.”

Parentheses override SQL’s default logic and **protect your meaning**.

---

## 6️⃣ Visual way to think about it

### Without parentheses (implicit logic)

```
A AND B OR C
↓
(A AND B) OR C
```

### With parentheses (explicit logic)

```
A AND (B OR C)
```

> That single pair of parentheses can **double or halve your dataset**.

---

## 7️⃣ `AND` / `OR` patterns you’ll use constantly

### Pattern 1: One fixed filter + many text matches

*(Very common in civic-tech)*

```sql
WHERE jurisdiction = 'Los Angeles, CA'
  AND (
        summary LIKE '%ICE%'
     OR summary LIKE '%detention%'
     OR summary LIKE '%use of force%'
  )
```

### Pattern 2: Multiple categories

```sql
WHERE outcome IN ('Guilty', 'Plea', 'Settled')
```

Equivalent to:

```sql
WHERE outcome = 'Guilty'
   OR outcome = 'Plea'
   OR outcome = 'Settled'
```

---

### Pattern 3: Excluding conditions safely

```sql
WHERE outcome IS NOT NULL
  AND outcome != 'Dismissed'
```

---

## 8️⃣ Parentheses + NOT (advanced but important)

```sql
WHERE NOT (
      outcome = 'Dismissed'
   OR outcome = 'Withdrawn'
)
```

This means:

> “Exclude rows where outcome is Dismissed or Withdrawn.”

NOT applies to **everything inside the parentheses**.

---

## 9️⃣ Debugging AND / OR issues (pro trick)

If a query feels wrong:

### Step 1: Rewrite with indentation

```sql
WHERE jurisdiction = 'CA'
  AND (
        summary LIKE '%ICE%'
     OR summary LIKE '%detention%'
  )
```

### Step 2: Remove parentheses and compare counts

```sql
SELECT COUNT(*) ...
```

If counts change dramatically, parentheses matter.

---

## A one-line rule to tattoo on your brain

> **If your `WHERE` clause uses both `AND` and `OR`, you should be using parentheses.**

No exceptions.
No shortcuts.
This is professional SQL hygiene.

---

# Truth Table for AND / OR in SQL

A truth table shows **all possible combinations of conditions** and what SQL will return.
Think of it as a **map of SQL’s logic**.

---

## 1️⃣ Basic AND

| Condition A | Condition B | `A AND B` |
| ----------- | ----------- | --------- |
| TRUE        | TRUE        | TRUE      |
| TRUE        | FALSE       | FALSE     |
| FALSE       | TRUE        | FALSE     |
| FALSE       | FALSE       | FALSE     |

> **Rule:** Both conditions must be **TRUE** for the row to survive.

Example:

```sql
WHERE jurisdiction = 'LA' AND outcome = 'Guilty'
```

* Only keeps rows where **both** are true.

---

## 2️⃣ Basic OR

| Condition A | Condition B | `A OR B` |
| ----------- | ----------- | -------- |
| TRUE        | TRUE        | TRUE     |
| TRUE        | FALSE       | TRUE     |
| FALSE       | TRUE        | TRUE     |
| FALSE       | FALSE       | FALSE    |

> **Rule:** At least **one** condition must be TRUE.

Example:

```sql
WHERE outcome = 'Guilty' OR outcome = 'Plea'
```

* Keeps rows with either outcome (or both).

---

## 3️⃣ AND + OR without parentheses

| Condition A | Condition B | Condition C | `A AND B OR C` |
| ----------- | ----------- | ----------- | -------------- |
| TRUE        | TRUE        | TRUE        | TRUE           |
| TRUE        | TRUE        | FALSE       | TRUE           |
| TRUE        | FALSE       | TRUE        | TRUE           |
| TRUE        | FALSE       | FALSE       | FALSE          |
| FALSE       | TRUE        | TRUE        | TRUE           |
| FALSE       | TRUE        | FALSE       | FALSE          |
| FALSE       | FALSE       | TRUE        | TRUE           |
| FALSE       | FALSE       | FALSE       | FALSE          |

> SQL implicitly evaluates `AND` first:
> `A AND B OR C` = `(A AND B) OR C`

---

## 4️⃣ AND + OR with parentheses

| Condition A | Condition B | Condition C | `A AND (B OR C)` |
| ----------- | ----------- | ----------- | ---------------- |
| TRUE        | TRUE        | TRUE        | TRUE             |
| TRUE        | TRUE        | FALSE       | TRUE             |
| TRUE        | FALSE       | TRUE        | TRUE             |
| TRUE        | FALSE       | FALSE       | FALSE            |
| FALSE       | TRUE        | TRUE        | FALSE            |
| FALSE       | TRUE        | FALSE       | FALSE            |
| FALSE       | FALSE       | TRUE        | FALSE            |
| FALSE       | FALSE       | FALSE       | FALSE            |

> Parentheses force SQL to evaluate `(B OR C)` first.
> Only then is `AND A` applied.

---

## 5️⃣ Why this matters

* Misplaced parentheses can **double, halve, or completely change** your dataset.
* Truth tables make it clear how each row is evaluated.
* They are a **debugging tool** before you run queries on large datasets.

---

## 6️⃣ Quick reference “map” for mental sanity

* `AND` = narrow → **both must be true**
* `OR` = widen → **at least one must be true**
* `()` = **override SQL’s default order**

> **Tip:** When in doubt, make a truth table before running a complex WHERE clause.

---

# Comparison Operators in SQL (`WHERE` clause)

Comparison operators are used in the `WHERE` clause to **compare a column’s value against a condition**.
They determine whether a row is kept or filtered out.

---

## Comparison Operator Reference

| Operator | Meaning                  | Typical Use Case      | SQL Example                  |
| -------- | ------------------------ | --------------------- | ---------------------------- |
| `=`      | Equal to                 | Exact match           | `outcome = 'Guilty'`         |
| `!=`     | Not equal to             | Exclude a value       | `outcome != 'Dismissed'`     |
| `<`      | Less than                | Lower bound checks    | `sentence_years < 5`         |
| `<=`     | Less than or equal to    | Inclusive upper limit | `sentence_years <= 10`       |
| `>`      | Greater than             | Threshold filtering   | `sentence_years > 20`        |
| `>=`     | Greater than or equal to | Minimum requirement   | `date_filed >= '2025-01-01'` |

---

## How SQL evaluates comparison operators

For each row:

1. SQL compares the column value to the condition
2. The result is either **TRUE** or **FALSE**
3. Only rows that evaluate to **TRUE** are returned

```sql
WHERE sentence_years >= 10
```

> “Keep rows where the sentence is **at least** 10 years.”

---

## Comparison operators by data type

### 🔢 Numbers

```sql
WHERE sentence_years >= 5
```

### 📅 Dates *(very common in civic-tech analysis)*

```sql
WHERE date_filed < '2025-07-01'
```

### 🔤 Text (exact match only)

```sql
WHERE jurisdiction = 'Los Angeles, CA'
```

> ⚠️ Text comparisons are **case-sensitive** in some databases (SQLite can vary by collation).

---

## What comparison operators **do NOT** do

❌ They do **not**:

* Match partial text → use `LIKE`
* Match multiple values → use `IN`
* Handle missing values → use `IS NULL` / `IS NOT NULL`

Example of a common mistake:

```sql
WHERE summary = '%ICE%'
```

This will **not work**. `%` only works with `LIKE`.

---

## Comparison operators + AND / OR

Comparison operators are almost always used **together**:

```sql
WHERE jurisdiction = 'Los Angeles, CA'
  AND sentence_years >= 10
  AND outcome != 'Dismissed'
```

Think:

> “Give me LA cases with long sentences that weren’t dismissed.”

---

## Important edge case: NULL values

Comparison operators **do not work with NULL**.

❌ This will fail silently:

```sql
WHERE outcome != NULL
```

✅ Correct:

```sql
WHERE outcome IS NOT NULL
```

This distinction is foundational for accurate analysis.

---

## Mental model (plain English)

You can read each comparison operator as a sentence:

* `=` → “is exactly”
* `!=` → “is not”
* `>` → “is more than”
* `>=` → “is at least”
* `<` → “is less than”
* `<=` → “is at most”

If it doesn’t read cleanly in English, it’s probably wrong.

---

## Why this matters for justice analysis

Comparison operators allow you to:

* Identify **sentencing disparities**
* Compare **time periods**
* Filter **outliers**
* Define **thresholds** for review

They are the *measuring sticks* of inequity analysis.

---

# WHERE Clause Failure Modes

The `WHERE` clause filters rows in SQL. When used incorrectly, it can produce **empty results**, **extra rows**, or **unexpected datasets**.

---

## 1️⃣ Forgetting quotes around text

```sql
-- ❌ Incorrect
WHERE jurisdiction = Los Angeles, CA

-- ✅ Correct
WHERE jurisdiction = 'Los Angeles, CA'
```

* Text values **must** be wrapped in single quotes.
* Numbers **do not** need quotes.

---

## 2️⃣ Mixing up `=` vs `LIKE` for partial text

```sql
-- ❌ Won’t match partial text
WHERE summary = '%ICE%'

-- ✅ Correct
WHERE summary LIKE '%ICE%'
```

* `=` is exact match only.
* Use `LIKE` with `%` for partial matches.

---

## 3️⃣ Using `AND` vs `OR` incorrectly

```sql
-- ❌ May return zero rows unintentionally
WHERE jurisdiction = 'LA' AND outcome = 'Dismissed' OR outcome = 'Guilty'

-- ✅ Safer with parentheses
WHERE jurisdiction = 'LA' AND (outcome = 'Dismissed' OR outcome = 'Guilty')
```

* Remember: **AND is evaluated before OR** in SQL.
* Parentheses prevent logic mistakes.

---

## 4️⃣ Ignoring NULL values

```sql
-- ❌ Won’t catch NULLs
WHERE outcome != 'Guilty'

-- ✅ Correct
WHERE outcome IS NOT NULL AND outcome != 'Guilty'
```

* Comparisons with NULL fail.
* Use `IS NULL` or `IS NOT NULL`.

---

## 5️⃣ Overlooking date formats

```sql
-- ❌ Likely fails
WHERE date_filed >= 1-1-2025

-- ✅ Correct
WHERE date_filed >= '2025-01-01'
```

* Always use **YYYY-MM-DD** or the database’s accepted date format.
* Strings for dates should be in single quotes.

---

## 6️⃣ Wrong use of BETWEEN

```sql
-- ❌ Off-by-one or misordered dates
WHERE date_filed BETWEEN '2025-07-31' AND '2025-01-01'

-- ✅ Correct
WHERE date_filed BETWEEN '2025-01-01' AND '2025-07-31'
```

* `BETWEEN … AND …` is **inclusive**.
* Always put the **smaller value first**.

---

## 7️⃣ Forgetting parentheses in complex conditions

```sql
-- ❌ Unexpected results
WHERE jurisdiction = 'LA' OR jurisdiction = 'SF' AND outcome = 'Guilty'

-- ✅ Safer
WHERE (jurisdiction = 'LA' OR jurisdiction = 'SF') AND outcome = 'Guilty'
```

* Without parentheses, SQL evaluates `AND` before `OR`.

---

## 8️⃣ Case sensitivity issues

```sql
-- ❌ Might not match
WHERE jurisdiction = 'los angeles, ca'

-- ✅ Correct
WHERE LOWER(jurisdiction) = 'los angeles, ca'
```

* Some databases treat text comparisons as case-sensitive.
* Use `LOWER()` or `UPPER()` to normalize strings.

---

## 9️⃣ Using the wrong column

* Double-check column names against the table schema.
* Typos in column names will cause **errors** or **empty results**.

---

## 10️⃣ Using too broad or too narrow conditions

```sql
-- ❌ Returns everything
WHERE outcome != 'Guilty'

-- ❌ Returns almost nothing
WHERE sentence_years > 1000

-- ✅ Plan your filters carefully
WHERE outcome IN ('Guilty','Plea') AND sentence_years <= 20
```

* Logical errors can give misleading insights.

---

💡 **Tip for learning:**
Whenever a `WHERE` clause gives the wrong results, **stop and do a row-level truth test** using a truth table for your conditions.
> Rule of Thumb:
- AND narrows, OR widens
- Parentheses clarify intent
- LIKE uses % for fuzzy matching
- NULL must be handled explicitly
- 
---
