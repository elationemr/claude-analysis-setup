# Claude Code Analysis Setup Guide - Elation Health (Windows)

Set up Claude Code for business analysis with Snowflake, dbt, and Looker.

**Audience:** Non-technical Windows users with no command line experience. Claude Code (an AI assistant) will guide you through most steps.

**Setup path:** Claude Code Enterprise via Okta (this guide does not cover AWS Bedrock).

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

### Ticket 1: Claude Code Enterprise Access

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

### Ticket 2: GitHub Account & elationemr Organization Access

#### Step 1: Create a GitHub Account

Before submitting to IT, create your GitHub account:

1. Go to [github.com/signup](https://github.com/signup)
2. Use your Elation work email address
3. Choose a username in this format: **`FIRSTNAME-elation`**
   - Example: `jane-elation` or `john-elation`
4. Complete email verification

**Note:** If you already have a personal GitHub account, you can either create a new one with this naming convention or ask IT to use your existing account.

#### Step 2: Submit the IT Ticket

**Submit to:** IT team (#ask-it)

**Subject:** Request access to elationemr GitHub organization

**Description** (fill in your username and email before sending):
```
Hi IT team,

I've created a GitHub account for work and need to be added to the elationemr GitHub organization.

My GitHub username: [FIRSTNAME-elation, e.g. jane-elation]
My work email: [your.name@elationhealth.com]

Thank you!
```

**What you'll receive:**
- An invitation to join the elationemr GitHub organization (sent to your work email)

#### Step 3: After Receiving the Invitation — Configure GitHub SSH and SSO

Once you receive and accept the elationemr invitation:

**Generate an SSH key for GitHub**

Open PowerShell and run:

```powershell
ssh-keygen -t ed25519 -C "your.name@elationhealth.com"
```

Press Enter to accept all defaults (no passphrase required). Then display your public key:

```powershell
cat ~/.ssh/id_ed25519.pub
```

Copy the entire output.

**Add the SSH key to GitHub**

1. Go to [github.com](https://github.com) and sign in
2. Click your profile picture (top right) → **Settings**
3. In the left sidebar, click **SSH and GPG keys**
4. Click **New SSH key**
5. Give it a title (e.g., "Elation Windows laptop") and paste your public key
6. Click **Add SSH key**

**Authorize the SSH key for SSO**

After adding the key, you'll see it listed under SSH keys:

1. Find your newly added key
2. Click **Configure SSO** next to it
3. Click **Authorize** next to **elationemr**
4. You will be redirected to sign in with your Okta credentials — complete the sign-in

**Test the connection**

```powershell
ssh -T git@github.com
```

You should see: `Hi [your-username]! You've successfully authenticated...`

---

### Ticket 3: Snowflake Access

**General Questions:** Data Platform ([#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC))

#### Step 1: Request access in [#ask-it](https://elation.slack.com/archives/CABPVMDHP)

Copy and send this message:

```
Hi IT team,

I'm setting up Claude Code for business analysis at Elation Health.
I need Snowflake access to run read-only queries for internal reporting, ad-hoc analysis, and dashboard investigation.

Please grant me: SNOWFLAKE - TEAM_PRODUCT_MANAGEMENT

Thank you!
```

#### Step 2: After access is provisioned, verify in Snowflake

Use these pages:
- Profile (username): https://app.snowflake.com/elationhealth/ehdw/settings/profile
- Preferences (default role and warehouse): https://app.snowflake.com/elationhealth/ehdw/settings/preferences

Verify these values:
- Snowflake username format: `FIRSTNAMELASTNAME` (example: `JANEDOE`)
- Default Role: `SNOWFLAKE - TEAM_PRODUCT_MANAGEMENT`
- Default Warehouse: `TEAM_PM_WH`

If any value does not match, contact Data Platform in [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC) and include what you expected vs. what you see.

---

### Ticket 4: Looker API Credentials

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

### Ticket 5: internal_dw GitHub Repository Access

**Submit to:** Analytics Team (#ask-bst on Slack)

**Subject:** Request access to internal_dw GitHub repository

**Description** (fill in your GitHub username before sending):
```
Hi Analytics Team,

I'm setting up Claude Code for business analysis and need read access to the internal_dw GitHub repository (elationemr/internal_dw).

My GitHub username: [FIRSTNAME-elation]

Thank you!
```

**What you'll receive:**
- GitHub access to `elationemr/internal_dw`

---

## Credentials Checklist

After IT responds, fill in these values as you receive them.

| Item | Value | Received? | Ticket |
|------|-------|-----------|--------|
| Claude Enterprise access (via Okta) | _________________ | ☐ | 1 |
| GitHub account connected to elationemr | _________________ | ☐ | 2 |
| Snowflake Username | _________________ | ☐ | 3 |
| Looker Client ID | _________________ | ☐ | 4 |
| Looker Client Secret | _________________ | ☐ | 4 |

**Important:** You need ALL credentials before starting Phase 2.

---

## Phase 1: Manual Setup (While Waiting for IT)

**Before Step 4 (Clone repos):** Make sure you have completed the GitHub SSH and SSO setup from Ticket 2 above, or cloning will fail.

---

### Step 1: Open PowerShell

1. Press `Win + X` and select **Terminal** or **Windows PowerShell**
   - Alternatively, press `Win + S`, type `PowerShell`, and press Enter

A window with a command prompt will appear. This is where you'll type commands.

**Tip:** Copy commands from this guide and paste into PowerShell with `Ctrl + V` (or right-click to paste).

---

### Step 2: Install Git, Python, and Node.js

Windows 10/11 includes `winget` (Windows Package Manager). Run these commands one at a time:

```powershell
winget install Git.Git
winget install Python.Python.3.12
winget install OpenJS.NodeJS.LTS
```

After each install, follow any on-screen prompts.

**Important:** Close and reopen PowerShell after these installs so the new tools are available.

Verify they worked:
```powershell
git --version
python --version
node --version
```

You should see version numbers for each.

---

### Step 3: Install Claude Code

```powershell
npm install -g @anthropic-ai/claude-code
```

Verify:
```powershell
claude --version
```

---

### Step 4: Clone the Setup Files

Create a folder for your work:

```powershell
mkdir ~/Documents/GitHub
cd ~/Documents/GitHub
```

Download the setup files:

```powershell
git clone git@github.com:elationemr/claude-analysis-setup.git
git clone git@github.com:elationemr/snowflake_idw.git
git clone git@github.com:elationemr/internal_dw.git
```

**Note:** If you see an authentication error, confirm you completed the GitHub SSH and SSO steps from Ticket 2. Ask #ask-it if you need help.

---

### Step 5: Generate Snowflake Keys

Git for Windows includes OpenSSL. Run these commands in PowerShell:

```powershell
mkdir ~/.ssh -ErrorAction SilentlyContinue
cd ~/.ssh
openssl genrsa 2048 | openssl pkcs8 -topk8 -inform PEM -out snowflake_private_key.p8 -nocrypt
openssl rsa -in snowflake_private_key.p8 -pubout -out snowflake_public_key.pub
```

**Note:** If `openssl` is not found, open **Git Bash** (search in Start menu) and run the same commands there.

**Important:** Now send your public key to [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC):

```powershell
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

```powershell
cd ~/Documents/GitHub/claude-analysis-setup
claude
```

Claude Code will open a browser window — log in with your Okta credentials.

---

### Step 2: Copy This Prompt Into Claude Code

Fill in your credentials below, then paste the entire prompt.

```
I'm a non-technical user setting up Claude Code for data analysis on Windows. I've completed the manual setup:
- Installed Git, Python 3.12, Node.js (via winget)
- Installed Claude Code
- Set up GitHub SSH key and authorized it for SSO with elationemr
- Cloned repos: claude-analysis-setup, snowflake_idw, internal_dw
- Generated Snowflake keys (sent public key to #team_data_platform)
- Logged into Claude Code via Okta

Setup path: Claude Enterprise (Windows)

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
- Install via: winget install AgileBits.1Password.CLI
- Help me sign in: op signin
- Tell me how to enable CLI in 1Password app:
  1. Open 1Password app
  2. Go to Settings (gear icon or top menu)
  3. Click "Developer" in sidebar
  4. Enable "Integrate with 1Password CLI"
  5. Click "Security" in the left sidebar
  6. Enable Windows Hello (or PIN) for CLI authentication

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

### 5. Configure PowerShell profile
Create/update my PowerShell profile ($PROFILE) using the Claude Enterprise Windows template in SETUP_GUIDE_WINDOWS.md, replacing all placeholder values with my actual credentials.

### 6. Copy Claude config files
- Create ~/.claude/commands and ~/.claude/skills folders
- Copy settings.json from claude-analysis-setup to ~/.claude/
- Copy analysis.md from claude-analysis-setup/commands/ to ~/.claude/commands/
- Copy snowflake-query.md from claude-analysis-setup/skills/ to ~/.claude/skills/
- Update all files: replace YOUR_USERNAME with my Windows username (find with: whoami)

### 7. Create dbt profile
Create ~/.dbt/profiles.yml with my credentials.

### 8. Test everything
- Test Snowflake connection: dbt debug
- Test 1Password: Load-ApiKeys
- Verify launcher function: clc (confirm it loads API keys and launches Claude)
- Run a simple /analysis command

Let's start with step 1!
```

---

## PowerShell Profile Template

Claude Code will help you create this file with your actual values during Phase 2.

To find your profile path, run: `$PROFILE`

```powershell
# ===========================================
# API Credentials (loaded from 1Password)
# ===========================================
# Uses personal auth with Windows Hello/PIN - works after initial sign-in

function Load-ApiKeys {
    $secret = (op read 'op://Employee/Looker API/client-secret' 2>$null)
    if ($LASTEXITCODE -eq 0 -and $secret) {
        $env:LOOKER_CLIENT_SECRET = $secret
        Write-Host "✓ API keys loaded from 1Password"
        return $true
    } else {
        Write-Host "✗ Could not load API keys from 1Password."
        Write-Host "  Make sure 1Password is unlocked, then run: op signin"
        Write-Host "  Then run: Load-ApiKeys  (or close and reopen PowerShell, then try 'clc' again)"
        return $false
    }
}

# ===========================================
# Non-secret configuration values
# ===========================================

# Looker
$env:LOOKER_BASE_URL = "https://elationhealth.looker.com"
$env:LOOKER_CLIENT_ID = "[MY_LOOKER_CLIENT_ID]"
$env:LOOKER_VERIFY_SSL = "true"

# Snowflake
$env:SNOWFLAKE_ACCOUNT = "elationhealth-ehdw"
$env:SNOWFLAKE_USER = "[MY_SNOWFLAKE_USERNAME]"
$env:SNOWFLAKE_WAREHOUSE = "TEAM_PM_WH"
$env:SNOWFLAKE_DATABASE = "IDW"
$env:SNOWFLAKE_SCHEMA = "SHARED"
$env:SNOWFLAKE_ROLE = "TEAM_PRODUCT_MANAGEMENT"

# ===========================================
# Claude Code launcher
# ===========================================
# Loads API keys then launches Claude — run "clc" to start your session

function clc {
    if (Load-ApiKeys) {
        claude
    }
}
```

**To activate the profile after editing:** Close and reopen PowerShell, or run:
```powershell
. $PROFILE
```

---

## dbt Profile Template (~/.dbt/profiles.yml)

```yaml
elation_health_snowflake:
  target: prod
  outputs:
    prod:
      type: snowflake
      account: GAA83698
      user: [MY_SNOWFLAKE_USERNAME]
      private_key_path: "{{ env_var('USERPROFILE') }}/.ssh/snowflake_private_key.p8"
      # role: TEAM_PRODUCT_MANAGEMENT
      database: IDW
      warehouse: TEAM_PM_WH
      schema: SHARED
      threads: 10
      client_session_keep_alive: False
      query_tag: DBT_CLAUDE_CODE
```

**Notes:**
- `USERPROFILE` is the Windows equivalent of `HOME` (e.g., `C:\Users\janedoe`)
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
| 5 | Updates PowerShell profile | Click "Accept" and provide your credential values |
| 6 | Copies config files | Click "Accept" for each file |
| 7 | Creates dbt profile | Click "Accept" |
| 8 | Tests everything | Watch for success messages |

---

## After Setup: Daily Usage

```powershell
# Open PowerShell (Win+X → Terminal)

# Start your session — loads API keys and launches Claude
clc

# Run analysis
/analysis "How many active accounts do we have?"
```

The `clc` function loads your API credentials from 1Password and then launches Claude Code automatically.

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `openssl` not found | Open **Git Bash** (search in Start menu) and run the key generation commands there |
| `winget` not found | Update Windows or install App Installer from the Microsoft Store |
| GitHub SSH auth error when cloning | Confirm you completed the SSH + SSO steps from Ticket 2; ask #ask-it |
| "Snowflake authentication failed" | Check that #team_data_platform registered your public key |
| "`dbt debug` cannot connect to Snowflake" | Verify `account` in `~/.dbt/profiles.yml` matches your Snowflake account identifier (use account identifier only, not full URL) |
| "SQL access control error / wrong role" | Ask #team_data_platform to confirm your default Snowflake role, or add `role:` in `~/.dbt/profiles.yml` only if needed |
| "Looker credentials not found" | Run `op signin`, then `Load-ApiKeys` |
| `op` not found | Run: `winget install AgileBits.1Password.CLI` |
| 1Password prompts not working | Enable CLI integration in 1Password app Settings > Developer |
| PowerShell profile not loading | Run `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser` then close and reopen PowerShell |

---

## Getting Help

- **Snowflake issues:** [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)
- **Looker API keys / GitHub repo access:** #ask-bst
- **1Password / Claude Enterprise / GitHub account:** #ask-it
- **This setup guide:** [#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)

---

## File Structure After Setup

```
~\.claude\
├── settings.json
├── commands\
│   └── analysis.md
└── skills\
    └── snowflake-query.md

~\.dbt\
└── profiles.yml

~\.ssh\
├── id_ed25519              (GitHub SSH key - keep secret!)
├── id_ed25519.pub          (GitHub public key - added to github.com)
├── snowflake_private_key.p8  (Snowflake key - keep secret!)
└── snowflake_public_key.pub  (sent to #team_data_platform)

~\Documents\PowerShell\
└── Microsoft.PowerShell_profile.ps1  (shell config with credentials)

~\Documents\GitHub\
├── ad_hoc_analysis\        # Your reports
├── claude-analysis-setup\  # Config source
├── snowflake_idw\          # Data models
└── internal_dw\            # Dashboards

1Password (Employee vault):
└── Looker API (client-secret)
```

---

## Quick Reference Card

```
┌─────────────────────────────────────────────┐
│  DAILY USAGE                                │
├─────────────────────────────────────────────┤
│  1. Open PowerShell (Win+X → Terminal)      │
│  2. clc  (loads API keys + launches Claude) │
│  3. /analysis "your question here"          │
├─────────────────────────────────────────────┤
│  IF 1PASSWORD NOT WORKING                   │
├─────────────────────────────────────────────┤
│  op signin                                  │
│  Load-ApiKeys                               │
├─────────────────────────────────────────────┤
│  HELP                                       │
├─────────────────────────────────────────────┤
│  Snowflake: #team_data_platform             │
│  Looker / GitHub repos: #ask-bst            │
│  1Password / Enterprise / GitHub: #ask-it   │
└─────────────────────────────────────────────┘
```

---

## Placeholder Values Reference

| Placeholder | Description | Where to Get |
|-------------|-------------|--------------|
| `[MY_USERNAME]` | Windows username | Run: `whoami` |
| `[MY_SNOWFLAKE_USERNAME]` | Snowflake username (UPPERCASE, e.g., `FIRSTNAMELASTNAME`) | Ticket 3 ([#team_data_platform](https://elation.slack.com/archives/C03MUBG02SC)) |
| `[MY_LOOKER_CLIENT_ID]` | Looker API Client ID | Ticket 4 (#ask-bst) |
| `[MY_LOOKER_CLIENT_SECRET]` | Looker API Client Secret | Ticket 4 (store in 1Password) |
