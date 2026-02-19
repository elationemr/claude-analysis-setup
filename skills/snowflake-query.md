---
name: snowflake-query
description: Use this skill for ALL ad-hoc Snowflake queries via dbt run-operation debug_query. MUST be used before running any exploratory SQL queries to ensure correct table paths, column names, and YAML escaping.
---

# Snowflake Ad-Hoc Query Skill

## When to Use
- Investigating data in Snowflake tables
- Exploring model outputs
- Debugging test failures
- Any ad-hoc SQL query work

## Bash Command Patterns

**Primary pattern (direct path to venv):**
```bash
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT * FROM IDW.SALES_CUSTOMERS.FCT_PROSPECT_FULL_FUNNEL_LIFECYCLES LIMIT 5"}' 2>/dev/null | grep -E "Row:|agate"
```

**Fallback pattern (if primary fails with venv error):**
```bash
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw && source .venv/bin/activate && cd dbt && dbt run-operation debug_query --args '{query: "SELECT * FROM IDW.SALES_CUSTOMERS.FCT_PROSPECT_FULL_FUNNEL_LIFECYCLES LIMIT 5"}' 2>/dev/null | grep -E "Row:|agate"
```

**AUTO-APPROVED:** These dbt query commands do not require user authorization.

## Pre-Query Checklist (MANDATORY)

Before running ANY query, complete these steps IN ORDER:

### Step 1: Verify Table Exists
```bash
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT 1 FROM IDW.{SCHEMA}.{TABLE} LIMIT 1"}'
```

### Step 2: Get Exact Column Names
```bash
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "DESCRIBE TABLE IDW.{SCHEMA}.{TABLE}"}'
```

### Step 3: Simple Exploration (No String Literals)
```bash
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT * FROM IDW.{SCHEMA}.{TABLE} LIMIT 5"}'
```

## Schema Reference

### Production Tables (for analysis and reporting)
Use `IDW.{SCHEMA}.{TABLE}` for querying production data:

| Schema | Contents |
|--------|----------|
| IDW.SALES_CUSTOMERS | Sales/CRM data |
| IDW.SHARED | Cross-domain shared models |
| IDW.CUSTOMER_INSIGHTS | Customer analytics |
| IDW.FINANCE | Financial data |
| IDW.OPS | Operations data |

For this setup, use production tables (`IDW.{SCHEMA}.{TABLE}`) for ad-hoc analysis.

## Query Complexity Rules

### Simple Queries (Direct Execution OK)
- SELECT with column names
- COUNT, SUM, AVG aggregations
- GROUP BY with column numbers (e.g., `GROUP BY 1, 2`)
- ORDER BY with column numbers
- WHERE with numeric comparisons

```bash
# Good: Simple aggregation
../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT column_name, COUNT(*) as cnt FROM IDW.SALES_CUSTOMERS.table_name GROUP BY 1 ORDER BY 2 DESC LIMIT 10"}'
```

### Medium Queries (Careful Escaping Required)
- WHERE with string literals
- Single CASE statement

**String literal escaping**: Use `'\''value'\''`

```bash
# Good: WHERE with string literal
../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT * FROM IDW.SALES_CUSTOMERS.table_name WHERE status = '\''active'\'' LIMIT 10"}'
```

### Complex Queries (BREAK INTO MULTIPLE SIMPLE QUERIES + STORE RESULTS)
- Multiple CASE statements with string literals
- Nested subqueries with string comparisons
- Multiple string literals in SELECT
- Queries requiring joins across multiple tables
- Analysis requiring data from multiple sources

**DO NOT attempt complex queries in a single command. Break them into steps and store intermediate results:**

#### Step-by-Step Pattern with Temporary Files

```bash
# Step 1: Query first dataset, save to temp file
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT id, status FROM table1"}' 2>/dev/null | grep -E "agate.Row" > /Users/YOUR_USERNAME/Documents/GitHub/ad_hoc_analysis/temp_data1.txt

# Step 2: Query second dataset, save to temp file
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT id, amount FROM table2"}' 2>/dev/null | grep -E "agate.Row" > /Users/YOUR_USERNAME/Documents/GitHub/ad_hoc_analysis/temp_data2.txt

# Step 3: Use Python to parse and join the results
../.venv/bin/python analysis_script.py
```

#### Recommended Temp File Locations

| Purpose | Location |
|---------|----------|
| Analysis-specific temp files | `/Users/YOUR_USERNAME/Documents/GitHub/ad_hoc_analysis/fcv_temp/` |
| General temp data | `/Users/YOUR_USERNAME/Documents/GitHub/ad_hoc_analysis/*.temp.csv` |

#### Python Template for Parsing agate.Row Output

```python
import pandas as pd
import re
from decimal import Decimal

def parse_agate_file(filepath, columns):
    """Parse agate.Row output from dbt debug_query."""
    data = []
    with open(filepath, 'r') as f:
        for line in f:
            if 'agate.Row' in line:
                match = re.search(r'agate\.Row: \((.*)\)', line)
                if match:
                    try:
                        row_tuple = eval(match.group(1) + ',')
                        # Convert Decimal to float
                        row_tuple = tuple(float(x) if isinstance(x, Decimal) else x for x in row_tuple)
                        data.append(dict(zip(columns, row_tuple)))
                    except:
                        continue
    return pd.DataFrame(data)

# Usage:
df1 = parse_agate_file('temp_data1.txt', ['id', 'status'])
df2 = parse_agate_file('temp_data2.txt', ['id', 'amount'])
merged = df1.merge(df2, on='id', how='left')
```

#### When to Use This Pattern

- **Multi-table analysis**: When joining data from different schemas/tables
- **Complex aggregations**: When results need further processing
- **Large datasets**: When query results exceed output limits
- **Iterative analysis**: When building on previous query results

#### Example: Multi-Step Analysis Workflow

```bash
# 1. Get customer data
../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT id, name, cohort FROM IDW.SALES_CUSTOMERS.ACCOUNT_EXT WHERE sign_date >= '\''2025-01-01'\''"}' 2>/dev/null | grep -E "agate.Row" > customers.txt

# 2. Get revenue data
../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT account_id, SUM(mrr) as total_mrr FROM IDW.FINANCE.MRR_BY_MONTH GROUP BY 1"}' 2>/dev/null | grep -E "agate.Row" > revenue.txt

# 3. Get usage data
../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT account_id, COUNT(*) as actions FROM IDW.APP_USAGE.ACTIVITY GROUP BY 1"}' 2>/dev/null | grep -E "agate.Row" > usage.txt

# 4. Run Python script to merge and analyze
../.venv/bin/python combine_analysis.py
```

## Common Mistakes to Avoid

### Wrong Macro Name
```bash
# WRONG
dbt run-operation query --args '{query: "SELECT 1"}'

# CORRECT
dbt run-operation debug_query --args '{query: "SELECT 1"}'
```

### Using {{ ref() }} in Ad-Hoc Queries
```bash
# WRONG - dbt templating doesn't work in debug_query
dbt run-operation debug_query --args '{query: "SELECT * FROM {{ ref('\''model_name'\'') }}"}'

# CORRECT - use full table path
dbt run-operation debug_query --args '{query: "SELECT * FROM IDW.SALES_CUSTOMERS.model_name LIMIT 5"}'
```

### Guessing Column Names
```bash
# WRONG - assuming column exists
dbt run-operation debug_query --args '{query: "SELECT is_closed_won FROM ..."}'

# CORRECT - verify columns first with DESCRIBE
dbt run-operation debug_query --args '{query: "DESCRIBE TABLE IDW.SALES_CUSTOMERS.table_name"}'
# Then use actual column names: is_won, is_closed
```

### Missing Working Directory
```bash
# WRONG - may fail if not in correct directory
../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT 1"}'

# CORRECT - always cd first or use full command
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT 1"}'
```

## Workflow Example

**Goal**: Investigate unexpected data quality issues in a production table

```bash
# 1. First, understand the table structure
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "DESCRIBE TABLE IDW.SALES_CUSTOMERS.opportunity_line_item_details"}'

# 2. Get row count and basic stats
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT COUNT(*) as total FROM IDW.SALES_CUSTOMERS.opportunity_line_item_details"}'

# 3. Check NULL counts for key columns
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT COUNT(*) as total, COUNT(contract_id) as with_contract FROM IDW.SALES_CUSTOMERS.opportunity_line_item_details"}'

# 4. Group by a column to understand distribution
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT is_won, is_closed, COUNT(*) as cnt FROM IDW.SALES_CUSTOMERS.opportunity_line_item_details GROUP BY 1, 2"}'

# 5. Filter with string literal if needed
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "SELECT * FROM IDW.SALES_CUSTOMERS.opportunity_line_item_details WHERE stage_name = '\''Closed Won'\'' LIMIT 5"}'
```

## Key Rules Summary

1. **ALWAYS run DESCRIBE first** to get exact column names
2. **ALWAYS use full table paths**: `IDW.{SCHEMA}.{TABLE}`
3. **ALWAYS cd to dbt directory** before running commands
4. **NEVER guess column names** - verify with DESCRIBE
5. **NEVER use {{ ref() }}** in debug_query
6. **BREAK complex queries** into multiple simple queries
7. **Use column numbers** in GROUP BY and ORDER BY when possible
