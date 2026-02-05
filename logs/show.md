> **Learning Log** - SHOW Keyword  
> **Date:** 2026-02-05  
> **Source / Exercise:** "SQL In Easy Steps" by Mike McGrath / ChatGPT  
> **Summary:** Using `SHOW` to inspect database objects  

# Quick Reference

- [SHOW Keyword](#show-keyword "Introduction to the SHOW keyword for inspecting database objects")
- [SHOW Databases](#show-databases "Display all databases on the server")
- [SHOW Tables](#show-tables "Display all tables in a database")
- [SHOW Columns / Describe](#show-columns--describe "Inspect the columns in a table")
- [SHOW Indexes](#show-indexes "See the indexes defined on a table")
- [Best Practices](#best-practices "Tips for using SHOW safely and efficiently")

---

## SHOW Keyword

The `SHOW` keyword is used to **inspect database objects**.

* ✅ Learn about databases, tables, columns, and indexes
* ❌ Does not modify any data — safe to use without risk
* 💡 Civic-Tech Tip: Use `SHOW` to understand dataset structure before running queries or cleaning data.

---

## SHOW Databases

```sql
SHOW DATABASES;
```

* Lists all databases on the server.
* Typical output includes system databases and any user-created databases:

| Database           |
| ------------------ |
| information_schema |
| mysql              |
| performance_schema |
| sys                |

> Useful when exploring a new server or public civic datasets to see what’s available.

---

## SHOW Tables

```sql
SHOW TABLES;
```

* Lists all **tables** in the current database.
* Helps you know what data is accessible.

### Example:

```sql
USE courtlistener;
SHOW TABLES;
```

Output might include:

| Tables_in_courtlistener |
| ----------------------- |
| court_cases             |
| people                  |
| opinions                |

> 💡 Civic-Tech Tip: Always check tables before running SELECT statements to avoid errors.

---

## SHOW Columns / DESCRIBE

```sql
SHOW COLUMNS FROM table_name;
-- or equivalently
DESCRIBE table_name;
```

* Displays **column names, types, keys, default values, and constraints** in a table.

### Example:

```sql
DESCRIBE citizens;
```

| Field      | Type         | Null | Key | Default | Extra |
| ---------- | ------------ | ---- | --- | ------- | ----- |
| citizen_id | INT          | NO   | PRI | NULL    |       |
| name       | VARCHAR(100) | NO   |     | NULL    |       |
| birthdate  | DATE         | YES  |     | NULL    |       |
| registered | BOOLEAN      | YES  |     | TRUE    |       |

> 💡 Civic-Tech Tip: Use DESCRIBE to check column types before importing external data (like CSVs) into a table.

---

## SHOW Indexes

```sql
SHOW INDEXES FROM table_name;
```

* Lists all **indexes** defined on a table.
* Shows which columns are indexed, the index type, and uniqueness.

### Example:

```sql
SHOW INDEXES FROM citizens;
```

| Table    | Non_unique | Key_name | Column_name |
| -------- | ---------- | -------- | ----------- |
| citizens | 0          | PRIMARY  | citizen_id  |
| citizens | 1          | idx_name | name        |

> 💡 Civic-Tech Tip: Check indexes before running queries on large public datasets — indexes improve query speed.

---

## Best Practices

* `SHOW` is **read-only** — it’s safe for exploration.
* Combine with `USE database_name;` to inspect specific databases.
* Use `DESCRIBE` or `SHOW COLUMNS` to understand table structure before querying.
