> **Learning Log** – LIKE Keyword  
> **Date:** 2026-02-05  
> **Source / Exercise:** "SQL In Easy Steps" by Mike McGrath / ChatGPT  
> **Summary:** Filtering rows using pattern matching with `LIKE`  

# Quick Reference

* [LIKE Keyword](#like-keyword "Filter rows based on pattern matching")
* [Basic LIKE](#basic-like "Simple pattern matching examples")
* [Wildcards](#wildcards "Special characters for flexible matching")
* [Case Sensitivity](#case-sensitivity "How case affects LIKE")
* [Civic-Tech Examples](#civic-tech-examples "Practical LIKE usage in civic datasets")
* [Tips & Gotchas](#tips--gotchas "Common pitfalls and best practices")

---

## LIKE Keyword

`LIKE` is used to **match text patterns** in string columns.

*Unlike `=`, which checks exact equality, `LIKE` lets you search for partial matches or patterns.*

Syntax:

```sql
SELECT column1, column2
FROM table_name
WHERE column_name LIKE 'pattern';
```


## Tips & Gotchas

* `%` matches **zero or more characters**, `_` matches **exactly one character**
* Use wildcards **sparingly** on large tables — can slow queries
* `LIKE` matches **patterns only in strings**; numbers must be cast to text
* For case-insensitive searches in PostgreSQL, use `ILIKE`
* Be cautious with leading `%` — prevents index usage in many DBMS

---

## Basic LIKE

```sql
SELECT name
FROM citizens
WHERE name LIKE 'Smith';
```

*Matches only rows where `name` is exactly "Smith" (no wildcards here — behaves like `=`).*  
`LIKE` is very literal. If someone replaces letters with accented symbols (ë, ï, ö) or swaps letters around, LIKE may not match unless you explicitly account for those variants.


---

## Wildcards

Two main wildcards in SQL:

| Wildcard | Meaning                 | Example                                         |
| -------- | ----------------------- | ----------------------------------------------- |
| `%`      | Zero or more characters | `'Smi%'` → "Smith", "Smithee", "Smiley"         |
| `_`      | Exactly one character   | `'S_ith'` → "Smith", "Swith", but not "Smithee" |

```sql
-- 5-letter names ending with "n"
SELECT name
FROM citizens
WHERE name LIKE '___n';
```

```sql
-- Fuzzy matching example for John Doe. 
SELECT name
FROM citizens
WHERE name LIKE 'J_n D__e%';
```

---

## Case Sensitivity

* Behavior depends on DBMS:

  * MySQL → usually **case-insensitive** (`LIKE 'smith'` matches "Smith")
  * PostgreSQL → **case-sensitive** by default (`ILIKE` can be used for case-insensitive)
* Tip: Check DBMS behavior for consistent civic datasets.

---

## Civic-Tech Examples

1. **Filter officers whose badge ID starts with "CA"**

```sql
SELECT officer_id, name
FROM officers
WHERE officer_id LIKE 'CA%';
```

2. **Find complaints mentioning “force” anywhere in summary**

```sql
SELECT case_id, summary
FROM complaints
WHERE summary LIKE '%force%';
```

3. **Identify city names with 5 letters**

```sql
SELECT city
FROM locations
WHERE city LIKE '_____';
```

### Better approaches used in real world compliance systems

Banks and regulators usually **go beyond simple SQL patterns**:

**a) Normalization / transliteration**

- Strip accents: ë → e, ü → u
- Convert all letters to the same case (uppercase/lowercase)
- Remove punctuation or whitespace

Example (pseudo-SQL):
```sql
SELECT *
FROM sanctioned_individuals
WHERE TRANSLATE(UPPER(name), 'ËÜÖ', 'EUO') = TRANSLATE(UPPER(customer_name), 'ËÜÖ', 'EUO');
```

This way, Jëan Düe would match Jean Due.

> ⚠️ Tip: When analyzing civic datasets with multiple languages or historical spelling variations, LIKE may miss relevant matches. Consider normalization or fuzzy matching for accurate reporting.

> Function availability differs by DBMS   
> TRANSLATE() exists in some systems (Oracle, PostgreSQL) but not in others (MySQL or SQLite).
> Different SQL dialects have different syntax for replacing multiple characters or normalizing text.

**b) Fuzzy / approximate matching**

- Algorithms like Levenshtein distance (edit distance) calculate how many letters need to change to match
- Common in sanctions screening, anti-money laundering (AML) checks

Example:

```sql
-- PostgreSQL extension for fuzzy matching
SELECT *
FROM sanctioned_individuals s
JOIN customers c ON levenshtein(s.name, c.name) < 2;
```

Can catch small misspellings, swapped letters, missing letters, etc.

**c) Soundex / phonetic matching**

- Converts names into codes based on pronunciation
- Captures variations like `Smith` vs `Smyth`
- Limited for foreign names or accented characters, but helpful in combination

**d) Manual review / automated alerts**

After the automated checks flag potential matches (based on fuzzy scores or phonetic similarity), a human investigator reviews them.
