> Learning log - Day 4b 
> `WHERE` is not about syntax — it’s about intent.

# `WHERE` Clauses and Conditional Logic
**One-Sentence Mental Model (Memorize This)**  
*Decide what kind of data, how precise, and what to include or exclude, then choose the operator.*

---

## **Part 1: Filtering with `WHERE` Clauses**

> **Mental Model:** Decide *what kind of data*, *how precise*, and *what to include or exclude* before picking your operator.

### Key SQL Operators

| Operator / Keyword | Condition Description                    | SQL Example                                            |
| ------------------ | ---------------------------------------- | ------------------------------------------------------ |
| `=`                | Equal to                                 | `outcome = 'Guilty'`                                   |
| `!=`               | Not equal to                             | `outcome != 'Dismissed'`                               |
| `<`, `<=`          | Less than / At most                      | `sentence_years <= 10`                                 |
| `>`, `>=`          | Greater than / At least                  | `sentence_years >= 5`                                  |
| `BETWEEN … AND …`  | Value is within a range (inclusive)      | `date_filed BETWEEN '2025-01-01' AND '2025-07-01'`     |
| `NOT BETWEEN …`    | Value is outside a range                 | `date_filed NOT BETWEEN '2025-01-01' AND '2025-07-01'` |
| `IN (…)`           | Value exists in a specified list         | `outcome IN ('Guilty','Plea')`                         |
| `NOT IN (…)`       | Value does not exist in a specified list | `outcome NOT IN ('Dismissed')`                         |
| `LIKE`             | Text matches a pattern using wildcards   | `summary LIKE '%ICE%'`                                 |
| `NOT LIKE`         | Text does not match a pattern            | `summary NOT LIKE '%detention%'`                       |

**Notes:**

* `BETWEEN` is inclusive.
* `IN` / `NOT IN` are often cleaner than multiple `OR` conditions.
* `LIKE` uses `%` for any number of characters, `_` for exactly one.

---

## **Part 2: Combining Conditions – `AND`, `OR`, and Parentheses**

### Core Rules

1. `AND` → all conditions must be true → **narrows results**
2. `OR` → at least one condition must be true → **widens results**
3. SQL evaluates **AND before OR**. Parentheses are critical when mixing both.

### Examples

**Without parentheses (dangerous):**

```sql
WHERE jurisdiction = 'CA'
  AND summary LIKE '%ICE%'
   OR summary LIKE '%detention%'
```

*SQL reads this as:*

```sql
(jurisdiction = 'CA' AND summary LIKE '%ICE%') OR summary LIKE '%detention%'
```

> Returns detention cases anywhere, not just in CA.

**With parentheses (explicit & safe):**

```sql
WHERE jurisdiction = 'CA'
  AND (
        summary LIKE '%ICE%'
     OR summary LIKE '%detention%'
  )
```

> Only keeps CA cases mentioning ICE or detention.

---

### Truth Table Visualization

**AND**

| A     | B     | A AND B |
| ----- | ----- | ------- |
| TRUE  | TRUE  | TRUE    |
| TRUE  | FALSE | FALSE   |
| FALSE | TRUE  | FALSE   |
| FALSE | FALSE | FALSE   |

**OR**

| A     | B     | A OR B |
| ----- | ----- | ------ |
| TRUE  | TRUE  | TRUE   |
| TRUE  | FALSE | TRUE   |
| FALSE | TRUE  | TRUE   |
| FALSE | FALSE | FALSE  |

**AND + OR with parentheses**

| A     | B     | C     | A AND (B OR C) |
| ----- | ----- | ----- | -------------- |
| TRUE  | TRUE  | TRUE  | TRUE           |
| TRUE  | TRUE  | FALSE | TRUE           |
| TRUE  | FALSE | TRUE  | TRUE           |
| TRUE  | FALSE | FALSE | FALSE          |
| FALSE | TRUE  | TRUE  | FALSE          |
| FALSE | TRUE  | FALSE | FALSE          |
| FALSE | FALSE | TRUE  | FALSE          |
| FALSE | FALSE | FALSE | FALSE          |

> Parentheses make SQL logic explicit and protect your dataset.

---

## **Part 3: WHERE Clause Failure Modes and Best Practices**

### Common Mistakes

1. **Forgetting quotes around text**

```sql
-- ❌
WHERE jurisdiction = Los Angeles, CA
-- ✅
WHERE jurisdiction = 'Los Angeles, CA'
```

2. **Using `=` instead of `LIKE` for partial text**

```sql
-- ❌
WHERE summary = '%ICE%'
-- ✅
WHERE summary LIKE '%ICE%'
```

3. **Ignoring NULL values**

```sql
-- ❌
WHERE outcome != 'Guilty'
-- ✅
WHERE outcome IS NOT NULL AND outcome != 'Guilty'
```

4. **Misordered BETWEEN**

```sql
-- ❌
WHERE date_filed BETWEEN '2025-07-31' AND '2025-01-01'
-- ✅
WHERE date_filed BETWEEN '2025-01-01' AND '2025-07-31'
```

5. **Mixing AND / OR without parentheses** → often doubles or halves results unintentionally

6. **Case sensitivity issues**

```sql
WHERE LOWER(jurisdiction) = 'los angeles, ca'
```

### Professional Tip

> When results look wrong, do a **row-level truth test** using a truth table before running a full query.

---

✅ **End-of-Day Mental Model**

* Numbers / Dates → `=, <, >, BETWEEN`
* Categories → `IN`, `NOT IN`
* Text → `LIKE`, `NOT LIKE`
* Boolean → `= TRUE / FALSE`
* Missing → `IS NULL / IS NOT NULL`

> Always sanity-check results and use parentheses when combining AND + OR. This is professional SQL hygiene.
