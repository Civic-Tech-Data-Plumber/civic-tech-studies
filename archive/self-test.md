> This page is the author just testing their coding skills by creating a simple script.

```sql

CREATE DATABASE IF NOT EXISTS recent_cases;

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
date         DATETIME NOT NULL,
FOREIGN KEY (judge_id) REFERENCES court(judge_id)
  ON DELETE RESTRICT
  ON UPDATE CASCADE
) ENGINE=InnoDB;

