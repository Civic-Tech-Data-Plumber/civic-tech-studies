> **Learning Log** - Example Python Script
> **Date:** 2026-01-30   
> **Source / Exercise:** ChatGPT  
> **Summary** Defining and understanding each part of a sample SQL script written in Python.

### Quick Reference
- [Example Python Script](#example-python-script "An example Python script used to reference when trying to understand what the does")
- [Demystifying Triple Quotes](#demystifying-triple-quotes "Understanding and demystifying triple quotes")
  - [What Are Docstrings](#what-are-docstrings "What docstrings are actually for")
  - [Docstrings ≠ Comments](#docstrings-are-not-comment-blocks "Docstrings Are NOT Comment Blocks")
  - [Why Use Docstrings](#why-use-docstrings "Why using docstrings is a great idea")
- [Import statement](#import-sqlite3 "What import does and why")
- [What `def` means](#what-def-means "How giving a function a nickname can save you time")
- [Functions](#functions "In depth explaination of a function")
  - [Example Function](#example-function "Brief snippet of a function")
- [Cursors](#cursors "An exmplantion of cursors and their spot in our example code")  
  - [`conn.cursor`](#conncursor "Creating a cursor object to execute SQL")
  - [`cursor.execute(query)`](#cursorexecute "Run the query against the database")
  - [`return cursor.fetchall()`](#cursorfetchall "Return all results as a list of tuples")
-[Decision Making](#decision-making "Useful for: Filtering, Validation and Conditional logic.") 
  - [`If`, `elif`, `else`](#if-elif-else "Useful for: Filtering, Validation and Conditional logic.")
  - [`For` and `In`](#for-and-in "For each one of these in all of this category/column...")
- [Closing the database connection](#conn-close "Terminating the connection created by `connect_db()`")

# Example Python Script
We will use the following script to highlight key components and define their use to help the budding data engineer understand the core concepts of the scripting language.

*Our example script for the day:*

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


    cursor = conn.cursor()      # Create a cursor object to execute SQL
    cursor.execute(query)       # Run the query against the database
    return cursor.fetchall()    # Return all results as a list of tuples


if __name__ == "__main__":
    conn = connect_db()                         # Connect to the database
    cases = fetch_ice_violence_cases(conn)      # Fetch filtered court cases
    for case in cases:
        print(case)

    conn.close()
```

> This example shows how SQL queries are commonly embedded inside a Python script to retrieve and analyze data from a database.

---

## Demystifying Triple Quotes

The first thing we see in this sample is `"""`

This is a `docstring`.  
A docstring:
- Is stored in memory
- Can be accessed programmatically
- Is used by help tools and documentation generators

### What Are Docstrings
Docstrings are structured documentation, not comments.

They are used to:

- Explain what a module, function, or class does
- Describe parameters and return values
- Generate documentation automatically

### Docstrings Are NOT Comment Blocks

Best practices for data engineers wanting to create a comment is to use the `#` symbol (per line).  
Use docstrings (`"""`) for documentation.

#### Beginner Mistakes:
❌ Docstrings are not used to comment out blocks of code:
```python
"""
conn = connect_db()
run_query(conn)
"""
```

Python will still **create a string object,** which is wasteful and confusing.  
*Reminder:* ❌ Do not use `"""` to comment out blocks of code, use `#` instead.

### Why Use Docstrings

- They explain intent, not just mechanics
- They signal seriousness and clarity
- They scale well as scripts grow
- They make our code self-documenting

---

## `import` sqlite3
`import` is a Python statement that lets you use code from another module or library.  
    *A "module" is just a Python file (or collection of files) that provides functions, classes, or variables.*  
By importing sqlite3, we call its functions into our script instead of rewriting them from scratch.

### Why we specifically imported `sqlite3`
We are working with a SQLite database called `courtlistener.db`.  
Python does not automatically know how to talk to databases.  
Each database type has its own API (Application Programming Interface).

`sqlite3` = Python’s built-in library for SQLite databases  
It provides functions to:

- Connect to the database
- Run SQL queries (`SELECT`, `INSERT`, `UPDATE`, `DELETE`)
- Fetch results
- Handle transactions
- Close the connection safely

Without importing it, Python would throw an error if you tried:
```python
conn = sqlite3.connect("courtlistener.db")
```
Because `sqlite3` wouldn’t have been defined.

### How it fits into our script

```python
import sqlite3

def connect_db(db_path="courtlistener.db"):
    return sqlite3.connect(db_path)
```
- `sqlite3.connect(db_path)` = creates a connection object to the SQLite database
- We store it in conn and use it to run queries later.
- All SQL queries are executed through the conn object.

---

## What `def` means

`def` = define
> I am defining a function with this name, these parameters, and this block of code.  
> Whenever I call this function, run this code.

```python
def connect_db(db_path="courtlistener.db")
```
Define a reusable block of code called `connect_db`.

### Breaking it down

| Part                              | Meaning                                                              |
| --------------------------------- | -------------------------------------------------------------------- |
| `def`                             | Introduces a new function                                            |
| `connect_db`                      | The **name** of the function — like an identifier you can call later |
| `(db_path="courtlistener.db")`    | **Parameter(s)** — placeholders for inputs; can have defaults        |
| `:`                               | Syntax marker that the indented code below belongs to the function   |
| `return sqlite3.connect(db_path)` | The **output** the function gives back when called                   |


### In English, Please!
- `def` = define
- `connect_db` = function name
- Everything indented underneath belongs to that function
- The function does nothing until it is called

📌 *Use `def` to nickname a function (a specified query-code) so you can later call upon the nickname instead of rewriting the entire code again.*

---

## Functions

**What are Functions?**
Functions are used constantly by data engineers because they let you:

- Avoid repeating yourself
- Make pipelines readable
- Isolate failures
- Reuse logic across scripts
- Test parts independently

In essence, for data plumbers, *functions are essentially the pipes*.  
They are a reusable tool, or pipe, from which to define the parameters of our data query.

*Example of a function:*

```python
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
```

📌 **Here's a brief overview of common SQL functions:*
- **Aggregate Functions:** These operate on a set of values, returning a single result. Examples include COUNT() (counts rows), SUM() (sums numeric values), AVG() (calculates average), and MIN()/MAX() (finds minimum/maximum value).
- **String Functions:** Used for manipulating text data. Common ones are CONCAT() (joins strings), LENGTH()/LEN() (gets string length), UPPER()/LOWER() (changes case), SUBSTRING() (extracts part of a string), and REPLACE() (substitutes a substring).
- **Numeric Functions:** Perform mathematical tasks. Examples include ROUND() (rounds numbers), FLOOR()/CEILING() (gets integer parts), and ABS() (returns absolute val- ue).
- **Date Functions:** Work with date and time. Useful functions include GETDATE()/CURRENT_TIMESTAMP() (gets current date/time), YEAR()/MONTH()/DAY() (extracts parts of a date), DATEADD() (adds time), and DATEDIFF() (finds date difference).

Your Functions rest between the opening `SELECT` keyword and the closing `;` tag.

#### How Terms Are Used in Functions
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

---

## Cursors 
**Cursor** – A control object used to interact with a database connection. The cursor is used to send SQL statements to the database and retrieve results. 

```python
    cursor = conn.cursor()    # Create a cursor object to execute SQL
    cursor.execute(query)     # Run the query against the database
    return cursor.fetchall()  # Return all results as a list of tuples
```

### `conn.cursor()`

* `conn` is your **database connection object**.  
It represents the live connection to your database (`courtlistener.db` in your example).  
* `.cursor()` is a method on that connection object.

**Think of a cursor like a “pointer” or “controller” that lets you navigate through your database**. You don’t interact with the database directly through the connection object — the cursor is the tool you use to send commands and retrieve results.

💡 Analogy: If the database is a library, `conn` is the library building, and the `cursor` is the librarian who fetches the books (data) you request.

---

### `cursor.execute(query)`

* `.execute()` tells the cursor to **run a SQL statement**.
* In your case, the `query` variable contains your SQL `SELECT ... FROM ... WHERE ...` string.
* When you call `execute()`, the SQL command is sent to the database, and the results are ready to be fetched.

💡 Important: At this stage, the results aren’t yet in your Python variable. They’re sitting in the database memory, ready to be “collected” by the cursor.

---

### `cursor.fetchall()`

* `.fetchall()` asks the cursor: **“Give me all the results of the query as a list of tuples.”**
* Each row in the database becomes a tuple in Python, with each column as an element in the tuple.
* This is what gets returned by your function so you can iterate over it or manipulate it in Python.

💡 Analogy: Continuing the library example: the librarian (cursor) went and pulled all the books (rows) that match your request, then handed them to you in a neat stack (list of tuples).

---

## Decision Making

```python
if __name__ == "__main__":
    conn = connect_db()
    cases = fetch_ice_violence_cases(conn)
```

What `__name__` means

Every Python file has a built-in variable called __name__.  
If the file is run directly, Python sets:

```python
  __name__ = "__main__"
```

If the file is imported by another file, Python sets:

  ```python
__name__ = "ice_violence_queries"
```

> 📆 More details covering this topic and why it is in this code example will be added at a later date

### `if`, `elif`, `else`

```python
if outcome == "Guilty":
    flag = True
else:
    flag = False
```
Useful for: Filtering, Validation and Conditional logic.

### `for` and `in` 

```python
    for case in cases:
        print(case)
```
The `for` statement in Python is used for iterating over a sequence (such as a list, tuple, string, or dictionary) or other iterable objects, executing a block of code for each item.

Example:

```python
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(f"I like this selected {fruit}")
```

**`for` in plain English:**
*"For each one of these in all of this category/column..."*

```python
for variable in iterable:
    # Code block to be executed
    # with the current 'variable' value
```

--- 

## `conn.close()` 

**closes the database connection that was created earlier by `connect_db()`** and then *passed into* `fetch_ice_violence_cases(conn)`.
> 📝 `conn.close()` closes the database connection that was originally opened by `connect_db()`, signaling that no more queries will be run and freeing system resources.

### Mental model (very useful)

Think of it like a phone call:

1. `connect_db()` → *dial the number*
2. `fetch_ice_violence_cases(conn)` → *have the conversation*
3. `conn.close()` → *hang up the phone*

You don’t hang up *inside* the conversation function — you hang up **after you’re done using the line**.


### Slightly longer (but still clean) explanation

```python
def connect_db(db_path="courtlistener.db"):
    return sqlite3.connect(db_path)
```

* This function **creates** a database connection.
* The object it returns is stored in `conn`:

```python
conn = connect_db()
```

That same `conn` is then **handed to** your query function:

```python
cases = fetch_ice_violence_cases(conn)
```

Inside:

```python
def fetch_ice_violence_cases(conn):
```

* `conn` is **not created here**
* It’s **received** as an argument so the function can run queries using an already-open connection

Finally:

```python
conn.close()
```

* This **terminates the connection created by `connect_db()`**
* It frees system resources and properly ends communication with the database
