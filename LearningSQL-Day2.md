> Learning log - Day 2

# Using SQL Helper Scripts to Explore Court Case Disparities

Today I explored how to use Python to run SQL queries on court case data.  
The goal is to identify disparities in outcomes by **race** or **state**, using a structured dataset similar to [CourtListener](https://www.courtlistener.com/).

---

## Python SQL Helper Script

I created a helper script `courtlistener_sql_helpers.py` with functions to:

- Connect to the database (`connect_db`)
- Run any query and return results as a list of dictionaries (`fetch_query`)
- Fetch recent cases (`recent_cases`)
- Fetch cases by jurisdiction (`cases_by_jurisdiction`)
- Summarize outcomes by jurisdiction (`outcomes_summary`)

---

## Example 1: Fetch cases in a specific state

```python
from courtlistener_sql_helpers import connect_db, cases_by_jurisdiction

# Connect to the database
conn = connect_db("courtlistener.db")

# Fetch all cases in California
california_cases = cases_by_jurisdiction(conn, "California")

for case in california_cases[:5]:  # show first 5 for brevity
    print(case)
```

### Output Example:

```bash
{'case_id': 12345, 'case_name': 'State v. Doe', 'court': 'CA Supreme Court', 'date_filed': '2024-01-15', 'outcome': 'Guilty'}
{'case_id': 12346, 'case_name': 'People v. Smith', 'court': 'CA Appellate Court', 'date_filed': '2024-01-12', 'outcome': 'Dismissed'}
...
```

## Example 2: Summarize outcomes by race

```from courtlistener_sql_helpers import fetch_query, connect_db

conn = connect_db("courtlistener.db")

# SQL query to get counts of outcomes by race
query = """
SELECT race, outcome, COUNT(*) as count
FROM court_cases
GROUP BY race, outcome
ORDER BY race, count DESC;
"""

outcome_summary = fetch_query(conn, query)

for row in outcome_summary:
    print(row)
```
### Output Example:

```{'race': 'Black', 'outcome': 'Guilty', 'count': 1200}
{'race': 'Black', 'outcome': 'Dismissed', 'count': 300}
{'race': 'White', 'outcome': 'Guilty', 'count': 950}
{'race': 'White', 'outcome': 'Dismissed', 'count': 500}
...
```

**Key insight:** This simple aggregation helps identify potential disparities in how cases are resolved for different racial groups.

## Example 3: Visualizing outcomes by state

```import matplotlib.pyplot as plt
from courtlistener_sql_helpers import fetch_query, connect_db

conn = connect_db("courtlistener.db")

# SQL query to count guilty outcomes by state
query = """
SELECT state, COUNT(*) as guilty_count
FROM court_cases
WHERE outcome = 'Guilty'
GROUP BY state
ORDER BY guilty_count DESC;
"""

results = fetch_query(conn, query)

states = [r['state'] for r in results]
counts = [r['guilty_count'] for r in results]

plt.bar(states, counts)
plt.xticks(rotation=90)
plt.title("Number of Guilty Outcomes by State")
plt.show()
```

## Summary of Day 2

- Learned how to use a Python SQL helper script to query structured court case data.
- Practiced filtering by jurisdiction, grouping by race, and aggregating outcomes.
- Saw how Python + SQL makes it easy to explore disparities and generate insights for civic tech projects.
- Next steps: add more visualizations, explore trends over time, and experiment with multi-factor queries.
