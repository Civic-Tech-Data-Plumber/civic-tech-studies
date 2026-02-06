> **Learning Log** - Modulo & Pattern Conditions 
> **Date:** 2026-02-06      
> **Source / Exercise:**  SQLBolt / ChatGPT  
> **Summary:** This page focuses primarily on modulo `%` as a foundational conditional operator, with brief coverage of related numeric patterns.

# Quick Reference
- [The Modulo Operator](#the-modulo-operator "Using % to detect numeric patterns")
- [Even vs Odd](#even-vs-odd "Parity checks with modulo")
- [Common Modulo Patterns](#common-modulo-patterns "Every Nth row, cycles, exclusions")
- [Time-Based Logic](#modulo--time-based-logic "Elections, census cycles")
- [Modulo vs BETWEEN](#modulo-vs-between "Pattern vs range logic")
- [Other Conditional Patterns](#other-logical-condition-patterns-beyond "Related numeric logic")
- [Dialect Differences](#syntax-and-implementation "Modulo across SQL engines")

---

# Logical Conditions & Patterns (SQL WHERE Cheat Sheet)

> **These patterns answer questions like:**
> *even or odd?* · *every Nth record?* · *bucketed logic?* · *rule-based filtering?*

---

## The Modulo Operator

### What `%` means

```sql
a % b
```

➡ Returns the **remainder** when `a` is divided by `b`.

| Expression | Result |
| ---------- | ------ |
| `10 % 2`   | `0`    |
| `11 % 2`   | `1`    |
| `14 % 5`   | `4`    |

---

## Even vs Odd

`year % 2 = 0` 

```sql
year % 2 = 0   -- even
year % 2 = 1   -- odd
```

### Why This Works

* Even numbers divide evenly by 2 → remainder = 0
* Odd numbers leave 1 behind → remainder = 1

### SQLBolt Example (decoded)

```sql
WHERE year % 2 = 0
```

Means:

> “Keep rows where the year divides evenly by 2.”

---

## Common Modulo Patterns

| Goal              | Condition     |
| ----------------- | ------------- |
| Even numbers      | `col % 2 = 0` |
| Odd numbers       | `col % 2 = 1` |
| Every 5 years     | `col % 5 = 0` |
| Every 10th record | `id % 10 = 0` |
| Exclude every 3rd | `id % 3 != 0` |

These show up in:

* Sampling
* Bucketing
* Fairness audits
* Temporal analysis

---

## Modulo + Time-based Logic

```sql
-- Election cycles
year % 4 = 0

-- Decade markers
year % 10 = 0
```

Examples:

* Presidential election years
* Census decades
* Policy review cycles

---

## Modulo vs BETWEEN

| Question                      | Use       |
| ----------------------------- | --------- |
| “Is this within a range?”     | `BETWEEN` |
| “Does this follow a pattern?” | `%`       |


-- ❌ Wrong tool
`WHERE year BETWEEN 2020 AND 2024`

-- ✅ Pattern logic
`WHERE year % 2 = 0`


---

## Other Logical Condition Patterns

### A. Divisibility

```sql
WHERE sentence_years % 5 = 0
```

> “Sentences that are exact multiples of 5 years.”

### B. Bucketing With Integer Division

```sql
sentence_years / 10 AS decade_bucket
```
> In some DBs, this produces decimal results unless cast.

Optimal Example:

```sql
CAST(sentence_years / 10 AS INT) AS decade_bucket
```
> Behavior depends on integer vs decimal division in your DBMS.

Groups values into ranges:

* 0–9
* 10–19
* 20–29

### C. Parity Checks

```sql
(id + year) % 2 = 0
```

Used for:

* Alternating assignment
* Load balancing
* Randomization without randomness

---

## Expressions Inside WHERE

If your WHERE clause:

* **Asks “does this divide evenly?”** → use `%`
* **Asks “is this in a range?”** → use `BETWEEN`
* **Asks “is this exact?”** → use `=`
* **Asks “does this contain text?”** → use `LIKE`

---

## Professional Caution ⚠️

Modulo:

* Only works on **numbers**
* Can be slower on massive datasets
* Is logic-heavy — document intent

Best practice:

```sql
-- Select even-year releases
WHERE year % 2 = 0
```

That comment matters in real analysis.

---

## Mental Model

> **Modulo answers pattern questions, not magnitude questions.**

If you’re asking:

* *Every N?*
* *Even / odd?*
* *Exact cycle?*

You’re in modulo territory.

---

## One-line Translation (plain English)

```sql
WHERE year % 2 = 0
```

> “Only keep rows where the year divides evenly by 2.”

That’s it. That’s the logic.

---

In SQL, the modulo operation calculates the remainder after dividing one number (the dividend) by another (the divisor).  
The syntax for this operation varies by the specific SQL database system, using either the MOD() function or the % operator. 

## Syntax and Implementation

> Most SQL dialects support one of the following methods:

| SQL Dialect                   | Syntax Example                                           |
| ----------------------------- | -------------------------------------------------------- |
| SQL Server (T-SQL)            | SELECT 38 % 5 AS Remainder;                              |
| MySQL                         | SELECT MOD(38, 5); or SELECT 38 % 5; or SELECT 38 MOD 5; |
| PostgreSQL                    | SELECT MOD(38, 5); or SELECT 38 % 5;                     |
| Oracle                        | SELECT MOD(38, 5) FROM DUAL;                             |
| DB2                           | SELECT MOD(38, 5);                                       |
