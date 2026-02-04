> **Learning Log:** - Example Python Script Date: 2026-01-30  
> **Source / Exercise:** SQLBolt and ChatGPT  
> **Summary:** Learning professional methods on how to paginate table returns.

### Quick Reference
- [How `LIMIT` and `OFFSET` Works](#how-limit--offset-works)
- [Simple Pagination Code Example](#shorter--simpler-syntax)
- [Auto Pagination Code Example](#how-to-avoid-manually-updating-offset)
---

### How `LIMIT` + `OFFSET` works

`LIMIT` + `OFFSET` is SQL’s way of paginating results—basically slicing your large dataset into smaller “pages” that are easier to handle or display. 


```sql
SELECT * FROM court_cases
LIMIT 100 OFFSET 0;
```

* `LIMIT 100` → return **at most 100 rows**
* `OFFSET 0` → start **at the first row**

To get the next “page” of 100 rows:

```sql
SELECT * FROM court_cases
LIMIT 100 OFFSET 100;
```

* `OFFSET 100` → skip the first 100 rows, start at row 101

Then next page:

```sql
LIMIT 100 OFFSET 200;
```

…and so on.

---

### Shorter / simpler syntax

Many SQL databases support **`LIMIT num_start, num_rows`**:

```sql
SELECT * FROM court_cases
LIMIT 0, 100;   -- first page
LIMIT 100, 100; -- second page
LIMIT 200, 100; -- third page
```

* First number = offset
* Second number = limit (number of rows)

> 📌 **Note:** SQLite officially uses `LIMIT x OFFSET y`, while MySQL allows `LIMIT offset, count`. *Always check your DB.*

---

### How to avoid manually updating OFFSET

If you’re doing this programmatically (Python, etc.), you can **calculate the offset dynamically**:

```python
rows_per_page = 100
page_number = 3  # page numbers start at 1

offset = (page_number - 1) * rows_per_page

query = f"""
SELECT * FROM court_cases
LIMIT {rows_per_page} OFFSET {offset};
"""
```

* Page 1 → offset = 0
* Page 2 → offset = 100
* Page 3 → offset = 200

This way, your code “automates” pagination—no need to type offsets manually.
