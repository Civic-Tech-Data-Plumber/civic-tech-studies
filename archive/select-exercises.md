> This page is the author just testing their coding skills by creating simple scripts, creating notes, and showing the corrected code.

# Exercises Testing SELECT Queries

<details>
<summary> Tables used for our example SELECT exercises</summary>
  
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

<details>
<summary>Schema</summary>

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
</details>
</details>

---

## 🧪 Exercise 1 — Simple Author Filter

Return:

* case_name
* judge_name
* cite_count

For opinions authored by a judge named **"Ruth Bader Ginsburg"**.  
Order by cite_count descending.

---

## 🧪 Exercise 2 — Single-Hop Court Filter

Return:

* case_name
* docket_number
* date_filed

For clusters from the court named **"Ninth Circuit"**.  
Order by date_filed ascending.

---

## 🧪 Exercise 3 — Panel Only (No Court Filter)

Return:

* case_name
* judge_name

For all judges who were **on a panel** (use opinion_joins).  
Order by case_name alphabetically.

(No court filtering this time. Just focus on the bridge table.)

---

## 🧪 Exercise 4 — Cite Threshold + Court

Return:

* case_name
* cite_count

For opinions with cite_count >= 15  
Only from the court named **"Supreme Court"**.  
Order by cite_count descending.

---

## 🧪 Exercise 5 — Source + Author

Return:

* case_name
* judge_name
* source_code

For opinions authored by judges  
Where source_code = 'CRU'.  
Order alphabetically by judge_name.  
