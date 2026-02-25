# Claude Code Analysis Setup Guide - Elation Health

Set up Claude Code for business analysis with Snowflake, dbt, and Looker.

**Audience:** Non-technical users with no command line experience. Claude Code (an AI assistant) will guide you through most steps.

---

## Choose Your Setup Path

| Path | Use if... |
|------|-----------|
| **Claude Enterprise** | You have access to Claude Code Enterprise via Okta |
| **AWS Bedrock** | You're using Elation's internal AWS infrastructure for AI |

Not sure which? Ask in #data-platform.

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

### Tickets Required for Both Paths

---

#### Ticket A: Snowflake Access (Required)

**General Questions:** Data Platform ([#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC))

##### Step 1: Request access in [#ask-it](https://elation.slack.com/archives/CABPVMDHP)

Copy and send this message:

```
Hi IT team,

I'm setting up Claude Code for business analysis at Elation Health.
I need Snowflake access to run read-only queries for internal reporting, ad-hoc analysis, and dashboard investigation.

Please grant me: SNOWFLAKE - TEAM_PRODUCT_MANAGEMENT

Thank you!
```

##### Step 2: After access is provisioned, verify in Snowflake

Use these pages:
- Profile (username): https://app.snowflake.com/elationhealth/ehdw/settings/profile
- Preferences (default role and warehouse): https://app.snowflake.com/elationhealth/ehdw/settings/preferences

Verify these values:
- Snowflake username format: `FIRSTNAMELASTNAME` (example: `JANEDOE`)
- Default Role: `SNOWFLAKE - TEAM_PRODUCT_MANAGEMENT`
- Default Warehouse: `TEAM_PM_WH`

If any value does not match, contact Data Platform in [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC) and include what you expected vs. what you see.

---

#### Ticket B: Looker API Credentials (Required)

**Submit to:** Analytics Team (#ask-bst on Slack)

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

#### Ticket C: internal_dw GitHub Repository Access (Required)

**Submit to:** Analytics Team (#ask-bst on Slack)

**Subject:** Request access to internal_dw GitHub repository

**Description:**
```
Hi Analytics Team,

I'm setting up Claude Code for business analysis and need read access to the internal_dw GitHub repository (elationemr/internal_dw).

Thank you!
```

**What you'll receive:**
- GitHub access to `elationemr/internal_dw`

---

### Claude Enterprise Path Only

---

#### Ticket 3: Claude Code Enterprise Access

**Submit to:** IT team (#ask-it)

**Subject:** Request Claude Code Enterprise access

**Description:**
```
Hi IT team,

I need access to Claude Code Enterprise for business analysis.

Thank you!
```

**What you'll receive:**
- Information on how to access Claude (Anthropic) via Okta

---

### AWS Bedrock Path Only

---

#### Ticket 1: AWS Bedrock Access (Required)

**Submit to:** IT Support (#ask-it)

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

#### Ticket 2: Bedrock Inference Profiles (Required - submit after Ticket 1 is complete)

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

After IT responds, fill in these values as you receive them.

### Claude Enterprise Path

| Item | Value | Received? | Ticket |
|------|-------|-----------|--------|
| Claude Enterprise access (via Okta) | _________________ | ☐ | #3 |
| Snowflake Username | _________________ | ☐ | A |
| Looker Client ID | _________________ | ☐ | B |
| Looker Client Secret | _________________ | ☐ | B |

**Important:** You need ALL credentials before starting Phase 2.

### AWS Bedrock Path

| Item | Value | Received? | Ticket |
|------|-------|-----------|--------|
| AWS SSO Profile | `AIPlayground` | ☐ | #1 |
| Snowflake Username | _________________ | ☐ | A |
| Looker Client ID | _________________ | ☐ | B |
| Looker Client Secret | _________________ | ☐ | B |
| Inference Profile ARN (sonnet) | _________________ | ☐ | #2 |
| Inference Profile ARN (haiku) | _________________ | ☐ | #2 |

**Important:** You need ALL credentials before starting Phase 2.

---

## Phase 1: Manual Setup (While Waiting for IT)

Steps 1–3 and 5–6 are the same for both paths. Step 4 differs slightly.

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

### Step 4: Install Claude Code

**Both paths:**
```bash
npm install -g @anthropic-ai/claude-code
```

**AWS Bedrock path only — also install AWS CLI:**
```bash
brew install awscli
```

Verify:
```bash
claude --version
# AWS Bedrock path only:
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
git clone git@github.com:elationemr/internal_dw.git
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
> Hi Data Platform team - I'm setting up Snowflake key-pair authentication for Claude Code and need help from a Snowflake administrator.
>
> Could a Snowflake admin please set my RSA public key on my Snowflake user account?
>
> Reference: https://docs.snowflake.com/en/user-guide/key-pair-auth
>
> Command to run:
> `ALTER USER <username> SET RSA_PUBLIC_KEY='<public_key_contents>';`
>
> My public key:
> [paste key here]

---

## Phase 2: Claude-Assisted Setup (After ALL Tickets Complete)

### Step 1: Start Claude Code

**Claude Enterprise path:**

```bash
cd ~/Documents/GitHub/claude-analysis-setup
claude
```

Claude Code will open a browser window — log in with your Okta credentials.

**AWS Bedrock path:**

First, log into AWS:

```bash
aws sso login --profile AIPlayground
```

This opens a browser window - log in with your Okta credentials.

Then launch Claude Code with your inference profile ARNs from #team_infra:

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

### Step 2: Copy This Prompt Into Claude Code

Find your path below, fill in your credentials, then paste the entire prompt.

---

**Claude Enterprise path prompt:**

```
I'm a non-technical user setting up Claude Code for data analysis. I've completed the manual setup:
- Installed Homebrew, Python 3.12, Node.js 20
- Installed Claude Code
- Cloned repos: claude-analysis-setup, snowflake_idw, internal_dw
- Generated Snowflake keys (sent public key to #team_data_platform)
- Logged into Claude Code via Okta

Setup path: Claude Enterprise

I have received these credentials from IT:
- Snowflake Username: [FILL IN - e.g., JANEDOE]
- Looker Client ID: [FILL IN]
- Looker Client Secret: [FILL IN]

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
  2. Go to Settings (top left menu under File)
  3. Click "Developer" in sidebar
  4. Enable "Integrate with 1Password CLI"
  5. Click "Security" in the left sidebar
  6. Enable "Touch ID" for CLI authentication

### 4. Add to 1Password Employee vault and store credentials
Guide me to create items in 1Password following this EXACT structure:

#### Create "Looker API" item (type: API Credential)
Standard fields used:
- vault: Employee
- username: (leave empty or note)
- credential: (leave empty or note)
Custom fields to add:
- client-secret: [MY_LOOKER_CLIENT_SECRET]

Note: Snowflake uses key-pair authentication (private key file), not password — no Snowflake item needed in 1Password.

### 5. Configure shell environment
Create/update my ~/.zshrc using the Claude Enterprise template in SETUP_GUIDE.md, replacing all placeholder values with my actual credentials.

### 6. Copy Claude config files
- Create ~/.claude/commands and ~/.claude/skills folders
- Copy settings.json from claude-analysis-setup to ~/.claude/
- Copy analysis.md from claude-analysis-setup/commands/ to ~/.claude/commands/
- Copy snowflake-query.md from claude-analysis-setup/skills/ to ~/.claude/skills/
- Update all files: replace YOUR_USERNAME with my macOS username (find with: whoami)

### 7. Create dbt profile
Create ~/.dbt/profiles.yml with my credentials.

### 8. Test everything
- Test Snowflake connection: dbt debug
- Test 1Password: load_api_keys
- Verify launcher alias: alias clc (confirm it's configured correctly)
- Run a simple /analysis command

Let's start with step 1!
```

---

**AWS Bedrock path prompt:**

```
I'm a non-technical user setting up Claude Code for data analysis. I've completed the manual setup:
- Installed Homebrew, Python 3.12, Node.js 20, AWS CLI
- Installed Claude Code
- Cloned repos: claude-analysis-setup, snowflake_idw, internal_dw
- Generated Snowflake keys (sent public key to #team_data_platform)

Setup path: AWS Bedrock

I have received these credentials from IT:
- AWS SSO Profile: AIPlayground
- Snowflake Username: [FILL IN - e.g., JANEDOE]
- Looker Client ID: [FILL IN]
- Looker Client Secret: [FILL IN]
- Inference Profile ARN (sonnet): [FILL IN - from #team_infra]
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
  2. Go to Settings (top left menu under File)
  3. Click "Developer" in sidebar
  4. Enable "Integrate with 1Password CLI"
  5. Click "Security" in the left sidebar
  6. Enable "Touch ID" for CLI authentication

### 4. Add to 1Password Employee vault and store credentials
Guide me to create items in 1Password following this EXACT structure:

#### Create "Looker API" item (type: API Credential)
Standard fields used:
- vault: Employee
- username: (leave empty or note)
- credential: (leave empty or note)
Custom fields to add:
- client-secret: [MY_LOOKER_CLIENT_SECRET]

Note: Snowflake uses key-pair authentication (private key file), not password — no Snowflake item needed in 1Password.

### 5. Configure shell environment
Create/update my ~/.zshrc using the AWS Bedrock template in SETUP_GUIDE.md, replacing all placeholder values with my actual credentials.

### 6. Copy Claude config files
- Create ~/.claude/commands and ~/.claude/skills folders
- Copy settings.json from claude-analysis-setup to ~/.claude/
- Copy analysis.md from claude-analysis-setup/commands/ to ~/.claude/commands/
- Copy snowflake-query.md from claude-analysis-setup/skills/ to ~/.claude/skills/
- Update all files: replace YOUR_USERNAME with my macOS username (find with: whoami)

### 7. Create dbt profile
Create ~/.dbt/profiles.yml with my credentials.

### 8. Test AWS SSO login
Run: aws sso login --profile AIPlayground
This will open a browser - I need to log in with my work credentials.

### 9. Test everything
- Test AWS: aws sts get-caller-identity --profile AIPlayground
- Test Snowflake connection: dbt debug
- Test 1Password: load_api_keys
- Verify launcher alias: alias clc (confirm it's configured correctly)
- Run a simple /analysis command

Let's start with step 1!
```

---

## Shell Configuration Templates (~/.zshrc)

Claude Code will help you create this file with your actual values during Phase 2.

### Claude Enterprise Path

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
  export LOOKER_CLIENT_SECRET="$(op read 'op://Employee/Looker API/client-secret' 2>/dev/null)"
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
export SNOWFLAKE_WAREHOUSE="TEAM_PM_WH"
export SNOWFLAKE_DATABASE="IDW"
export SNOWFLAKE_SCHEMA="SHARED"
export SNOWFLAKE_ROLE="TEAM_PRODUCT_MANAGEMENT"

# ===========================================
# Claude Code launcher
# ===========================================

alias clc='claude'
export PATH="$HOME/.npm-global/bin:$PATH"
```

### AWS Bedrock Path

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
  export LOOKER_CLIENT_SECRET="$(op read 'op://Employee/Looker API/client-secret' 2>/dev/null)"
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
export SNOWFLAKE_WAREHOUSE="TEAM_PM_WH"
export SNOWFLAKE_DATABASE="IDW"
export SNOWFLAKE_SCHEMA="SHARED"
export SNOWFLAKE_ROLE="TEAM_PRODUCT_MANAGEMENT"

# AWS
export AWS_PROFILE="AIPlayground"
export AWS_REGION="us-west-2"

# ===========================================
# Claude Code launcher function
# ===========================================

launch_claude() {
  aws sso login --profile AIPlayground
  AWS_PROFILE=AIPlayground AWS_REGION=us-west-2 CLAUDE_CODE_USE_BEDROCK=1 \
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

Same for both paths:

```yaml
elation_health_snowflake:
  target: prod
  outputs:
    prod:
      type: snowflake
      account: GAA83698
      user: [MY_SNOWFLAKE_USERNAME]
      private_key_path: "{{ env_var('HOME') }}/.ssh/snowflake_private_key.p8"
      # role: TEAM_PRODUCT_MANAGEMENT
      database: IDW
      warehouse: TEAM_PM_WH
      schema: SHARED
      threads: 10
      client_session_keep_alive: False
      query_tag: DBT_ETL
```

**Notes:**
- `role:` is optional. Leave it out if your Snowflake user already has the correct default role; add it only if #team_data_platform tells you to.

---

## 1Password Structure Reference

### Vault: `Employee`

| Item Name | Type | Fields | Path |
|-----------|------|--------|------|
| **Looker API** | API Credential | client-secret (custom) | `op://Employee/Looker API/client-secret` |

**Note:** Snowflake uses key-pair authentication (private key file at `~/.ssh/snowflake_private_key.p8`), not a password. Claude Enterprise authentication is handled via Okta — no additional 1Password item needed.

---

## What to Expect During Phase 2

| Step | Claude Does | You Do |
|------|-------------|--------|
| 1-2 | Runs commands | Watch and wait |
| 3 | Installs 1Password CLI | Enter your 1Password password; enable CLI in app |
| 4 | Guides 1Password setup | Add item to Employee vault in 1Password app |
| 5 | Updates .zshrc | Click "Accept" and provide your credential values |
| 6 | Copies config files | Click "Accept" for each file |
| 7 | Creates dbt profile | Click "Accept" |
| 8 | Tests AWS login *(AWS path only)* | Log in via browser when it opens |
| 9 | Tests everything | Watch for success messages |

---

## After Setup: Daily Usage

```bash
# Open Terminal (Cmd+Space → "Terminal")

# Option 1: Use the shortcut
clc

# Option 2: Manual launch
load_api_keys
aws sso login --profile AIPlayground   # AWS Bedrock path only
cd ~/Documents/GitHub
claude

# Run analysis
/analysis "How many active accounts do we have?"
```

The `clc` shortcut handles authentication and launches Claude Code with all the right settings.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "aws: command not found" | Run: `brew install awscli` *(AWS path only)* |
| "Token has expired" or "Could not load credentials from any providers" | Run: `aws sso login --profile AIPlayground` *(AWS path only)*, then relaunch with `clc` |
| "Snowflake authentication failed" | Check that #team_data_platform registered your public key |
| "`dbt debug` cannot connect to Snowflake" | Verify `account` in `~/.dbt/profiles.yml` matches your Snowflake account identifier (use account identifier only, not full URL) |
| "SQL access control error / wrong role" | Ask #team_data_platform to confirm your default Snowflake role, or add `role:` in `~/.dbt/profiles.yml` only if needed |
| "Looker credentials not found" | Run: `op signin`, then `load_api_keys` |
| "op: command not found" | Run: `brew install --cask 1password-cli` |
| 1Password prompts not working | Enable CLI integration in 1Password app Settings > Developer |

---

## Getting Help

- **AWS/Bedrock access (Okta group):** #ask-it
- **Inference Profile ARNs:** #team_infra
- **Snowflake issues:** [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)
- **Looker API keys:** #ask-bst
- **1Password help:** #ask-it
- **Claude Enterprise access:** #ask-it
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
└── internal_dw/            # Dashboards

1Password (Employee vault):
└── Looker API (client-secret)
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
│  IF TOKEN EXPIRED (AWS Bedrock only)        │
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
│  AWS/Bedrock (Okta): #ask-it                │
│  Inference Profiles: #team_infra            │
│  Snowflake: #team_data_platform             │
│  Looker: #ask-bst                           │
│  1Password/Enterprise: #ask-it              │
└─────────────────────────────────────────────┘
```

---

## Placeholder Values Reference

### Both Paths

| Placeholder | Description | Where to Get |
|-------------|-------------|--------------|
| `[MY_USERNAME]` | macOS username | Run: `whoami` |
| `[MY_SNOWFLAKE_USERNAME]` | Snowflake username (UPPERCASE, e.g., `FIRSTNAMELASTNAME`) | Ticket A ([#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)) |
| `[MY_LOOKER_CLIENT_ID]` | Looker API Client ID | Ticket B (#ask-bst) |
| `[MY_LOOKER_CLIENT_SECRET]` | Looker API Client Secret | Ticket B (store in 1Password) |

### AWS Bedrock Path Only

| Placeholder | Description | Where to Get |
|-------------|-------------|--------------|
| `[MY_AWS_SSO_PROFILE]` | AWS SSO profile name | Always `AIPlayground` |
| `[INFERENCE_PROFILE_ARN_DEFAULT]` | Bedrock default/sonnet model | Ticket #2 (#team_infra) |
| `[INFERENCE_PROFILE_ARN_SONNET]` | Bedrock Sonnet model | Ticket #2 (#team_infra) |
| `[INFERENCE_PROFILE_ARN_HAIKU]` | Bedrock Haiku model | Ticket #2 (#team_infra) |
