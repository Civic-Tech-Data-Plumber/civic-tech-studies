> **Learning Log** - Intro to SQL 
> **Date:** 2026-01-30   
> **Source / Exercise:** SQLBolt / ChatGPT  
> **Goal of today's study:**  
> What SQL is, programs that use it, and how SQL pulls, organizes and edits data

# Quick Reference
- [Glossary](#glossary "Definitions of common SQL terms")
- [What's SQL?](#what-is-sql "Basic description of what SQL is")
- [What Are Queries?](#what-are-queries "Basic description of what a query is in SQL")
- [Query Syntax](#query-syntax "Why order of operations matter")
- [Order of Operations](#order-of-operations "Logical order of operations performed by SQL databases")
  - [Visual Analogy](#visual-analogy "Easy way to think of SQL's order of operations")
  - [Quick Guide Table](#quick-guide-table "Table provided for quick reference for the order of operations")

---

## Glossary 

* **Query** – A request to a database to retrieve or manipulate data
* **Expression** – A calculation or operation used to transform or evaluate data in a query
* **Modulo** – The remainder of a division operation (`5 % 2 = 1`)
* **Schema** – The structure of a database, including tables, columns, and relationships
* **Condition** – A logical statement that filters or controls data returned by a query
* **Concatenation** – Combining two or more strings into one
* **Aggregate** – A function that summarizes data across multiple rows (e.g., `SUM`, `AVG`, `COUNT`)
  
---

## What is SQL 

SQL means Structured Query Language. 
It is a standardized language with a defined syntax used to command databases and retrieve specific datasets. 
Some popular SQL databases include:

- SQLite
- MySQL
- Postgres
- Oracle
- and Microsoft SQL Server
All of them support the common SQL language syntax.

## What Are Queries 

A **query** is a request you make to a database to retrieve, manipulate, or summarize data. In SQL, queries are written as statements that the database can understand, letting you ask questions like “Which movies earned more internationally than domestically?” or “How many users signed up last month?” Think of queries as coded questions: you define what you want, and the database returns the answers.

## Query Syntax 
Every language has an order of operations. "I fed the dog" doesn't mean the same as "The dog fed me." 
So, when starting out it is important to know that there is a specific set of syntax rules one should follow in order to get the desired results.
For example:

```sql
SELECT column, another_column, column3
FROM mytable
WHERE condition;
```
- `SELECT column1, column2` → tells SQL which columns to return
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

## Order of Operations 

The logical execution order of SQL is:
1. `FROM` → SQL identifies which table(s) to use.
2. `JOIN` (if any) → combines tables if multiple tables are involved.
3. `WHERE` → filters rows based on your conditions.
4. `GROUP BY` → groups rows if aggregation is needed.
5. `HAVING` → filters groups (not individual rows).
6. `SELECT` → chooses which columns to return.
7. `ORDER BY` → sorts the final results.

**Key point:** `WHERE` comes after `FROM` because you need to know which rows exist in the table before you can filter them.

  ### Visual Analogy 

  **Picking Apples from a Basket**

  1. `FROM table_name` → Pick your basket of apples
  2. `WHERE` condition → Only pick the red apples
  3. `GROUP` BY column → Group apples by size
  4. `HAVING` condition → Only keep groups with more than 3 apples
  5. `SELECT` column → Note apple details (color, size, weight)
  6. `ORDER` BY column → Arrange apples by size

**Important Note:** `SELECT` is last logically, even though it’s written first.

### Quick Guide Table 

| Step | Clause   | What Happens                                     |
| ---- | -------- | ------------------------------------------------ |
| 1    | `FROM`     | Identify which table(s) to use                   |
| 2    | `JOIN`     | Combine rows from multiple tables (if any)       |
| 3    | `WHERE`    | Filter rows based on conditions                  |
| 4    | `GROUP BY` | Group filtered rows for aggregation              |
| 5    | `HAVING`   | Filter groups after aggregation                  |
| 6    | `SELECT`   | Choose which columns (or calculations) to return |
| 7    | `ORDER BY` | Sort the final results                           |
