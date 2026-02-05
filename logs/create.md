> **Learning Log** - CREATE Keyword  
> **Date:** 2026-02-05  
> **Source / Exercise:** "SQL In Easy Steps" by Mike McGrath / ChatGPT  
> **Summary:** Creating database objects with the `CREATE` keyword

# Quick Reference

* [CREATE Keyword](#create-keyword "Quick overview of the CREATE keyword.")
* [CREATE Database](#database "Create a new database.")
* [CREATE Table](#table "Create a new table.")
* [CREATE View](#view "Create a new view.")
* [CREATE Index](#index "Create a new index.")
* [CREATE Stored Procedures / Functions](#stored-procedures--functions "Create a new procedure or function.")
* [CREATE Triggers](#triggers "Create a new trigger.")
* [Best Practices](#best-practices "Pro tips"  )

---

## CREATE Keyword

The `CREATE` keyword is used to **make new objects in a database**. These objects include databases, tables, views, indexes, stored procedures, functions, and triggers.

### Quick Takeaway

**CREATE is constructive — the opposite of DROP.**

* ✅ Use `CREATE` to define a new database object
* ❌ Cannot use CREATE to modify existing objects (use `ALTER` instead)
* 💡 Civic-Tech Tip: Always confirm that object names are unique to avoid overwriting or conflicts, especially when using shared datasets.

---

## Database

```sql
CREATE DATABASE database_name;
```

* Creates a new database on the server.
* Optionally, you can ensure safety with:

```sql
CREATE DATABASE IF NOT EXISTS database_name;
```

> MySQL on Linux treats `LIBRARY` and `library` as different databases. Consistent lowercase naming prevents confusion.

---

## Table

```sql
CREATE TABLE table_name (
    column1 datatype constraints,
    column2 datatype constraints,
    ...
);
```

* Defines a **table** with columns and their data types.
* Common data types: `INT`, `VARCHAR(length)`, `DATE`, `TEXT`, `BOOLEAN`
* Constraints: `PRIMARY KEY`, `NOT NULL`, `UNIQUE`, `FOREIGN KEY`

### Example:

```sql
CREATE TABLE citizens (
    citizen_id INT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    birthdate DATE,
    registered BOOLEAN DEFAULT TRUE
);
```

> 💡 Civic-Tech Tip: When designing tables for public datasets, include metadata columns like `created_at` and `updated_at` to track changes responsibly.

---

## View

```sql
CREATE VIEW view_name AS
SELECT column1, column2
FROM table_name
WHERE conditions;
```

* Saves a **query** as a reusable object.
* The underlying tables remain intact; views just give a filtered or formatted perspective.

### Example:

```sql
CREATE VIEW active_citizens AS
SELECT citizen_id, name
FROM citizens
WHERE registered = TRUE;
```

---

## Index

```sql
CREATE INDEX index_name ON table_name(column_name);
```

* Improves **query performance** by letting the database find rows faster.
* Does **not** duplicate data; just creates a reference.

### Example:

```sql
CREATE INDEX idx_birthdate ON citizens(birthdate);
```

> ⚠️ Remember: Indexes improve reads but slow writes slightly. Use them selectively, especially with large civic datasets.

---

## Stored Procedures / Functions

```sql
CREATE PROCEDURE procedure_name(parameters)
BEGIN
    -- SQL statements
END;

CREATE FUNCTION function_name(parameters)
RETURNS datatype
BEGIN
    -- SQL statements
END;
```

* Encapsulates SQL logic for reuse.
* Procedures are invoked with `CALL procedure_name();`
* Functions return a value that can be used in queries.

---

## Triggers

```sql
CREATE TRIGGER trigger_name
AFTER INSERT ON table_name
FOR EACH ROW
BEGIN
    -- SQL statements
END;
```

* Executes **automatically** when a table event occurs (INSERT, UPDATE, DELETE).
* Useful for logging, validation, or automated updates.

---

## Best Practices

* Use `IF NOT EXISTS` to avoid errors when creating objects that may already exist.
* Stick to lowercase names with underscores for consistency across platforms.
* Document the purpose of each object with comments:

```sql
-- This table stores registered citizens in the city database
CREATE TABLE citizens (...);
```

* 💡 Civic-Tech Tip: When working with civic datasets, ensure column names are meaningful, descriptive, and comply with privacy rules.
