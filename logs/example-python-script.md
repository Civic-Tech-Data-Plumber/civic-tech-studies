> **Learning Log** - Example Python Script
> **Date:** 2026-01-30   
> **Source / Exercise:** ChatGPT  
> **Summary:** Defining and understanding each part of a sample SQL script written in Python.

### Quick Reference
- [Example Python Script](#example-python-script "An example Python script used to reference when trying to understand what the does")
- [Demystifying Triple Quotes](#demystifying-triple-quotes-""" "Understanding and demystifying triple quotes")
  - [Docstrings (What They Are)](#docstrings-what-they-are "What docstrings are for")
  - [Docstrings Are Not Comments](#docstrings-are-not-comments "Why docstrings are not used for commenting out code")
- [`query = """` (What’s Really Happening)](#query---whats-really-happening "Understanding multiline strings assigned to variables")
  - [Is `query = """` a Docstring?](#is-query---a-docstring "Clarifying when triple quotes create docstrings")
  - [Why Triple Quotes Are Used for SQL](#why-triple-quotes-are-used-for-sql "Why triple quotes are used for SQL queries in Python")
  - [Side-by-Side Comparison](#side-by-side-comparison "Comparing docstrings vs string literals")
  - [Quick Definitions](#quick-definitions "Definitions of string literal and docstring")
- [`import` sqlite3](#import-sqlite3 "What import does and why")
- [What `def` means](#what-def-means "How giving a function a nickname can save you time")
- [Functions](#functions "In-depth explanation of a function")
  - [Example Function](#example-function "Brief snippet of a function")
- [Cursors](#cursors "Explanation of cursors and their role in the example code")
  - [`conn.cursor()`](#conncursor "Creating a cursor object to execute SQL")
  - [`cursor.execute(query)`](#cursorexecute "Run the query against the database")
  - [`cursor.fetchall()`](#cursorfetchall "Return all results as a list of tuples")
- [Decision Making](#decision-making "Useful for filtering, validation, and conditional logic")
  - [`if`, `elif`, `else`](#if-elif-else "Useful for conditional statements")
  - [`for` and `in`](#for-and-in "Iterating over sequences")
- [`conn.close()`](#connclose "Terminating the connection created by `connect_db()`")
- [Safety Considerations](#safety-considerations "Learn to protect your data")

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

> ❗ This example assumes the database exists and contains data—real scripts should handle exceptions for missing files, bad connections, or empty queries.

---

## Demystifying Triple Quotes (`"""`)

Triple quotes (`"""`) are used in Python to create **multi-line strings**.

Sometimes those strings become **docstrings**.
Sometimes they’re just **data**.

The difference depends entirely on **where the string appears in the code**.

---

## Docstrings (What They Are)

A **docstring** is a string literal used as **documentation**, not a comment.

Docstrings are used to:

* Explain what a module, function, or class does
* Describe parameters and return values
* Support tools like `help()` and documentation generators

When used correctly, docstrings:

* Are stored in memory
* Can be accessed programmatically (via `__doc__`)
* Help make code self-documenting

---

## Docstrings Are Not Comments

Docstrings are **not** meant to comment out code.

To leave comments in Python, use `#`.

❌ **Incorrect use of docstrings:**

```python
"""
conn = connect_db()
run_query(conn)
"""
```

Python will still create a string object here, which is wasteful and misleading.

✅ **Correct approach:**

```python
# conn = connect_db()
# run_query(conn)
```

---

## `query = """` (What’s Really Happening)

```python
query = """
SELECT
    case_id,
    case_name,
    ...
"""
```

This line stores a SQL query inside a Python variable named `query`.

At this point:

* The query is **defined**, not executed
* Python treats the SQL as **data**
* The query is run later using:

```python
cursor.execute(query)
```

---

## Is `query = """` a Docstring?

**No.**

Although it uses triple quotes, this is **not** a docstring.
It is a **multiline string literal assigned to a variable**.

Triple quotes do **not** automatically create docstrings.

---

## When a String *Is* a Docstring

A string is a docstring **only if all of the following are true**:

1. It is a string literal
2. It appears immediately after:

   * the start of a file, **or**
   * a `def` line, **or**
   * a `class` line
3. It is **not assigned to a variable**

If any of these conditions are not met, the string is **not** a docstring.

---

## Why Triple Quotes Are Used for SQL

Triple quotes are used here purely for **readability**.

SQL queries are often:

* Long
* Multi-line
* Easier to understand when formatted like real SQL

This works:

```python
query = "SELECT case_id, case_name FROM court_cases WHERE jurisdiction = 'Los Angeles, CA';"
```

But it’s hard to read.

Triple quotes preserve:

* Line breaks
* Indentation
* Human-friendly formatting

Nothing more than that.

---

## Side-by-Side Comparison

### ✅ This *is* a docstring

```python
def fetch_ice_violence_cases(conn):
    """
    Fetch court cases from Los Angeles, CA that reference ICE-related violence.
    """
```

Why?

* First statement after `def`
* Not assigned to a variable
* Stored as `fetch_ice_violence_cases.__doc__`

---

### ❌ This is *not* a docstring

```python
def fetch_ice_violence_cases(conn):
    query = """
    SELECT * FROM court_cases;
    """
```

Why?

* Assigned to a variable
* Used as executable data
* Not documentation

---

## The One Rule That Matters

> **Triple quotes don’t make something a docstring.
> Placement does.**

That single rule resolves most confusion around this topic.

---

## Quick Definitions

* **String Literal** – Text written directly into code and enclosed in quotes, used as fixed data (messages, labels, SQL queries).
* **Docstring** – A string literal placed at the start of a file, function, or class to document its purpose and usage.

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

> This ensures your database queries don’t run automatically if this file is imported as a module elsewhere.

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

**Closes the database connection that was created earlier by `connect_db()`** and then *passed into* `fetch_ice_violence_cases(conn)`.
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

---

## Safety Considerations

When coding as a professional it is important to consider bad actors and mistakes.  
In our example, we hard coded everything. Which isn't bad for an example.  
But in real-world applications, we need to consider safety. 

### **1. The problem: raw SQL strings**

Right now, in our example:

```python
query = """
SELECT case_id, case_name
FROM court_cases
WHERE jurisdiction = 'Los Angeles, CA'
  AND date_filed BETWEEN '2025-01-01' AND '2025-07-31';
"""
cursor.execute(query)
```

Here, everything is hardcoded. This works fine because we typed the values ourselves.

**The danger:** if any of the values come from user input (e.g., a form, API, or variable), directly inserting them into a string can let someone **inject malicious SQL**. This is called **SQL injection**.

Example of a risky pattern:

```python
user_input = "Los Angeles, CA'; DROP TABLE court_cases; --"
query = f"""
SELECT case_id, case_name
FROM court_cases
WHERE jurisdiction = '{user_input}';
"""
cursor.execute(query)
```

> This could delete your table! 😱


### **2. The safe way: parameterized queries**

Python’s `sqlite3` library supports **parameterized queries**, which separate **SQL logic** from **data**.

Instead of building the SQL string manually, you use **placeholders** (`?`) where data will go, and then supply the values separately.

Example:

```python
jurisdiction = "Los Angeles, CA"
start_date = "2025-01-01"
end_date = "2025-07-31"

query = """
SELECT case_id, case_name
FROM court_cases
WHERE jurisdiction = ?
  AND date_filed BETWEEN ? AND ?;
"""

cursor.execute(query, (jurisdiction, start_date, end_date))
```

**What’s happening:**

1. The SQL engine sees the `?` placeholders as “slots.”
2. The values `(jurisdiction, start_date, end_date)` are **passed separately**.
3. The database safely inserts the data without letting it break the SQL logic.

✅ Result: safer queries, immune to SQL injection, and easier to maintain if you ever need dynamic values.

In Python’s sqlite3 module, the typical placeholder is a ?.
For example:

```python
cursor.execute(
    "SELECT * FROM users WHERE username = ?",
    (username,)
)

```

### **3. Why it matters**

Parameterized Queries: In real apps, you rarely want to hard‑code values directly into your SQL string. Instead, use *parameterized queries* where the SQL has `?` placeholders and the values are passed separately. This keeps the SQL logic separate from the data and prevents SQL injection attacks — a class of vulnerabilities where user input could otherwise be interpreted as SQL code. In Python’s sqlite3, it looks like:

```python
cursor.execute("SELECT * FROM court_cases WHERE jurisdiction = ? AND date_filed >= ?", (jurisdiction, start_date))
```

* If you ever accept input from outside your code (forms, APIs, files), **never** put it directly into a raw SQL string.
* Parameterized queries are the standard way to prevent security risks.

---

#### TL;DR

* **Raw string** = good for teaching or hardcoded values.
* **Parameterized query** = safe for dynamic data; uses `?` as placeholders.
* Example:

```python
cursor.execute("SELECT * FROM table WHERE col = ?", (value,))
```
