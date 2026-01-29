> Learning Log Day 8  
> Learning how to remove duplicates and when it's appropriate to do so.

# `DISTINCT` in SQL

**What it does, what it doesn’t do, and when to use it**

---

## 1️⃣ The core idea (memorize this)

> **`DISTINCT` removes duplicate rows from the result set — not from the table.**

It does **not**:

* Change data
* “Group” data
* Pick one row per entity intelligently

It only affects **what is returned** by the `SELECT`.

---

## 2️⃣ Why duplicates happen in the first place

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

## 3️⃣ What DISTINCT does

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

---

## 4️⃣ DISTINCT works on the entire selected row

This is the most common confusion.

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

## 5️⃣ DISTINCT vs GROUP BY (very important)

### DISTINCT (quick deduplication)

```sql
SELECT DISTINCT jurisdiction
FROM cases;
```

Use when:

* You just want unique values
* No calculations needed

---

### GROUP BY (aggregation & analysis)

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

## 6️⃣ DISTINCT with JOINs (real-world important)

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

Sometimes `GROUP BY` is the better fix.

---

## 7️⃣ DISTINCT with WHERE (very common)

```sql
SELECT DISTINCT jurisdiction
FROM cases
WHERE outcome = 'Guilty';
```

Meaning:

> “Show me all jurisdictions that have **at least one guilty case**.”

This is a powerful pattern in civic-tech analysis.

---

## 8️⃣ DISTINCT does NOT choose “the best row”

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

---

## 9️⃣ Performance note (professional awareness)

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

## 🧑‍⚖️ Civic-tech examples

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

## 📌 One-sentence rule to remember forever

> **If you’re asking “what unique values exist?”, use `DISTINCT`.
> If you’re asking “how many / how much / how often?”, use `GROUP BY`.**
* Compare `DISTINCT` vs `GROUP BY` side-by-side with visuals
* Talk about when DISTINCT is a **code smell**

Just tell me how you want to proceed.
