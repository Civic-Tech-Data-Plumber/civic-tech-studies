> This page is the author just testing their coding skills by creating simple scripts.

## 1

**Goals:**
1. Create database
2. Create tables and define parameters
3. Populate tables with a mock dataset
4. Perform a SELECT query

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
---

## 2

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
---

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

### How to Think About It Systematically

Ask three questions:

1. Is the child conceptually meaningless without the parent?
→ Use CASCADE.

2. Is the relationship optional or historically preservable?
→ Use SET NULL.

3. Is the parent foundational and rarely deleted?
→ Use RESTRICT.

---


# 3
**Goal:** Create sample `SELECT` queries for the above tables  
(assuming mock data has already been provided)

```sql
SELECT
      CONCAT_WS(' ', court.judge_fname, court.judge_lname) AS "Judge Name",
      CONCAT_WS(' v ', cases.plaintiff, cases.defendant)   AS "Case Name",
      cases.case_date                                      AS "Case Date"
      
FROM court
JOIN cases ON cases.judge_id = court.judge_id

WHERE cases.date = '2025-09-02 10:35:00' 

ORDER BY court.judge_id ASC;
```
