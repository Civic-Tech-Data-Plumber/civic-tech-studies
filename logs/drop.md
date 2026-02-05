> **Learning Log** - Drop Keyword  
> **Date:** 2026-02-05     
> **Source / Exercise:** "SQL In Easy Steps" by Mike McGrath / ChatGPT  
> **Summary:**  Deleting elements with the `DROP` keyword

# Quick Reference
- [Drop Keyword](#drop-keyword)
- [Drop Tables](#tables)
- [Drop Databases](#databases)
- [Drop Views](#views)
- [Drop Indexes](#indexes)
- [Drop Stored Procedures / Functions](#stored-procedures--functions)
- [Drop Triggers](#triggers)

---

## Drop Keyword

To permanently remove objects from a database we can use the `DROP` keyword. It is used to **permanently remove objects from a database**. Here’s a clear breakdown:

### Quick Takeaway

**DROP is like a “nuclear option” in SQL.**
Anything you `DROP` is **gone permanently**, so it’s used for removing database objects, not individual rows or columns (for rows, use `DELETE`; for columns, use `ALTER TABLE … DROP COLUMN`).

* ✅ Tables, databases, views, indexes, procedures, functions, triggers
* ❌ Not used for data inside rows or for altering table columns (different commands for that)

---

## Tables

```sql
DROP TABLE table_name;
```

* Deletes the **table structure** and **all data** inside it.
* Cannot be undone unless you have a backup or transaction rollback (some DBMS allow `DROP TABLE … CASCADE` carefully).

---

## Databases

```sql
DROP DATABASE database_name;
```

* Deletes the **entire database** including all its tables, views, stored procedures, etc.
* **Use with extreme caution.**

---

## Views

```sql
DROP VIEW view_name;
```

* Removes a **view** (a saved SQL query) from the database.
* The underlying tables are **not affected**, only the saved query disappears.

---

## Indexes

```sql
DROP INDEX index_name ON table_name;  -- MySQL
```

* Deletes an **index** used for faster searching.
* The table itself remains; only the index is removed.

---

## Stored Procedures / Functions

```sql
DROP PROCEDURE procedure_name;
DROP FUNCTION function_name;
```

* Removes a **procedure** or **function** from the database.

---

## Triggers

```sql
DROP TRIGGER trigger_name;
```

* Removes a **trigger** (an automated action tied to a table event, e.g., INSERT, UPDATE, DELETE).
