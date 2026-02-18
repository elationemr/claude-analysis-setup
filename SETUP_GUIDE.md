# Claude Code Analysis Setup Guide - Elation Health

Set up Claude Code for business analysis with Snowflake, dbt, and Looker.

**Audience:** Non-technical users with no command line experience. Claude Code (an AI assistant) will guide you through most steps.

---

## How This Guide Works

| Phase | What | Timeline |
|-------|------|----------|
| **Phase 0** | Submit IT support tickets | Do first - takes 1-2 business days |
| **Phase 1** | Manual setup steps | While waiting for IT |
| **Phase 2** | Let Claude Code complete configuration | After IT responds |

**Total active time:** ~30 minutes (spread across 1-2 days while waiting for IT)

---

## Phase 0: IT Support Tickets (Submit First!)

Before starting technical setup, you need access credentials from IT. Submit these tickets **now** - they may take 1-2 business days.

---

### Ticket 1: AWS Bedrock Access (Required)

**Submit to:** IT Support (#it-support or your IT ticketing system)

**Subject:** Request AWS Bedrock Access for Claude Code

**Description:**
```
Hi IT Team,

I need AWS Bedrock access to use Claude Code for business analysis.

Please add me to the "AWS - AI Playground" Okta push group.

Thank you!
```

**What you'll receive:**
- Confirmation you've been added to the Okta group
- AWS SSO profile name: `AIPlayground`

---

### Ticket 2: Snowflake Access (Required)

**General Questions:** Data Platform ([#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC))

#### IMPORTANT: Choose the scenario that matches your needs

#### Scenario A: Business Analysis Only (most common)

Request access in [#ask-it](https://elation.slack.com/archives/CABPVMDHP): `SNOWFLAKE - TEAM_PRODUCT_MANAGEMENT`

#### Scenario B: dbt Development (uncommon)

Request access in [#ask-it](https://elation.slack.com/archives/CABPVMDHP): `SNOWFLAKE - DEVELOPER`

**After access is provisioned, confirm you received and verified:**

- Snowflake username (e.g., `JANEDOE`) from your profile page: https://app.snowflake.com/elationhealth/ehdw/settings/profile
  - Username convention: `FIRSTNAMELASTNAME`
- Confirmation of your default role and default warehouse from preferences: https://app.snowflake.com/elationhealth/ehdw/settings/preferences
  - If you requested `SNOWFLAKE - TEAM_PRODUCT_MANAGEMENT`:
    - Default Role: `SNOWFLAKE - TEAM_PRODUCT_MANAGEMENT`
    - Default Warehouse: `TEAM_PM_WH`
  - If you requested `SNOWFLAKE - DEVELOPER`:
    - Default Role: `SNOWFLAKE - DEVELOPER`
    - Default Warehouse: `DBT_WH`
- Dev schema guidance (dbt development only):
  - Database access: `DEV_IDW`
  - Dev schema convention: `FIRSTNAME_DEV`

---

### Ticket 3: Looker API Credentials (Required)

**Submit to:** Analytics Team (#analytics on Slack)

**Subject:** Request Looker API Credentials for Claude Code

**Description:**
```
Hi Analytics Team,

I need Looker API credentials to enable Claude Code to search dashboards and explore data definitions.

Please either:
1. Generate API credentials for me, OR
2. Give me instructions to generate them myself at https://elationhealth.looker.com

Thank you!
```

**What you'll receive:**
- Looker Client ID
- Looker Client Secret
- Instructions to access: https://elationhealth.looker.com > Admin > Users > API Keys

---

### Ticket 4: 1Password Vault Access (If not already set up)

**Submit to:** IT Support (#it-support)

**Subject:** 1Password CLI Setup for Development

**Description:**
```
Hi IT Team,

I need to use 1Password CLI to securely store API credentials for local development tools.

Please confirm:
1. I can install 1Password CLI (brew install --cask 1password-cli)
2. I can create a personal vault called "LocalDev-APIKeys" for storing API tokens
3. My 1Password account has CLI integration enabled

Thank you!
```

**What you'll receive:**
- Confirmation you can use 1Password CLI
- Any required configuration steps

---

### Ticket 5: Bedrock Inference Profiles (Required - submit after Ticket 1 is complete)

**Submit to:** Infrastructure Team (#team_infra on Slack)

**Subject:** Request Bedrock Inference Profile ARNs for Claude Code

**Description:**
```
Hi Infra Team,

I've been added to the AWS - AI Playground Okta group and need personal Bedrock inference profile ARNs to use Claude Code.

Please create inference profiles for me in us-west-2 for:
- Claude Sonnet (default model)
- Claude Haiku (fast/cheap model)

Thank you!
```

**What you'll receive:**
- 2-3 inference profile ARNs (strings starting with `arn:aws:bedrock:us-west-2:...`)

**Note:** You can start Phase 1 while waiting for these ARNs. Phase 2 requires all credentials including inference profiles.

---

## Credentials Checklist

After IT responds, fill in these values as you receive them:

| Item | Value | Received? | Ticket |
|------|-------|-----------|--------|
| AWS SSO Profile | `AIPlayground` | ☐ | #1 |
| Snowflake Username | _________________ | ☐ | #2 |
| Snowflake Dev Schema | _________________ | ☐ | #2 |
| Looker Client ID | _________________ | ☐ | #3 |
| Looker Client Secret | _________________ | ☐ | #3 |
| Inference Profile ARN (default/sonnet) | _________________ | ☐ | #5 |
| Inference Profile ARN (haiku) | _________________ | ☐ | #5 |

**Important:** You need ALL credentials before starting Phase 2. Claude Code cannot run without inference profile ARNs.

---

## Phase 1: Manual Setup (While Waiting for IT)

### Step 1: Open Terminal

1. Press `Cmd + Space` to open Spotlight
2. Type `Terminal`
3. Press Enter

A window with a command prompt will appear. This is where you'll type commands.

**Tip:** Copy commands from this guide and paste into Terminal with `Cmd + V`.

---

### Step 2: Install Homebrew

Homebrew is a tool that helps install other software. Copy and paste this command:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Press Enter and follow any prompts. This may take a few minutes.

**Important:** After installation, Homebrew may show you commands to run. Copy and paste those commands too.

Verify it worked:
```bash
brew --version
```

You should see a version number like `Homebrew 4.x.x`.

---

### Step 3: Install Python and Node.js

Run this command:

```bash
brew install python@3.12 node@20
```

Verify:
```bash
python3 --version
node --version
```

---

### Step 4: Install Claude Code and AWS CLI

Run:

```bash
npm install -g @anthropic-ai/claude-code
brew install awscli
```

Verify:
```bash
claude --version
aws --version
```

---

### Step 5: Clone the Setup Files

Create a folder for your work:

```bash
mkdir -p ~/Documents/GitHub
cd ~/Documents/GitHub
```

Download the setup files:

```bash
git clone git@github.com:elationemr/claude-analysis-setup.git
git clone git@github.com:elationemr/snowflake_idw.git
git clone git@github.com:elationemr/internal_idw.git
```

---

### Step 6: Generate Snowflake Keys

Run these commands to create your authentication keys:

```bash
mkdir -p ~/.ssh
openssl genrsa 2048 | openssl pkcs8 -topk8 -inform PEM -out ~/.ssh/snowflake_private_key.p8 -nocrypt
openssl rsa -in ~/.ssh/snowflake_private_key.p8 -pubout -out ~/.ssh/snowflake_public_key.pub
chmod 600 ~/.ssh/snowflake_private_key.p8
```

**Important:** Now send your public key to [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC):

```bash
cat ~/.ssh/snowflake_public_key.pub
```

Copy the output and send it with this message:
> "Here's my public key for Snowflake access. Please register it with my user account. [paste key here]"

---

## Phase 2: Claude-Assisted Setup (After ALL Tickets Complete)

You need ALL credentials before starting this phase, including your inference profile ARNs from #team_infra (Ticket #5).

### Step 1: Configure AWS SSO

First, log into AWS:

```bash
aws sso login --profile AIPlayground
```

This opens a browser window - log in with your Okta credentials.

### Step 2: Start Claude Code

Run this command, replacing the placeholder ARNs with your actual inference profile ARNs from #team_infra:

```bash
cd ~/Documents/GitHub/claude-analysis-setup
AWS_PROFILE=AIPlayground AWS_REGION=us-west-2 CLAUDE_CODE_USE_BEDROCK=1 \
  ANTHROPIC_MODEL="[YOUR_DEFAULT_INFERENCE_PROFILE_ARN]" \
  ANTHROPIC_SMALL_FAST_MODEL="[YOUR_HAIKU_INFERENCE_PROFILE_ARN]" \
  claude
```

**Example with real ARNs:**
```bash
cd ~/Documents/GitHub/claude-analysis-setup
AWS_PROFILE=AIPlayground AWS_REGION=us-west-2 CLAUDE_CODE_USE_BEDROCK=1 \
  ANTHROPIC_MODEL="arn:aws:bedrock:us-west-2:303970654249:application-inference-profile/abc123xyz" \
  ANTHROPIC_SMALL_FAST_MODEL="arn:aws:bedrock:us-west-2:303970654249:application-inference-profile/def456uvw" \
  claude
```

---

### Step 3: Copy This Entire Prompt Into Claude Code:

```
I'm a non-technical user setting up Claude Code for data analysis. I've completed the manual setup:
- Installed Homebrew, Python 3.12, Node.js 20, AWS CLI
- Installed Claude Code
- Cloned repos: claude-analysis-setup, snowflake_idw, internal_idw
- Generated Snowflake keys (sent public key to #team_data_platform)

I have received these credentials from IT:
- AWS SSO Profile: AIPlayground
- Snowflake Username: [FILL IN - e.g., JANEDOE]
- Snowflake Dev Schema: [FILL IN - e.g., jane_dev]
- Looker Client ID: [FILL IN]
- Looker Client Secret: [FILL IN]
- Inference Profile ARN (default/sonnet): [FILL IN - from #team_infra]
- Inference Profile ARN (haiku): [FILL IN - from #team_infra]

Please complete my setup. For each step:
1. Explain what you're doing in simple terms
2. Run the commands for me
3. Tell me if I need to do anything
4. Confirm it worked before moving on

## Setup Steps to Complete:

### 1. Create output folder
Create ~/Documents/GitHub/ad_hoc_analysis

### 2. Set up dbt (data tool)
- Create Python environment in snowflake_idw folder
- Install dbt-snowflake and pandas packages
- Verify dbt works

### 3. Install 1Password CLI
- Install via: brew install --cask 1password-cli
- Help me sign in: op signin
- Tell me how to enable CLI in 1Password app:
  1. Open 1Password app
  2. Go to Settings (gear icon)
  3. Click "Developer" in sidebar
  4. Enable "Integrate with 1Password CLI"
  5. Enable "Touch ID" for CLI authentication

### 4. Create 1Password vault and store credentials
Guide me to create items in 1Password following this EXACT structure:

#### Create vault
- Name: `LocalDev-APIKeys`

#### Create "Looker" item (type: API Credential)
Standard fields used:
- username: (leave empty or note)
- credential: (leave empty or note)
Custom fields to add:
- client-secret: [MY_LOOKER_CLIENT_SECRET]

**Note:** Snowflake authentication uses key-pair (private key file), not password, so no Snowflake item is needed in 1Password.

### 5. Configure shell environment
Create/update my ~/.zshrc file using the template provided below, replacing placeholder values with my actual credentials.

### 6. Copy Claude config files
- Create ~/.claude/commands and ~/.claude/skills folders
- Copy settings.json from claude-analysis-setup to ~/.claude/
- Copy analysis.md from claude-analysis-setup/commands/ to ~/.claude/commands/
- Copy snowflake-query.md from claude-analysis-setup/skills/ to ~/.claude/skills/
- Update all files: replace YOUR_USERNAME with my macOS username (find with: whoami)
- Update all files: replace YOUR_DEV_SCHEMA with my Snowflake dev schema
- Update settings.json: replace [INFERENCE_PROFILE_ARN_DEFAULT] and [INFERENCE_PROFILE_ARN_SONNET] with my default/sonnet ARN
- Update settings.json: replace [INFERENCE_PROFILE_ARN_HAIKU] with my haiku ARN

### 7. Create dbt profile
Create ~/.dbt/profiles.yml with my credentials.

### 8. Test AWS SSO login
Run: aws sso login --profile AIPlayground
This will open a browser - I need to log in with my work credentials.

### 9. Test everything
- Test AWS: aws sts get-caller-identity --profile AIPlayground
- Test Snowflake connection: dbt debug
- Test 1Password: load_api_keys
- Test Claude Code: clc (should start Claude with Bedrock)
- Run a simple /analysis command

Let's start with step 1!
```

---

## Shell Configuration Template (~/.zshrc)

Claude Code will help you create this file with your actual values:

```bash
export PATH="/opt/homebrew/bin:$PATH"

# Google Cloud SDK (if installed)
if [ -f '/Users/[MY_USERNAME]/Documents/google-cloud-sdk/path.zsh.inc' ]; then . '/Users/[MY_USERNAME]/Documents/google-cloud-sdk/path.zsh.inc'; fi
if [ -f '/Users/[MY_USERNAME]/Documents/google-cloud-sdk/completion.zsh.inc' ]; then . '/Users/[MY_USERNAME]/Documents/google-cloud-sdk/completion.zsh.inc'; fi

export PATH=~/.npm-global/bin:$PATH

# ===========================================
# API Credentials (loaded from 1Password)
# ===========================================
# Uses personal auth with Touch ID - works offline after initial sync

load_api_keys() {
  # Looker
  export LOOKER_CLIENT_SECRET="$(op read 'op://LocalDev-APIKeys/Looker/client-secret' 2>/dev/null)"
  echo "✓ API keys loaded from 1Password"
}

# Lazy-load API keys on first interactive command
_lazy_load_api_keys() {
  if [[ -z "$LOOKER_CLIENT_SECRET" ]]; then
    echo "🔐 Loading API keys from 1Password... (Ctrl+C to skip)"
    load_api_keys
  fi
  add-zsh-hook -d precmd _lazy_load_api_keys
}

# Only set up hook in interactive shells with real TTY (skip IDE background shells)
if [[ $- == *i* ]] && [[ -t 0 ]] && [[ -z "$LOOKER_CLIENT_SECRET" ]] && [[ -z "$VSCODE_INJECTION" ]] && [[ "$TERM_PROGRAM" != "vscode" ]]; then
  autoload -Uz add-zsh-hook
  add-zsh-hook precmd _lazy_load_api_keys
fi

# ===========================================
# Non-secret configuration values
# ===========================================

# Looker
export LOOKER_BASE_URL="https://elationhealth.looker.com"
export LOOKER_CLIENT_ID="[MY_LOOKER_CLIENT_ID]"
export LOOKER_VERIFY_SSL="true"

# Snowflake
export SNOWFLAKE_ACCOUNT="elationhealth-ehdw"
export SNOWFLAKE_USER="[MY_SNOWFLAKE_USERNAME]"
export SNOWFLAKE_WAREHOUSE="DBT_WH"
export SNOWFLAKE_DATABASE="DEV_IDW"
export SNOWFLAKE_SCHEMA="[MY_DEV_SCHEMA]"
export SNOWFLAKE_ROLE="TEAM_PRODUCT_MANAGEMENT"

# AWS credentials for Bedrock
export AWS_PROFILE="[MY_AWS_SSO_PROFILE]"
export AWS_REGION="us-west-2"

# ===========================================
# Claude Code launcher function
# ===========================================

launch_claude() {
  aws sso login --profile [MY_AWS_SSO_PROFILE]
  AWS_PROFILE=[MY_AWS_SSO_PROFILE] AWS_REGION=us-west-2 CLAUDE_CODE_USE_BEDROCK=1 \
    ANTHROPIC_MODEL="[INFERENCE_PROFILE_ARN_DEFAULT]" \
    ANTHROPIC_SONNET_MODEL="[INFERENCE_PROFILE_ARN_SONNET]" \
    ANTHROPIC_SMALL_FAST_MODEL="[INFERENCE_PROFILE_ARN_HAIKU]" \
    claude
}

alias clc='launch_claude'
export PATH="$HOME/.npm-global/bin:$PATH"
```

---

## dbt Profile Template (~/.dbt/profiles.yml)

```yaml
elation_health_snowflake:
  target: dev
  outputs:
    dev:
      type: snowflake
      account: elationhealth-ehdw
      user: [MY_SNOWFLAKE_USERNAME]
      private_key_path: ~/.ssh/snowflake_private_key.p8
      role: TEAM_PRODUCT_MANAGEMENT
      database: DEV_IDW
      warehouse: DBT_WH
      schema: [MY_DEV_SCHEMA]
      threads: 10
      client_session_keep_alive: False
      query_tag: DBT_ETL
```

---

## 1Password Structure Reference

### Vault: `LocalDev-APIKeys`

| Item Name | Type | Custom Fields |
|-----------|------|---------------|
| **Looker** | API Credential | client-secret |

**Note:** Snowflake uses key-pair authentication (private key file at `~/.ssh/snowflake_private_key.p8`), not password.

### 1Password CLI path:
```bash
op://LocalDev-APIKeys/Looker/client-secret
```

---

## What to Expect During Phase 2

| Step | Claude Does | You Do |
|------|-------------|--------|
| 1-2 | Runs commands | Watch and wait |
| 3 | Installs 1Password CLI | Enter your 1Password password; enable CLI in app |
| 4 | Guides 1Password setup | Create vault and items in 1Password app |
| 5 | Updates .zshrc | Click "Accept" and provide your credential values |
| 6 | Copies config files | Click "Accept" for each file |
| 7 | Creates dbt profile | Click "Accept" |
| 8 | Tests AWS login | Log in via browser when it opens |
| 9 | Tests everything | Watch for success messages |

---

## After Setup: Daily Usage

```bash
# Open Terminal (Cmd+Space → "Terminal")

# Option 1: Use the shortcut
clc

# Option 2: Manual launch
load_api_keys
aws sso login --profile AIPlayground
cd ~/Documents/GitHub
claude

# Run analysis
/analysis "How many active accounts do we have?"
```

The `clc` shortcut handles AWS login and launches Claude Code with all the right settings.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "aws: command not found" | Run: `brew install awscli` |
| "Token has expired" | Run: `aws sso login --profile AIPlayground` |
| "Snowflake authentication failed" | Check that #team_data_platform registered your public key |
| "Looker credentials not found" | Run: `op signin`, then `load_api_keys` |
| "op: command not found" | Run: `brew install --cask 1password-cli` |
| 1Password prompts not working | Enable CLI integration in 1Password app Settings > Developer |

---

## Getting Help

- **AWS/Bedrock access (Okta group):** #it-support
- **Inference Profile ARNs:** #team_infra
- **Snowflake issues:** [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)
- **Looker API keys:** #analytics
- **1Password help:** #it-support
- **This setup guide:** [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)

---

## File Structure After Setup

```
~/.claude/
├── settings.json
├── commands/
│   └── analysis.md
└── skills/
    └── snowflake-query.md

~/.dbt/
└── profiles.yml

~/.ssh/
├── snowflake_private_key.p8  (keep secret!)
└── snowflake_public_key.pub  (sent to #team_data_platform)

~/.zshrc  (shell configuration with credentials)

~/Documents/GitHub/
├── ad_hoc_analysis/        # Your reports
├── claude-analysis-setup/  # Config source
├── snowflake_idw/          # Data models
└── internal_idw/           # Dashboards

1Password (LocalDev-APIKeys vault):
└── Looker (client-secret)
```

---

## Quick Reference Card

```
┌─────────────────────────────────────────────┐
│  DAILY USAGE                                │
├─────────────────────────────────────────────┤
│  1. Open Terminal (Cmd+Space → Terminal)    │
│  2. clc  (or: load_api_keys + aws sso +     │
│           cd ~/Documents/GitHub + claude)   │
│  3. /analysis "your question here"          │
├─────────────────────────────────────────────┤
│  IF TOKEN EXPIRED                           │
├─────────────────────────────────────────────┤
│  aws sso login --profile AIPlayground       │
├─────────────────────────────────────────────┤
│  IF 1PASSWORD NOT WORKING                   │
├─────────────────────────────────────────────┤
│  op signin                                  │
│  load_api_keys                              │
├─────────────────────────────────────────────┤
│  HELP                                       │
├─────────────────────────────────────────────┤
│  AWS/Bedrock (Okta): #it-support            │
│  Inference Profiles: #team_infra            │
│  Snowflake: #team_data_platform             │
│  Looker: #analytics                         │
│  1Password: #it-support                     │
└─────────────────────────────────────────────┘
```

---

## Placeholder Values Reference

| Placeholder | Description | Where to Get |
|-------------|-------------|--------------|
| `[MY_USERNAME]` | macOS username | Run: `whoami` |
| `[MY_SNOWFLAKE_USERNAME]` | Snowflake username (UPPERCASE, e.g., `FIRSTNAMELASTNAME`) | Ticket #2 ([#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)) |
| `[MY_DEV_SCHEMA]` | Your dev schema (lowercase, dbt development only) | Ticket #2 ([#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)) |
| `[MY_LOOKER_CLIENT_ID]` | Looker API Client ID | Ticket #3 (#analytics) |
| `[MY_LOOKER_CLIENT_SECRET]` | Looker API Client Secret | Ticket #3 (store in 1Password) |
| `[MY_AWS_SSO_PROFILE]` | AWS SSO profile name | Always `AIPlayground` |
| `[INFERENCE_PROFILE_ARN_DEFAULT]` | Bedrock default/sonnet model | Ticket #5 (#team_infra) |
| `[INFERENCE_PROFILE_ARN_SONNET]` | Bedrock Sonnet model | Ticket #5 (#team_infra) |
| `[INFERENCE_PROFILE_ARN_HAIKU]` | Bedrock Haiku model | Ticket #5 (#team_infra) |
