> This page is the author just testing their coding skills by creating simple scripts, creating notes, and showing the corrected code.

## Create sample snippet

```sql

CREATE DATABASE IF NOT EXISTS recent_cases;

USE recent_cases; 

CREATE TABLE IF NOT EXISTS court
(
judge_id      INT AUTO_INCREMENT PRIMARY KEY,
judge_fname   VARCHAR(50) NOT NULL,
judge_lname   VARCHAR(50) NOT NULL,
) ENGINE=InnoDB;

CREATE TABLE IF NOT EXISTS cases
(
judge_id     INT NOT NULL,
plaintiff    VARCHAR(50) NOT NULL, 
defendant    VARCHAR(50) NOT NULL,
case_date         DATETIME NOT NULL,
FOREIGN KEY (judge_id) REFERENCES court(judge_id)
  ON DELETE SET NULL
  ON UPDATE CASCADE
) ENGINE=InnoDB;

INSERT INTO court (judge_fname, judge_lname)
VALUES
  ("Mark", "McCullin"),
  ("James", "Cuban"),
  ("Jennifer", "Aster"),
  ("Bob", "May"),
  ("Tyger", "Woods");

INSERT INTO cases (plaintiff, defendant, judge_id, case_date)
VALUES
  ("Maria Gomez", "Washington University", 1, '2025-09-05 12:45:00'),
  ("Tyrome Johnson", "Lysom McGovern", 2, '2025-09-02 10:35:00'),
  ("Washington Dairy Farms", "Douglass Mitchelle", 1, '2025-09-02 10:35:00'),
  ("Jane Doe", "Jeffery Eipenhouser", 3, '2025-11-07 09:00:00'),
  ("McDonnalds Inc", "Bryson Beef Dairy Farms", 2, '2025-09-05 14:35:00'),
  ("Sicamore Apartments", "Jeremey Dylan", 2, '2025-09-02 13:35:00'),
  ("Micheal Douglass", "Maryland Dairy Farms", 1, '2025-09-02 12:35:00');

SELECT
      CONCAT_WS(' ', court.judge_fname, court.judge_lname) AS "Judge Name",
      CONCAT_WS(' v ', cases.plaintiff, cases.defendant)   AS "Case Name",
      cases.case_date                                      AS "Case Date"
      
FROM court
JOIN cases ON cases.judge_id = court.judge_id

WHERE cases.date = '2025-09-02 10:35:00' 

ORDER BY court.judge_id ASC;
```

> **Personal review:** Good, but could use an upgrade. 

---

## Schema design for tables

**Goal:**
Create schema for the tables utilizing realworld field terms from CourtListener.com

```
*Schema Design Overview*

Chosen model:

- Courts
- Judges
- Dockets
- Clusters (cases)
- Opinions
- Sources
- Panel membership (many-to-many)

Core logic:
- A court has many dockets
- A docket has many clusters
- A cluster has many opinions
- An opinion has one author (judge)
- An opinion can have multiple joining judges
- A cluster has one source code
- Panel membership is many-to-many
```

```
courts
  └── dockets
        └── clusters
              └── opinions
                    └── opinion_joins
```

```
judges
   ├── opinions (author_id)
   └── opinion_joins (panel membership)
```

```
sources
   └── clusters
```

---

## 2 continued
**Tables**
```sql

CREATE TABLE courts (
    court_id VARCHAR(20) PRIMARY KEY,
    court_name VARCHAR(255) NOT NULL
) ENGINE=InnoDB;

CREATE TABLE judges (
    judge_id INT AUTO_INCREMENT PRIMARY KEY,
    full_name VARCHAR(255) NOT NULL,
    court_id VARCHAR(20),
    FOREIGN KEY (court_id) REFERENCES courts(court_id)
        ON DELETE SET NULL
        ON UPDATE CASCADE
) ENGINE=InnoDB;

CREATE TABLE sources (
    source_code VARCHAR(10) PRIMARY KEY,
    description VARCHAR(255) NOT NULL
) ENGINE=InnoDB;

CREATE TABLE dockets (
    docket_id INT AUTO_INCREMENT PRIMARY KEY,
    docket_number VARCHAR(100) NOT NULL,
    court_id VARCHAR(20) NOT NULL,
    date_filed DATE,
    FOREIGN KEY (court_id) REFERENCES courts(court_id)
        ON DELETE RESTRICT
        ON UPDATE CASCADE
) ENGINE=InnoDB;

CREATE TABLE clusters (
    cluster_id INT AUTO_INCREMENT PRIMARY KEY,
    docket_id INT NOT NULL,
    case_name VARCHAR(255) NOT NULL,
    precedential_status VARCHAR(50),
    source_code VARCHAR(10),
    FOREIGN KEY (docket_id) REFERENCES dockets(docket_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    FOREIGN KEY (source_code) REFERENCES sources(source_code)
        ON DELETE SET NULL
        ON UPDATE CASCADE
) ENGINE=InnoDB;

CREATE TABLE opinions (
    opinion_id INT AUTO_INCREMENT PRIMARY KEY,
    cluster_id INT NOT NULL,
    author_id INT,
    opinion_type VARCHAR(100),
    date_filed DATE,
    per_curiam BOOLEAN DEFAULT FALSE,
    cite_count INT DEFAULT 0,
    FOREIGN KEY (cluster_id) REFERENCES clusters(cluster_id)
        ON DELETE CASCADE  
        ON UPDATE CASCADE,
    FOREIGN KEY (author_id) REFERENCES judges(judge_id)
        ON DELETE SET NULL   
        ON UPDATE CASCADE
) ENGINE=InnoDB;

-- Many-to-many: judges joining opinions
CREATE TABLE opinion_joins (
    opinion_id INT,
    judge_id INT,
    PRIMARY KEY (opinion_id, judge_id),
    FOREIGN KEY (opinion_id) REFERENCES opinions(opinion_id)
        ON DELETE CASCADE,
    FOREIGN KEY (judge_id) REFERENCES judges(judge_id)
        ON DELETE CASCADE
) ENGINE=InnoDB;
```

> **Notes on cascading logic based on tables created a above.**

### Applying logic 
**Why `ON DELETE` we use `RESTRICT`, `CASCADE`, and/or `SET NULL`**

| Relationship               | Best Practice        | Why                                          |
| -------------------------- | -------------------- | -------------------------------------------- |
| courts → dockets           | RESTRICT             | Courts are foundational                      |
| dockets → clusters         | CASCADE              | Clusters belong to docket                    |
| clusters → opinions        | CASCADE              | Opinions belong to cluster                   |
| opinions → opinion_joins   | CASCADE              | Join rows meaningless without opinion        |
| judges → opinions (author) | SET NULL             | Opinion survives judge deletion              |
| sources → clusters         | RESTRICT or SET NULL | Depends on whether you allow source deletion |

#### Mental Model: Strong vs Weak Entities

You can think of this like:

**Strong (root) entities:**

- courts
- judges
- sources

**Mid-level structural entities:**

- dockets
- clusters

**Weak / dependent entities:**

- opinions
- opinion_joins

> Weak entities should usually cascade.  
> Attribution-type relationships should usually set null.  
> Foundational institutions should restrict.  

#### How to Think About It Systematically

Ask three questions:

1. Is the child conceptually meaningless without the parent?
→ Use CASCADE.
2. Is the relationship optional or historically preservable?
→ Use SET NULL.
3. Is the parent foundational and rarely deleted?
→ Use RESTRICT.

### If You Delete Things…

If you delete:

**A court:**
→ Blocked (RESTRICT)

**A docket:**
→ Its clusters deleted
→ Their opinions deleted
→ Their opinion_joins deleted
> Chain reaction.

**A judge:**

→ Their authored opinions remain
→ author_id becomes NULL
→ Their panel join entries deleted
> This preserves history without orphaning join rows.

---

# Testing `SELECT` queries
**Goal:** Create sample `SELECT` queries for the above tables  
(assuming mock data has already been provided)

### 🧪 Example Query:
What's wrong? 

```sql
SELECT
    clusters.cluster_id,
    clusters.case_name,
    courts.court_name,
    dockets.docket_number,
    opinions.cite_count
FROM clusters
JOIN opinions 
    ON opinions.cluster_id = clusters.cluster_id
JOIN dockets 
    ON clusters.docket_id = dockets.docket_id
JOIN courts 
    ON dockets.court_id = courts.court_id
JOIN sources 
    ON clusters.source_code = sources.source_code
JOIN judges 
    ON judges.court_id = courts.court_id
WHERE opinions.cite_count > 3
  AND clusters.source_code = 'C';
```
> The above example includes the judges table. But, was joining judges necessary for the intended output?  
> If not, remove that `JOIN`

---

## SELF TESTS

#### 🧪 Query 1 — Basic Join Fluency

Return:
- case_name
- docket_number
- court_name
- For all clusters filed in the court named "Ninth Circuit".
- Order by docket_number ascending.

```sql
SELECT
  clusters.case_name,
  dockets.docket_number,
  courts.court_name
FROM clusters
JOIN dockets 
    ON clusters.docket_id = dockets.docket_id
JOIN courts 
    ON dockets.court_id = courts.court_id
WHERE courts.court_name = 'Ninth Circuit'
ORDER BY dockets.docket_number ASC;
```

#### 🧪 Query 2 — Filtering + Aggregation

Return:
- court_name
- COUNT of opinions
- Only for opinions where cite_count > 5.
- Group by court_name.
- Order by count descending.

> 1ST ATTEMPT:

```sql
SELECT
  courts.court_name,
  COUNT(*)
FROM
  opinions
WHERE opinions.cite_count > 5  -- note: need to join other tables
GROUP BY court_name -- note: should have been courts.court_name, as above
ORDER BY count DEC;  --note: should have used DESC
```
> Errors! didn't joined opinions to courts, use DESC
> opinions → clusters → dockets → courts

**Corrected:**

```sql
SELECT
    courts.court_name,
    COUNT(*) AS total_opinions
FROM opinions
JOIN clusters 
    ON opinions.cluster_id = clusters.cluster_id
JOIN dockets 
    ON clusters.docket_id = dockets.docket_id
JOIN courts 
    ON dockets.court_id = courts.court_id
WHERE opinions.cite_count > 5
GROUP BY courts.court_name
ORDER BY total_opinions DESC;
```

---
#### 🧪 Query 3 — Multi-Hop Join

Return:
- case_name
- judge_name (author)
- cite_count
- For opinions authored by judges in the court named "Supreme Court".
- Only include opinions with cite_count >= 10.
- Order by cite_count descending.

> 1st attempt

```sql
SELECT
  case_name,
  full_name AS judge_name,
  cite_count
FROM opinion_joins
WHERE 
   court_name = "Supreme Court" AND cite-count >= 10
ORDER BY opinions.cite_count DESC;
```

> even though the FOREIGN KEYS utilized in the table creations enforce referential integrity, they do *not* auto-join tables during queries. SQL never follows queries unless you explicitly tell it to.

What the prompt demands

We need:

- case_name → lives in clusters
- judge_name (author) → lives in judges
- cite_count → lives in opinions
- Filter by court_name = 'Supreme Court' → lives in courts
- Only authored opinions → so we use opinions.author_id
- Only cite_count >= 10

Where the snippit went wrong:
❌ No JOINs (foreign keys don’t auto-join)
❌ Referenced `court_name` without joining courts
❌ `cite-count` should be `cite_count`
❌ `FROM opinion_joins` is incorrect for author filtering
❌ `ORDER BY opinions.cite_count` requires the `opinions` table in scope

**Corrected:**

SELECT
  clusters.case_name,
  judges.full_name AS judge_name,
  opinions.cite_count
FROM opinions
JOIN clusters
  ON opinions.cluster_id = clusters.cluster_id
JOIN dockets
  ON clusters.docket_id = dockets.docket_id
JOIN courts
  ON dockets.court_id = courts.court_id
JOIN judges
  ON opinions.author_id = judges.judge_id
WHERE courts.court_name = 'Supreme Court'
  AND opinions.cite_count >= 10
ORDER BY opinions.cite_count DESC;

#### 🧪 Query 4 — Source Filtering

Return:

- case_name
- source_code
- cite_count
- For clusters where source_code = 'CRU'.
- Only include opinions with cite_count > 3.
- Order by cite_count descending.

> 1st attempt

```sql
SELECT
  clusters.case_name,
  sources.source_code,
  opinions.cite_count
FROM opinions
  JOIN clusters
    ON opinions.cluster_id = clusters.cluster_id
  JOIN sources
    ON clusters.source_code = sources.source_code
WHERE clusters.source_code = "CRU"
  AND opinions.cite_count > 3
 ORDER BY opinions.cite_count DESC; 
 ```

 > Joining sources was not needed as source code is already included in the clusters table

 **Tip: ou only need a JOIN if:**
- You are selecting columns from that table
- Or filtering using columns not already accessible
 
 **Corrected:**
 
```sql
 SELECT
  clusters.case_name,
  clusters.source_code,
  opinions.cite_count
FROM opinions
JOIN clusters
  ON opinions.cluster_id = clusters.cluster_id
WHERE clusters.source_code = 'CRU'
  AND opinions.cite_count > 3
ORDER BY opinions.cite_count DESC;
```

---

#### 🧪 Query 5 — Join Table Reasoning

Return:

- case_name
- judge_name
- For judges who were on the panel (not necessarily author).
- Use the opinion_joins table.
- Only include clusters from the court named "Second Circuit".
- Order alphabetically by judge_name.

> 1st attempt

```sql 
SELECT
  clusters.case_name,
  judges.full_name AS judge_name
FROM opinion_joins
JOIN judges
  ON opinion_joins.judge_id = judges.judge_id
JOIN opinions
  ON opinions.author_id = judges.judge_id
JOIN clusters
  ON opinions.cluster_id = clusters.cluster_id
WHERE courts.court_name = "Second Circuit"
  OR courts.court_name LIKE "2nd Circuit"
ORDER BY judge_name;
```

> errors: Joined opinions incorrectly, referenced courts but never joined, added LIKE is unnessary.

**Corrected:**

```sql
SELECT
  clusters.case_name,
  judges.full_name AS judge_name
FROM opinion_joins
JOIN judges
  ON opinion_joins.judge_id = judges.judge_id
JOIN opinions
  ON opinion_joins.opinion_id = opinions.opinion_id
JOIN clusters
  ON opinions.cluster_id = clusters.cluster_id
JOIN dockets
  ON clusters.docket_id = dockets.docket_id
JOIN courts
  ON dockets.court_id = courts.court_id
WHERE courts.court_name = 'Second Circuit'
ORDER BY judge_name ASC;
```
