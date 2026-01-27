# **SQL WHERE Clause Cheat Sheet**

> Quick reference for filtering, combining conditions, and avoiding common mistakes.

---

## **1️⃣ Comparison Operators**

| Operator | Meaning               | Example                      |
| -------- | --------------------- | ---------------------------- |
| `=`      | Equal to              | `outcome = 'Guilty'`         |
| `!=`     | Not equal to          | `outcome != 'Dismissed'`     |
| `<`      | Less than             | `sentence_years < 5`         |
| `<=`     | Less than or equal    | `sentence_years <= 10`       |
| `>`      | Greater than          | `sentence_years > 20`        |
| `>=`     | Greater than or equal | `date_filed >= '2025-01-01'` |

**Tip:** Works for numbers, dates, and exact text matches. Partial text → use `LIKE`.

---

## **2️⃣ Range & Set Operators**

| Operator          | Description                 | Example                                            |
| ----------------- | --------------------------- | -------------------------------------------------- |
| `BETWEEN … AND …` | Inclusive range             | `date_filed BETWEEN '2025-01-01' AND '2025-07-01'` |
| `NOT BETWEEN …`   | Outside the range           | `sentence_years NOT BETWEEN 1 AND 5`               |
| `IN (…)`          | Matches any value in a list | `outcome IN ('Guilty','Plea')`                     |
| `NOT IN (…)`      | Excludes values in a list   | `outcome NOT IN ('Dismissed')`                     |

---

## **3️⃣ Pattern Matching: LIKE / NOT LIKE**

| Operator   | Description                  | Example                          |
| ---------- | ---------------------------- | -------------------------------- |
| `LIKE`     | Matches text using `%` / `_` | `summary LIKE '%ICE%'`           |
| `NOT LIKE` | Excludes text pattern        | `summary NOT LIKE '%detention%'` |

**Wildcards:** `%` = any number of characters, `_` = exactly one character

---

## **4️⃣ Combining Conditions: AND / OR**

| Operator | Meaning                             | Effect on result set |
| -------- | ----------------------------------- | -------------------- |
| `AND`    | All conditions must be TRUE         | Narrows results      |
| `OR`     | At least one condition must be TRUE | Widens results       |
| `()`     | Override SQL default precedence     | Makes logic explicit |

**Important:** SQL evaluates `AND` before `OR`. Use parentheses when mixing both.

### Example

```sql
-- Correct: only CA cases mentioning ICE or detention
WHERE jurisdiction = 'CA'
  AND (summary LIKE '%ICE%' OR summary LIKE '%detention%')
```

---

## **5️⃣ NULL Handling**

| Goal              | Example                     |
| ----------------- | --------------------------- |
| Check for missing | `WHERE outcome IS NULL`     |
| Exclude missing   | `WHERE outcome IS NOT NULL` |

> Comparisons like `!= NULL` **do not work**.

---

## **6️⃣ Common WHERE Clause Pitfalls**

| Mistake                              | Example                                 | Fix                                     |
| ------------------------------------ | --------------------------------------- | --------------------------------------- |
| Forget quotes for text               | `WHERE jurisdiction = LA`               | `WHERE jurisdiction = 'LA'`             |
| Using `=` instead of `LIKE` for text | `WHERE summary = '%ICE%'`               | `WHERE summary LIKE '%ICE%'`            |
| Misordered BETWEEN                   | `BETWEEN '2025-07-31' AND '2025-01-01'` | `BETWEEN '2025-01-01' AND '2025-07-31'` |
| Mixing AND / OR without parentheses  | `A AND B OR C`                          | `(A AND B) OR C`                        |
| Case sensitivity issues              | `WHERE jurisdiction = 'la'`             | `WHERE LOWER(jurisdiction) = 'la'`      |

---

## **7️⃣ Quick Mental Models**

* Numbers / Dates → `=, <, >, BETWEEN`
* Categories → `IN`, `NOT IN`
* Text → `LIKE`, `NOT LIKE`
* Boolean → `= TRUE / FALSE`
* Missing → `IS NULL / IS NOT NULL`

> **Rule of Thumb:** If using `AND + OR` → **use parentheses**.

---

## **8️⃣ Example – Real Civic-Tech Query**

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

✅ Combines: exact filtering, range filtering, NULL check, fuzzy text matching.
