# CLAUDE.md - Analysis Setup Repository

Configuration files for Claude Code business analysis at Elation Health.

## Repository Contents

```
claude-analysis-setup/
├── CLAUDE.md              # This file
├── README.md              # Quick start instructions
├── SETUP_GUIDE.md         # Full setup guide (IT tickets, manual steps)
├── settings.json          # Claude Code settings (copy to ~/.claude/)
├── commands/
│   └── analysis.md        # /analysis command
└── skills/
    └── snowflake-query.md # Snowflake query skill
```

## Setup

1. Copy `settings.json` to `~/.claude/settings.json`
2. Copy `commands/*.md` to `~/.claude/commands/`
3. Copy `skills/*.md` to `~/.claude/skills/`
4. Replace `YOUR_USERNAME` placeholders with your macOS username
5. Replace credential placeholders with your actual values

---

## Analysis Principles

### Investigation Before Analysis
- **Query actual data first** - Don't assume field semantics; run `DESCRIBE TABLE` before writing queries
- **Verify column names** - Never guess column names; they vary between tables
- **Check data freshness** - Know when tables were last updated before drawing conclusions
- **Compare to known sources** - Cross-check results against existing dashboards for sanity

### Scenario-Based Validation
- **Define expected results** - Before running complex analysis, predict what you expect to find
- **Test edge cases** - Check NULLs, zeros, negative values, date boundaries
- **Validate totals** - Filtered subsets cannot exceed total population
- **Document assumptions** - Mark all assumptions with ⚠️ in reports

### Simplification Principle
- **Break complex queries into steps** - Store intermediate results, then combine
- **Use simple aggregations first** - Start with COUNT(*), then add complexity
- **Prefer explicit over clever** - Clear SQL is better than compact SQL
- **Column numbers in GROUP BY** - Reduces errors from column name changes

---

## Report Standards

### Every Report Must Include
- **Analysis Date** - When the analysis was run
- **Data Sources** - Exact table names (DATABASE.SCHEMA.TABLE)
- **Definitions** - Clear definitions for all terms and metrics
- **Executive Summary** - Lead with conclusions, not methodology
- **Queries Used** - Full SQL for reproducibility
- **Data Quality Notes** - Caveats, limitations, assumptions

### Formatting Rules
| Element | Format | Example |
|---------|--------|---------|
| Numbers | Absolute + percentage | "2,700 (53%)" |
| Increases | Arrow up | ↑17%, +340 |
| Decreases | Arrow down | ↓4.3pp |
| Rate changes | Percentage points | "↓4.3pp" (not "↓4.3%") |
| Confidence | By sample size | 30+ = HIGH, 10-29 = MED, <10 = LOW |

### Table Rules
- Every data table MUST be followed by **Key Finding:**
- Max 5-6 columns for readability
- Right-align numeric columns
- Include totals row when applicable

---

## Communication Standards

- **Be matter-of-fact and succinct** - Every word must earn its place
- **Lead with decisions** - What does the audience need to decide/do?
- **Use emojis sparingly** - OK for emphasis (⚠️ ✓ ↑ ↓)
- **File names explain content** - `2025-01-15_monthly_revenue_by_segment.md` not `analysis.md`

---

## Schema Quick Reference

### Production Tables (for analysis)
| Schema | Contents |
|--------|----------|
| IDW.SALES_CUSTOMERS | Sales/CRM data |
| IDW.SHARED | Cross-domain shared models |
| IDW.CUSTOMER_INSIGHTS | Customer analytics |
| IDW.FINANCE | Financial data |
| IDW.OPS | Operations data |

### Raw Sources
| Database | Contents |
|----------|----------|
| DATA_LAKE.HIPPO_CORE_PRODUCTION | Raw EHR data |
| FIVETRAN_LANDING_DB.SALESFORCE | Raw Salesforce |

---

## Placeholder Values

Replace these in all config files:

| Placeholder | Description |
|-------------|-------------|
| `YOUR_USERNAME` | macOS username (run `whoami`) |
| `[INFERENCE_PROFILE_ARN_*]` | AWS Bedrock inference profile ARNs |
| `[YOUR_LOOKER_CLIENT_ID]` | Looker API Client ID |
| `[YOUR_LOOKER_CLIENT_SECRET]` | Looker API Client Secret |
