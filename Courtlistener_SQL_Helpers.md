> Script helper specific to CourtListener 

## Cheatsheet
| Column Name    | Type    | Description                        |
| -------------- | ------- | ---------------------------------- |
| `case_id`      | INTEGER | Unique identifier for the case     |
| `case_name`    | TEXT    | Name of the case                   |
| `court`        | TEXT    | Court where the case was heard     |
| `date_filed`   | DATE    | Date the case was filed            |
| `judges`       | TEXT    | Names of judges                    |
| `party_names`  | TEXT    | Names of parties involved          |
| `jurisdiction` | TEXT    | State or federal jurisdiction      |
| `outcome`      | TEXT    | Outcome or disposition of the case |

**Example Script**

```python
import sqlite3
from datetime import datetime

def connect_db(db_path="courtlistener.db"):
    """Connect to the SQLite database."""
    return sqlite3.connect(db_path)

def fetch_query(conn, query):
    """Run a SQL query and return results as a list of dictionaries."""
    cursor = conn.cursor()
    cursor.execute(query)
    columns = [col[0] for col in cursor.description]
    results = [dict(zip(columns, row)) for row in cursor.fetchall()]
    return results

def cases_by_jurisdiction(conn, jurisdiction):
    """Fetch all cases from a specific jurisdiction."""
    query = f"""
    SELECT case_id, case_name, court, date_filed, outcome
    FROM court_cases
    WHERE jurisdiction = '{jurisdiction}'
    ORDER BY date_filed DESC;
    """
    return fetch_query(conn, query)

def outcomes_summary(conn):
    """Get a summary count of case outcomes by jurisdiction."""
    query = """
    SELECT jurisdiction, outcome, COUNT(*) as count
    FROM court_cases
    GROUP BY jurisdiction, outcome
    ORDER BY jurisdiction, count DESC;
    """
    return fetch_query(conn, query)

def recent_cases(conn, limit=10):
    """Fetch the most recent N cases filed."""
    query = f"""
    SELECT case_id, case_name, court, date_filed
    FROM court_cases
    ORDER BY date_filed DESC
    LIMIT {limit};
    """
    return fetch_query(conn, query)

# Usage example
if __name__ == "__main__":
    conn = connect_db()
    
    print("Recent 5 cases:")
    for case in recent_cases(conn, 5):
        print(case)
    
    print("\nCases in California:")
    for case in cases_by_jurisdiction(conn, "California"):
        print(case)
    ```
    print("\nOutcome summary:")
    for row in outcomes_summary(conn):
        print(row)
```

## What this script does:
- Connects to a SQLite database (`courtlistener.db`) representing CourtListener-style data.
- Provides reusable functions for:
  - Fetching recent cases
  - Fetching cases by jurisdiction
  - Summarizing outcomes by jurisdiction
- Uses a helper `fetch_query` function so you don’t rewrite cursor logic every time.
