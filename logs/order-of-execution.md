> **Learning Log** - SQL's Order of Execution 
> **Date:** YYYY-MM-DD   
> **Source / Exercise:** SQLBolt
> **Goal of today's study:** Brief example of the logical order of execution and syntax structure of SQL query coding practices.

# Quick Reference
- [Query Syntax](#query-syntax)
- [Order of Execution](#order-of-execution)
- [Quick View](#quick-view)
---

# Query Syntax
Every language has an order of operations. "I fed the dog" doesn't mean the same as "The dog fed me." 
So, when starting out it is important to know that there is a specific set of syntax rules one should follow in order to get the desired results.
For example:

```sql
SELECT column, another_column, column3
FROM mytable
WHERE condition;
```
- `SELECT` column1, column2 → tells SQL which columns to return
- `FROM` table_name → tells SQL which table to look in
- `WHERE` condition → filters rows so only those matching the condition appear

Example: 
```sql
SELECT race, AVG(sentence_years) AS avg_sentence
FROM court_cases
GROUP BY race
ORDER BY avg_sentence DESC;
```

**Note:** SQL is case-insensitive for keywords (e.g., SELECT = select), but table and column names can be case-sensitive depending on the database system.

## Order of Execution

**The logical execution order of SQL is:**

1. `FROM` → SQL identifies which table(s) to use.
2. `JOIN` (if any) → combines tables if multiple tables are involved.
3. `WHERE` → filters rows based on your conditions.
4. `GROUP BY` → groups rows if aggregation is needed.
5. `HAVING` → filters groups (not individual rows).
6. `SELECT` → chooses which columns to return.
7. `ORDER BY` → sorts the final results.

**Key point:** `WHERE` comes after `FROM` because you need to know which rows exist in the table before you can filter them.

## Visual Analogy

**Picking Apples from a Basket**

1. `FROM` table_name → Pick your basket of apples
2. `WHERE` condition → Only pick the red apples
3. `GROUP` BY column → Group apples by size
4. `HAVING` condition → Only keep groups with more than 3 apples
5. `SELECT` column → Note apple details (color, size, weight)
6. `ORDER` BY column → Arrange apples by size

**Important Note:** `SELECT` is last logically, even though it’s written first.

## Quick View

**Another way to put it: SQL actually processes it in this logical order**

| Step | Clause   | What Happens                                     |
| ---- | -------- | ------------------------------------------------ |
| 1    | FROM     | Identify which table(s) to use                   |
| 2    | JOIN     | Combine rows from multiple tables (if any)       |
| 3    | WHERE    | Filter rows based on conditions                  |
| 4    | GROUP BY | Group filtered rows for aggregation              |
| 5    | HAVING   | Filter groups after aggregation                  |
| 6    | SELECT   | Choose which columns (or calculations) to return |
| 7    | ORDER BY | Sort the final results                           |
