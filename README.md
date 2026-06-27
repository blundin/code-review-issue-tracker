# code-review-issue-tracker

A Claude Code skill that automatically tracks code review findings across sessions. When you ask Claude to review code, investigate a bug, or audit a module, it silently maintains a JSON log of every finding — and keeps a rolling `known-issues.json` so nothing falls through the cracks between sessions.

## How it works

The skill triggers automatically on review and investigation prompts — no slash command required. Claude opens a timestamped session file, records each finding as it's identified, updates status when issues are fixed, and regenerates a `known-issues.json` aggregate after every change.

All files land in `docs/code-reviews/` in your project root.

```
docs/code-reviews/
  20260627143052-myproject-main.json   ← one file per review session
  known-issues.json                    ← open/acknowledged findings, all sessions
```

Each finding records severity (`high` / `medium` / `low` / `info`), category (`bug` / `security` / `performance` / `correctness` / `convention` / `style`), file, line, a description, and a suggestion. Status tracks from `open` → `fixed` or `wont-fix` / `acknowledged`.

## Installation

Requires [Claude Code](https://claude.ai/code).

```bash
# Clone anywhere you keep local tools
git clone https://github.com/blundin/review-tracker ~/developer/ai-tools/review-tracker

# Symlink into Claude Code's skills directory
ln -s ~/developer/ai-tools/review-tracker ~/.claude/skills/review-tracker
```

Claude Code picks up the skill automatically — no restart needed.

## Usage

The skill is proactive. Start a review naturally:

> "Review the auth module for bugs."
> "What's wrong with this query?"
> "Audit the payment service."

Claude will open a session file, populate findings as it works, and update statuses as issues are resolved. At session wrap-up it reports how many findings were saved — otherwise it works silently.

### Trigger phrases

The skill activates on: *review*, *code review*, *find bugs*, *audit*, *check for issues*, *fix a bug*, *investigate*, *look at this code*, *what's wrong with*.

## Forking and customization

The entire skill is defined in [`SKILL.md`](SKILL.md). The YAML frontmatter controls the name, description (used for auto-invocation matching), and allowed tools. The body is the protocol Claude follows.

Common things to change:

- **Severity or category values** — edit the lists in Step 2
- **Trigger phrases** — update the `description` field in the frontmatter
- **Output location** — change `docs/code-reviews/` in Steps 1 and 4
- **`known-issues.json` fields** — extend the schema in Step 4

After forking, update `name` in both `SKILL.md` and `.claude-plugin/plugin.json`, then symlink under the new name.

## Requirements

- Claude Code (any recent version)
- Git (used to capture branch and commit context in session files)
