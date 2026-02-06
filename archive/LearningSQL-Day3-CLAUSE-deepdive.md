> Learning log - Day 3

Today we created a helper script to query ICE-related court cases in LA for the first half of 2025, explored how functions, loops, and conditionals operate in Python, and clarified how docstrings, imports, and connections work.

# Diving deeper into what the code does and means
Day two showed us some important terms for us to use and consider.  
Today I learned about these terms, and a few new ones. 

 It's helpful to think of Python data scripts like this:

**IMPORT** tools  
**DEFINE** reusable functions  
**CONNECT** to data  
**QUERY** data  
**LOOP** through results  
**RETURN** or **SAVE** output  
**CLOSE** resources   

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

    cursor = conn.cursor()
    cursor.execute(query)
    return cursor.fetchall()


if __name__ == "__main__":
    conn = connect_db()
    cases = fetch_ice_violence_cases(conn)

    for case in cases:
        print(case)

    conn.close()
```

## Understanding and demystifying `"""`

The first thing we see in this sample is `"""`

This is a `docstring`.  
A docstring:
- Is stored in memory
- Can be accessed programmatically
- Is used by help tools and documentation generators

### What docstrings are actually for

Docstrings are structured documentation, not comments.

They are used to:

- Explain what a module, function, or class does
- Describe parameters and return values
- Generate documentation automatically

### Docstrings are NOT comment blocks

Best practices for data engineers wanting to create a comment is to use the `#` symbol (per line).  
Use docstrings (`"""`) for documentation.

### Why using docstrings in my learning journal is a great idea:

- They explain intent, not just mechanics
- They signal seriousness and clarity
- They scale well as scripts grow
- They make our code self-documenting

### Beginner mistakes:
❌ Do not use `"""` to comment out blocks of code:
```python
"""
conn = connect_db()
run_query(conn)
"""
```
Python will still **create a string object,** which is wasteful and confusing.  
*Reminder:* ❌ Do not use `"""` to comment out blocks of code, use `#` instead.

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
##v What `def` means
```python
def connect_db(db_path="courtlistener.db"):
```
Define a reusable block of code called `connect_db`.
### In English, Please!
- def = define
- connect_db = function name
- Everything indented underneath belongs to that function
- The function does nothing until it is called

### So, "Functions"?
Functions are used constantly by data engineers because they let you:
- Avoid repeating yourself
- Make pipelines readable
- Isolate failures
- Reuse logic across scripts
- Test parts independently

In essence, for data plumbers, *functions are are essentially the pipes*.  
They are a reusable too, or pipe, from which to define the paramaters of our data query.
```python
def fetch_cases(conn):
    return conn.execute("SELECT * FROM court_cases")
```

## If, elif, else = decision making
```python
if outcome == "Guilty":
    flag = True
else:
    flag = False
```
Useful for: Filtering, Validation and Conditional logic.

## For and In *(for each selected item in the defined set of items)* 
Example:
```python
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(f"I like this selected {fruit}")
```
### Nerding out on definitions (*not for the light hearted*)
The `for` statement in Python is used for iterating over a sequence (such as a list, tuple, string, or dictionary) or other iterable objects, executing a block of code for each item.
```python
for variable in iterable:
    # Code block to be executed
    # with the current 'variable' value
```
`variable` is a loop variable that takes on the value of the current item in the `iterable` during each iteration.  
And an `iterable` is the collection of items you want to loop through.  
***Iteration = the repetition of a process or utterance***

You can alter the flow of a for loop using control statements: 
- `break`: Exits the loop entirely, regardless of whether the iterable has been exhausted.
- `continue`: Skips the current iteration and proceeds to the next item in the sequence.
- `pass`: Acts as a placeholder when a statement is syntactically required but no action is desired.
- `else` clause: A block that executes only if the loop completes all iterations without encountering a break statement.

### To reiterate our iterations 😛
`for` item `in` set_of_items `do` x, y and return z.
## `with` - Safe resource handling (Important!)
```python
with sqlite3.connect("courtlistener.db") as conn:
    ...
```
This sample code basically means, “Open this resource, and guarantee it gets closed.”  
It replaces `conn.close()`  
*I'll need to cover this topic later, as I still need to learn a bit more about it.*

## `try` / `except` is used for error handling
```python
try:
    conn.execute(query)
except Exception as e:
    print(e)
```
Used to:
- Prevent crashes
- Log failures
- Keep pipelines running

## `class` an uncommon, but useful feature
```python
class CourtCase:
    ...
```
*Another topic I need to learn more about before divulging too much.*

## `if __name__ == "__main__":` fundamental to Python literacy
What __name__ means

Every Python file has a built-in variable called __name__.
If the file is run directly, Python sets:
```python
  __name__ = "__main__"
```

If the file is imported by another file, Python sets:

  ```python
__name__ = "ice_violence_queries"
```
If you're still confused, don't worry. So am I.  
Hopefully, by day 4 we will have this figured out. There is so much to wrap our heads around.
