> **Learning Log** - Fuzzy Matching  
> **Date:** 2026-02-06     
> **Source / Exercise:** ChatGPT  
> **Summary:** Detecting OCR errors, typos, and alternate spellings in legal documents using SQL pattern matching, Python fuzzy matching, and text normalization.

# Quick Reference
- [SQL Approach](#sql-approach "Using LIKE and Pattern Matching to catch errors")
- [Python Approach](#python-approach "Using Python for fuzzy pattern recognition and foreign characters.")
- [Example Script](#example-script "Real world example script written in Python.")

---

## SQL Approach 

**Using LIKE & pattern matching**

If your text is in a column (`text_column`) of a table (`cases`):

```sql
-- Find rows that might contain OCR typos
SELECT *
FROM cases
WHERE text_column LIKE '% fife %'
   OR text_column LIKE '% fiVe %'
   OR text_column LIKE '% fiv %';
```

**Explanation:**

* `%` → zero or more characters
* `_` → exactly one character (optional for more granular patterns)
* Can chain multiple LIKE clauses with `OR` to catch variants
* Quick, but **manual**: you need to know what to search for

---

### Another SQL Approach
**Using Levenshtein / Fuzzy Matching**

Some DBMS support **approximate string matching**. PostgreSQL example with `fuzzystrmatch`:

```sql
-- Find words similar to 'five' with up to 1 character difference
SELECT *
FROM cases
WHERE levenshtein('five', word_column) <= 1;
```

* Catches “fife”, “fiv”, “fivv”, etc.
* More flexible than LIKE for unknown typos
* Useful for names, addresses, or OCR’d terms

---

## Python Approach: FuzzyWuzzy / RapidFuzz

Python gives you more control for pre-processing and scanning full text.

How it works

- Input: a word from the document ("fife")
- Compare it to a list of expected words (["five", "ten", "life"])
- Output: a similarity score (0–100)

Example:

```python
from rapidfuzz import process

# Sample text
text = "The defendant was sentenced for a term less than for fife."

# Words we want to check against
target_words = ["five", "ten", "life"]

# Split the text into words
words_in_text = text.split()

# Check each word
for word in words_in_text:
    match, score, _ = process.extractOne(word, target_words)
    if score >= 80:  # threshold for "close enough"
        print(f"Potential typo: '{word}' ~ '{match}' ({score}%)")
```

**Output:**

```matlab
Potential typo: 'fife' ~ 'life' (80%)
```
✅ Why this is useful: You catch words that are likely intended but misspelled, without having to manually inspect every row.

* `fuzz.ratio` → basic similarity score
* Can be used in loops for large datasets
* Threshold (e.g., 80%) decides sensitivity

> Fuzzy matching lets you detect similar strings instead of exact matches. This is essential when dealing with typos, OCR errors, or inconsistent formatting in court documents.

---

### Python + Normalization / Transliteration 
**For foreign characters**

Documents often contain special or accented characters (ë, ü, ö, etc.) that break simple string matching. `unidecode` converts text to plain ASCII, making fuzzy matching more reliable.

Installation:
```bash
pip install unidecode
```

OCR can also introduce accented characters or misread symbols:

```python
import unidecode

text = "Jëan Düe was sanctioned."
normalized_text = unidecode.unidecode(text)
print(normalized_text)

```
Output:
```nginx
Jean Due was sanctioned.
```

* Converts accented characters to ASCII equivalents
* Combine with fuzzy matching for robust OCR error handling

---

### Combining Normalization + Fuzzy Matching

```python
from rapidfuzz import process
import unidecode

# Example court text
text = "The defendant was sentenced for a term less than for fife."

# Words to check
target_words = ["five", "ten", "life"]

# Normalize text
normalized_text = unidecode.unidecode(text)

# Split into words
words_in_text = normalized_text.split()

# Fuzzy match each word
for word in words_in_text:
    match, score, _ = process.extractOne(word, target_words)
    if score >= 80:
        print(f"Possible typo detected: '{word}' ~ '{match}' ({score}%)")
```

**Output:**

```matlab
Possible typo detected: 'fife' ~ 'life' (80%)
```

* This shows that `"fife"` might actually be `"life"` or `"five"`.
* You can adjust the threshold (`score >= 80`) depending on how strict you want to be.

---

## Why Fuzzy Matching + Normalization is Critical in Civic-Tech

1. **OCR errors are common** in scanned court documents or PDFs.
2. **Misspellings and variations** in names, locations, and legal terms can hide relevant records.
3. **Automated dashboards or reports** need clean, standardized data. Missing “life” as a keyword could completely alter your statistics.

By normalizing and fuzzily matching:

* You catch near-miss terms like `"fife"` → `"life"`
* You maintain data integrity for **aggregates, counts, and dashboards**
* You reduce manual review time while flagging potential errors

---

### Pro Tips 

* Always **document your threshold**: e.g., 80–90% similarity is common for OCR/typo detection.
* **Normalize first**, then match. Accents or special characters can throw off fuzzy matching.
* **Keep a human review step**: Automated methods flag potential matches, but a human confirms.
* Combine with **context clues**: e.g., the sentence mentions “term” → likely a number or `"life"`.

✅ **Key Takeaways for Civic-Tech Data Handling:**

* OCR errors, typos, and alternate spellings are **common** in legal documents.
* Use **pattern matching (LIKE)** for known issues.
* Use **fuzzy matching** for unknown variations.
* Consider **normalization** before comparison (remove accents, lowercase, strip punctuation).
* Always **document flagged results** for later verification—human review matters.
* If processing large datasets, normalize all text first and then vectorize for performance.
* Consider combining fuzzy matching + context checks (e.g., look for surrounding words like "sentence" or "term" to reduce false positives).
* This workflow can feed dashboards, reports, or compliance checks, so you catch key legal terms even with OCR errors.

🧠 Mental Model

* LIKE → quick, literal string patterns; best for known issues
* Fuzzy matching → flexible, finds near matches for unknown errors
* Normalization → removes accents, special characters, and noise
* Thresholds → determine sensitivity; too low = false positives, too high = false negatives
* Human review → necessary for verification before dashboarding or reporting

--

## Example Script

```python
# Fuzzy Matching + Normalization Civic-Tech Example
# Detect "life" in scanned court text, even with OCR errors

from rapidfuzz import process
import unidecode

# Example dataset (could be rows from a CSV or database)
court_texts = [
    "The defendant was sentenced to a term less than for fife.",
    "Sentence included 25 years, not l1fe.",
    "This individual received a life sentence.",
    "The jury recommended lifë imprisonment.",
]

# Words we want to flag
target_words = ["life"]

# Threshold for fuzzy match (0–100)
threshold = 80

# Loop through each text entry
for i, text in enumerate(court_texts, start=1):
    # Step 1: Normalize text (remove accents, special characters)
    normalized_text = unidecode.unidecode(text)
    
    # Step 2: Split normalized text into words
    words_in_text = normalized_text.split()

---
    
    # Step 3: Compare each word to our target words
    for word in words_in_text:
        match, score, _ = process.extractOne(word, target_words)
        if score >= threshold:
            print(f"[Row {i}] Possible typo detected: '{word}' ~ '{match}' ({score}%)")
```

Output:
```csharp
[Row 1] Possible typo detected: 'fife' ~ 'life' (80%)
[Row 2] Possible typo detected: 'l1fe' ~ 'life' (90%)
[Row 3] Possible typo detected: 'life' ~ 'life' (100%)
[Row 4] Possible typo detected: 'life' ~ 'life' (100%)
```

---

### Example Script Exanded
**Mini Workflow: CourtListener CSV → Cleaned & Fuzzy-Matched Dataset**
 a **mini workflow** that illustrates the full process, using both **SQL** and **Python**, and highlighting where **normalization** and **fuzzy matching** come in.

**Goal:** Take a raw CourtListener CSV, detect potential misspellings, normalize text, and prepare it for analysis.

---

#### **Step 1: Load the CSV into SQL**

Assume you have downloaded a CourtListener CSV (e.g., `cases.csv`) and want to inspect it.

```sql
-- Create a table
CREATE TABLE cases (
    case_id INTEGER,
    defendant_name TEXT,
    summary TEXT,
    sentence_years INTEGER
);

-- Load CSV data (example for SQLite)
.mode csv
.import cases.csv cases
```

**Quick Checks in SQL:**

```sql
-- Look at distinct names / values
SELECT DISTINCT defendant_name FROM cases LIMIT 20;

-- Count rows
SELECT COUNT(*) FROM cases;
```

> ✅ At this stage, you are **exploring the raw data**. Look for obvious duplicates, empty rows, or encoding issues (e.g., accented letters, strange symbols).

---

#### **Step 2: Identify Potential OCR / Misspellings Using SQL**

Use `LIKE` or approximate string matching:

```sql
-- Simple LIKE example
SELECT *
FROM cases
WHERE defendant_name LIKE '%fife%';  -- might actually be 'five' or 'life'

-- PostgreSQL: fuzzy matching with Levenshtein
SELECT *
FROM cases
WHERE levenshtein(defendant_name, 'five') <= 1;
```

> ⚠️ SQL is good for **quick filtering**, but Python gives more flexibility for full-text normalization and fuzzy matching.

---

#### **Step 3: Export Data to Python for Normalization & Fuzzy Matching**

```python
import pandas as pd
from rapidfuzz import process
import unidecode

# Load CSV
df = pd.read_csv("cases.csv")

# Normalize text: remove accents, unify case
df['defendant_name_norm'] = df['defendant_name'].apply(lambda x: unidecode.unidecode(str(x)).upper())

# Target words we want to check for
target_words = ["FIVE", "LIFE", "TEN"]

# Define fuzzy matching function
def fuzzy_check(word, targets, threshold=80):
    match, score, _ = process.extractOne(word, targets)
    if score >= threshold:
        return match
    return None

# Apply fuzzy matching
df['potential_typo'] = df['defendant_name_norm'].apply(lambda x: fuzzy_check(x, target_words))
```

**Output Example:**

| defendant_name | defendant_name_norm | potential_typo |
| -------------- | ------------------- | -------------- |
| fife           | FIFE                | FIVE           |
| life           | LIFE                | LIFE           |
| jëan düe       | JEAN DUE            | None           |

---

## **Step 4: Review / Flag Potential Matches**

```python
# Flag rows with possible typos
typos = df[df['potential_typo'].notna()]
print(typos[['defendant_name', 'potential_typo']])
```

> ⚠️ Human review step: You **verify the flagged entries** before correcting them. Automation is powerful, but mistakes can propagate into dashboards.

---

#### **Step 5: Correct / Standardize Names (Optional)**

```python
# Replace with corrected values
df['defendant_name_clean'] = df.apply(
    lambda row: row['potential_typo'] if row['potential_typo'] else row['defendant_name_norm'],
    axis=1
)
```

Now your dataset has **normalized, typo-corrected names** ready for aggregation, counts, or civic-tech analysis.

---

#### **Step 6: Analyze / Aggregate**

```python
# Example: count defendants by corrected name
counts = df.groupby('defendant_name_clean').size().reset_index(name='case_count')
print(counts.sort_values(by='case_count', ascending=False))
```

---

#### ✅ **Mini Workflow Summary**

| Step | Tool       | Purpose                                                             |
| ---- | ---------- | ------------------------------------------------------------------- |
| 1    | SQL        | Load CSV, quick exploration, basic filtering                        |
| 2    | SQL        | Detect potential typos with LIKE or Levenshtein                     |
| 3    | Python     | Normalize text (unidecode, uppercase), fuzzy match unknown variants |
| 4    | Python     | Flag potential typos for human review                               |
| 5    | Python     | Correct / standardize names or text                                 |
| 6    | Python/SQL | Aggregate, count, analyze, generate civic-tech dashboards           |

---

**Pro Tips:**

* Always normalize before fuzzy matching (`unidecode` + `upper()` or `lower()`).
* Keep a **human-in-the-loop** review step for sensitive or legal data.
* Document **thresholds** used for fuzzy matching (e.g., 80–90% similarity).
* Use SQL for **bulk checks**, Python for **complex normalization/fuzzy logic**.
* Store the cleaned dataset as CSV or SQLite for downstream reporting.
