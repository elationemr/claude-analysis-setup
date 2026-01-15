---
description: "Business analysis across Elation's data ecosystem"
argument-hint: "<analysis request in natural language>"
allowed-tools: ["Read", "Write", "Edit", "Bash", "Glob", "Grep", "Task", "WebFetch", "TodoWrite", "mcp__context7__resolve-library-id", "mcp__context7__get-library-docs"]
---

# Elation Business Analysis

Analyze the user's request and produce a comprehensive, auditable markdown report.

**User's Request:** $ARGUMENTS

---

## CRITICAL RULES

1. **NEVER fabricate data** - Every number must come from a query result
2. **NEVER present insights without supporting query results** - Show your work
3. **If data is unavailable, say so explicitly** - Don't estimate or guess
4. **Every claim must be independently verifiable** - Include exact SQL queries
5. **ONLY execute SELECT queries** - No INSERT/UPDATE/DELETE/DROP

---

## Workflow

### Step 1: Understand the Request

Identify:
- Business question being asked
- Metrics/dimensions needed
- Time range and filters

### Step 2: Schema Discovery

**When to explore:** If you don't already know the exact tables/columns needed from prior context.

**Skip exploration if:** User provided specific table references or you have context from recent conversation.

**To explore:** Use Task tool with `subagent_type: "Explore"`:
```
Find tables and columns for [TOPIC].

Search IN ORDER:
1. Looker: /Users/YOUR_USERNAME/Documents/GitHub/internal_idw/ (dashboards/, views/, docs/AI_GUIDE.md)
2. DBT: /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt/models/ (schema.yml files)
3. Raw sources: FIVETRAN_LANDING_DB.SALESFORCE.*, DATA_LAKE.HIPPO_CORE_PRODUCTION.*

Return: Table names (database.schema.table), relevant columns, join keys, important filters.
```

### Step 3: Execute Queries

**📖 Full reference:** `~/.claude/skills/snowflake-query.md`

**Command pattern:**
```bash
cd /Users/YOUR_USERNAME/Documents/GitHub/snowflake_idw/dbt && ../.venv/bin/dbt run-operation debug_query --args '{query: "YOUR SQL HERE"}' 2>/dev/null | grep -E "Row:|agate"
```

**String escaping:** Use `'\''value'\''` for SQL strings

**Key rules:**
1. Run `DESCRIBE TABLE` first to verify column names
2. Use production tables: `IDW.{SCHEMA}.{TABLE}`
3. Break complex queries into simple steps
4. Use column numbers in GROUP BY/ORDER BY

**Schema reference:**
| Schema | Contents |
|--------|----------|
| IDW.SALES_CUSTOMERS | Sales/CRM data |
| IDW.SHARED | Cross-domain shared models |
| IDW.CUSTOMER_INSIGHTS | Customer analytics |
| DATA_LAKE.HIPPO_CORE_PRODUCTION | Raw EHR data |
| FIVETRAN_LANDING_DB.SALESFORCE | Raw Salesforce |

### Step 4: Generate Report

**Output location:** `/Users/YOUR_USERNAME/Documents/GitHub/ad_hoc_analysis/YYYY-MM-DD_<brief-description>.md`

**After writing, open in Cursor with preview:**
```bash
cursor "/path/to/report.md" && sleep 1 && osascript -e 'tell application "System Events" to keystroke "k" using {command down, shift down}' -e 'delay 0.2' -e 'tell application "System Events" to keystroke "v"'
```

---

## Data Quality Checks (Before Finalizing)

1. **Sanity check totals** - Filtered subset cannot exceed total population
2. **Verify filter propagation** - Filters must apply in ALL downstream CTEs
3. **Complete data** - No partial results ("15 of 25") or missing values ("-")
4. **Define ambiguous terms** - Every metric needs a clear definition
5. **Cross-check** - Compare against existing dashboards for large discrepancies

---

## Report Structure

```markdown
# [Analysis Title]

**Analysis Date:** YYYY-MM-DD
**Data Sources:** `DB.SCHEMA.TABLE`, ...

---

## Definitions

| Term | Definition |
|------|------------|
| [Term] | [Precise definition] |

---

## Executive Summary

### Bottom Line Up Front
[1-2 sentences: ACTIONABLE conclusion. Lead with insights, not methodology.]

### Key Decisions Required
| # | Decision/Action | Supporting Data | Confidence |
|---|-----------------|-----------------|------------|
| 1 | [Action] | [Metric] | HIGH/MED/LOW |

### Critical Findings
1. **[Title]:** [One sentence with numbers + business implication]

---

## [Analysis Sections]

**Section Takeaway:** [Conclusion first, then evidence]

[Data tables with **Key Finding:** after each]

---

## Actionable Target Lists (when applicable)

| Rank | [Dimension] | [Entity] | [Metric 1] | [Metric 2] |
|-----:|-------------|----------|----------:|----------:|

---

## Recommendations

### 1. [Action] (CONFIDENCE)
- **Data:** Evidence
- **Action:** Steps
- **Impact:** Quantified outcome

---

## Queries Used
```sql
-- Query name
SELECT ...
```

---

## Data Quality Notes
1. [Caveats and methodology choices]

---
*Analysis completed by: Claude Code - YYYY-MM-DD*
```

---

## Formatting Standards

| Element | Format | Example |
|---------|--------|---------|
| Numbers | Absolute + percentage | "2,700 (53%)" |
| Increases | Arrow up | ↑17%, +340 |
| Decreases | Arrow down | ↓4.3pp |
| Rate changes | Percentage points | "↓4.3pp" (not "↓4.3%") |
| Comparisons | With context | "vs Q3", "vs 4.27% avg" |
| Confidence | By sample size | 30+ = HIGH, 10-29 = MED, <10 = LOW |

**Tables:** Every table MUST be followed by **Key Finding:**

**Time comparisons:** Always include YoY or period-over-period when possible. Note if periods aren't directly comparable.

**Statistics:** Prefer percentiles (median, P75, P90, P95) over averages for skewed distributions.

---

## Marking Assumptions

```markdown
> ⚠️ **Assumption:** [What you're assuming]
> **Rationale:** [Why reasonable]
> **Impact if wrong:** [How conclusions change]
```

---

## Reference Examples

- `/Users/YOUR_USERNAME/Documents/GitHub/ad_hoc_analysis/2025-12-26_tier1_connect_individual_physician_analysis_corrected.md`
- `/Users/YOUR_USERNAME/Documents/GitHub/ad_hoc_analysis/connect_usage_analysis_20251219/`
