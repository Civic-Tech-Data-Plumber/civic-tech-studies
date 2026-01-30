> **Learning Log** - Intro to SQL 
> **Date:** 2026-01-30   
> **Source / Exercise:** SQLBolt / ChatGPT  
> **Summary:** What SQL is, programs that use it, and how SQL pulls, organizes and edits data.

### Quick Reference

- [What's SQL?](#what-is-sql "Basic description of what SQL is")
- [What Are Queries?](#what-are-queries "Basic description of what a query is in SQL")
- [Query Syntax](#query-syntax "Why order of operations matter")
- [Order of Operations](#order-of-operations "Logical order of operations performed by SQL databases")
  - [Visual Analogy](#visual-analogy "Easy way to think of SQL's order of operations")
  - [Quick Guide Table](#quick-guide-table "Table provided for quick reference for the order of operations")
- [Example](#example "An example SQL script written in Python")
- [Key Terms](#key-terms "a short list of the terms used to describe the code presented in this log)

---

# What is SQL 

SQL means Structured Query Language. 
It is a standardized language with a defined syntax used to command databases and retrieve specific datasets.   
Some popular SQL databases include:

- SQLite
- MySQL
- Postgres
- Oracle
- and Microsoft SQL Server
   
*All of them support the common SQL language syntax.*

## What Are Queries 

A **query** is a request you make to a database to retrieve, manipulate, or summarize data. In SQL, queries are written as statements that the database can understand, letting you ask questions like “Which movies earned more internationally than domestically?” or “How many users signed up last month?” Think of queries as coded questions: you define what you want, and the database returns the answers.

## Query Syntax 
Every language has an order of operations. "I fed the dog" doesn't mean the same as "The dog fed me." 
So, when starting out it is important to know that there is a specific set of syntax rules one should follow in order to get the desired results.
For example:

```sql
SELECT column1, column2, column3
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

---

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
  2. `JOIN` other_table → Pour in apples from another basket that match certain rules
  3. `WHERE` condition → Only pick the red apples
  4. `GROUP` BY column → Group apples by size
  5. `HAVING` condition → Only keep groups with more than 3 apples
  6. `SELECT` column → Note apple details (color, size, weight)
  7. `ORDER` BY column → Arrange apples by size

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

  📌 **QUICK TIP**
  > JOIN decides what data exists; WHERE decides what data survives.

  ### Conceptual Flow

  It's helpful to think of data scripts (often written in Python) like this:

  **IMPORT** tools  
  **DEFINE** reusable functions  
  **CONNECT** to data  
  **QUERY** data  
  **LOOP** through results  
  **RETURN** or **SAVE** output  
  **CLOSE** resources   

---

## Example 

*An example script:*
> This example shows how SQL queries are commonly embedded inside a Python script to retrieve and analyze data from a database.

```python
"""
ice_violence_queries.py

Helper functions for querying CourtListener-style data
related to ICE violence in Los Angeles, CA.

Date range: January 2025 – July 2025
"""

import sqlite3


def connect_db(db_path="courtlistener.db"):
    """
    Connect to the SQLite database.

    Returns:
        sqlite3.Connection
    """
    return sqlite3.connect(db_path)


def fetch_ice_violence_cases(conn):
    """
    Fetch court cases from Los Angeles, CA that reference ICE-related violence
    between January and July 2025.

    Returns:
        list of tuples
    """
    query = """
    SELECT
        case_id,
        case_name,
        court_name,
        jurisdiction,
        date_filed,
        outcome,
        summary
    FROM court_cases
    WHERE jurisdiction = 'Los Angeles, CA'
      AND date_filed BETWEEN '2025-01-01' AND '2025-07-31'
      AND (
            summary LIKE '%ICE%'
         OR summary LIKE '%Immigration and Customs Enforcement%'
         OR summary LIKE '%detention%'
         OR summary LIKE '%use of force%'
      );
    """

    cursor = conn.cursor()    # Create a cursor object to execute SQL
    cursor.execute(query)     # Run the query against the database
    return cursor.fetchall()  # Return all results as a list of tuples


if __name__ == "__main__":
    conn = connect_db()                       # Connect to the database
    cases = fetch_ice_violence_cases(conn)    # Fetch filtered court cases
    for case in cases:
        print(case)
    conn.close()
```

🔗 This script is demystified at [logs/example-python-script.md](example-python-script.md "Decoding this script with examples, definitions and explanations.") if you want a deep dive into what the script is saying and doing.

---

## Key Terms

In SQL, `SELECT` is officially called a **keyword**. Sometimes people also refer to it as a **clause** (especially when talking about the part of a query that starts with `SELECT` and defines what columns or expressions to return).

| Term          | Example                                   | What it means                                                                                   |
| ------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------- |
| **Keyword**   | `SELECT`, `FROM`, `WHERE`                 | Reserved words in SQL that have a special meaning. They tell the database what to do.           |
| **Clause**    | `SELECT column1, column2 FROM table_name` | A part of a SQL statement. A clause often starts with a keyword like `SELECT`, `FROM`, `WHERE`. |
| **Statement** | `SELECT * FROM movies WHERE rating > 8;`  | A complete instruction to the database. A statement can contain multiple clauses.               |

✅ So in short:

* `SELECT` → **keyword**
* The part `SELECT column1, column2` → **SELECT clause**
* The whole query with `SELECT ... FROM ... WHERE ...` → **SQL statement**

🔗 A more comprehensive list of terminology can be found in the [Glossary](glossary.md "Glossary page").
