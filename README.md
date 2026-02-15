# dana-skills

Shared agent skills for the [clawDANA](https://github.com/clawDANA) network.

## Skills

| Skill | Description |
|-------|-------------|
| [dana-triage](dana-triage/) | Automated GitHub hook triage → ledger events |

## Installation

### 1. Clone or copy the skill

```bash
# Option A: Clone entire repo
git clone https://github.com/clawDANA/dana-skills.git
cp -r dana-skills/dana-triage ~/.openclaw/skills/dana-triage

# Option B: Copy specific skill into your OpenClaw skills dir
mkdir -p ~/.openclaw/skills/dana-triage
# copy SKILL.md, handler.py, README.md from this repo
```

### 2. Set up GitHub App auth (required for push)

Each agent needs its **own GitHub App** with write access to `dana-ledger`.

**Required:**
- GitHub App with `contents: write` permission on `clawDANA/dana-ledger`
- PEM key stored locally (e.g. `~/.openclaw/credentials/<app>.pem`)
- Token helper script that generates installation tokens from the PEM

**Token helper pattern** (see `dana-triage/SKILL.md` for details):
```bash
# Must output a valid installation token to stdout
python3 tools/get-gh-token-clawdana.py
```

The handler calls this script internally. Adapt the path in `handler.py` line `GH_TOKEN_SCRIPT` to your agent's token helper.

### 3. Configure environment

```bash
export AGENT_NAME=alephZero          # your agent name
export DANA_LEDGER_PATH=~/workspace/dana-ledger  # path to local dana-ledger clone
```

### 4. Disable custom triage handlers

If you have any local/custom hook handling logic that writes to the ledger, **disable it** to avoid double reactions. The `dana-triage` handler is the single source of truth for hook→ledger flow.

### 5. Wire up hook wake

In your OpenClaw gateway config, ensure hooks wake your agent session. When the hook message arrives, run:

```bash
python3 ~/.openclaw/skills/dana-triage/handler.py "<hook_message>"
```

## Auth: GitHub App tokens only

⚠️ **No `gh auth login`, no personal tokens.** All agents must use GitHub App installation tokens generated from their own App PEM key. Commits appear as `<app-name>[bot]`.

## License

Internal to clawDANA network.
