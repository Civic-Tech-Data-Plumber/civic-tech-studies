> **Learning Log** - Show & Create Databases   
> **Date:** 2026-02-05     
> **Source / Exercise:** "SQL In Easy Steps" by Mike McGrath  
> **Summary:**  An introduction to viewing and creating databases

# Quick Reference
- [Show Databases](#show-databases)
- [Create Database](#create-database)
- [Best Practices](#best-practices)

---

## Show Databases

The SQL query that can be used to display all existing databases on a database server is:

```sql
SHOW DATABASES ;
```

This will generally output the following table, plus any additional databases created by the user:

| Database              |
| --------------------- |
| information_schema    |
| mysql                 |
| performance_schema    |
| sys                   |

> Typical MySQL installations will include the databases listed above by default, which the MySQL server uses.


---

## Create Database

To make a new database we use the SQL keywords: `CREATE` `DATABASE` and the code will look like this:

```sql
CREATE DATABASE database_name ;
```

> Due to case sensitivity issues across different DBMS, it is recommended to use lowercase characters with underscores to seperate words.  
> Database names may contain letters, digits, and the underscore character. Avoid spaces and any other characters.


---

## Best Practices

To prevent errors when creating a database that may already exist, best practice is to use the `IF NOT EXISTS` clause.

```sql
CREATE DATABASE IF NOT EXISTS database_name ;
```

> MySQL on Linux will treat the database "LIBRARY" as a different database than "library", whereas others may not. Convention dictates using one method (lowercase only) to prevent future confusion.

```sql
### Naming Example (Civic Tech)

```sql
CREATE DATABASE IF NOT EXISTS courtlistener_archive;
```

> In civic tech projects, databases often store public records, court data, housing data, or budget information.   
> Clear naming conventions and predictable structure are critical for transparency, reproducibility, and collaboration.
