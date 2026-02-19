# Claude Code Analysis Setup

Configuration files for Claude Code business analysis at Elation Health.

## Quick Start

1. Follow the [Setup Guide](SETUP_GUIDE.md) to complete IT tickets and manual setup
2. Copy these files to your Claude Code config:

```bash
# Create directories
mkdir -p ~/.claude/commands ~/.claude/skills

# Copy files
cp settings.json ~/.claude/
cp commands/analysis.md ~/.claude/commands/
cp skills/snowflake-query.md ~/.claude/skills/
```

3. Replace placeholders in all files (see below)

## Files

| File | Destination | Purpose |
|------|-------------|---------|
| `settings.json` | `~/.claude/settings.json` | Claude Code configuration |
| `commands/analysis.md` | `~/.claude/commands/` | `/analysis` command |
| `skills/snowflake-query.md` | `~/.claude/skills/` | Snowflake query patterns |

## Placeholders to Replace

After copying, update these values in all files:

| Placeholder | Replace With | How to Find |
|-------------|--------------|-------------|
| `YOUR_USERNAME` | Your macOS username | Run `whoami` in Terminal |
| `[INFERENCE_PROFILE_ARN_DEFAULT]` | Bedrock default ARN | From IT/Data Platform |
| `[INFERENCE_PROFILE_ARN_SONNET]` | Bedrock Sonnet ARN | From IT/Data Platform |
| `[INFERENCE_PROFILE_ARN_HAIKU]` | Bedrock Haiku ARN | From IT/Data Platform |
| `[YOUR_LOOKER_CLIENT_ID]` | Looker API Client ID | From Analytics team |
| `[YOUR_LOOKER_CLIENT_SECRET]` | Looker API Client Secret | From Analytics team |

## Usage

After setup, run analyses with:

```bash
# Start Claude Code
clc

# Run an analysis
/analysis "How many active accounts do we have by segment?"
```

## Related Repositories

- `snowflake_idw` - DBT models (data transformations)
- `internal_idw` - Looker dashboards and views

## Support

- **Snowflake issues:** [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)
- **Looker questions:** #analytics
- **AWS/Bedrock access:** #it-support
