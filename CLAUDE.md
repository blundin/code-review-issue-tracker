# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Claude Code plugin that provides the `review-tracker` skill — a protocol for automatically persisting code review findings across sessions. It writes JSON files to `docs/code-reviews/` in the target project, maintaining a session file per review and a rolling `known-issues.json` aggregate.

## Repository Structure

```
.claude-plugin/plugin.json   # Plugin manifest (name, version, author, skills list)
SKILL.md                     # The skill definition: frontmatter metadata + protocol instructions
```

There is no build system, no test runner, and no package manager. This is a pure-markdown skill plugin.

## How Claude Code Plugins Work

- A plugin is a directory with `.claude-plugin/plugin.json` and one or more skill files
- Each skill file is a Markdown file with YAML frontmatter (`name`, `description`, `allowed-tools`)
- The frontmatter `description` field is what Claude Code reads to decide when to auto-invoke the skill
- `plugin.json` `skills` array lists paths to skill files; `"./"` means `SKILL.md` in the repo root
- Plugins are installed by symlinking the repo into `~/.claude/skills/<name>/`

## Editing the Skill

`SKILL.md` has two parts that must stay in sync:

1. **Frontmatter** (`---` block at top): controls auto-invocation triggers — the `description` field is the primary trigger text Claude Code matches against
2. **Protocol body**: the actual instructions Claude follows when the skill runs — file layout, JSON schemas, step-by-step protocol

When adding a new finding field to the JSON schema, update both the example in Step 2 and the `known-issues.json` example in Step 4.

## Plugin Manifest

`plugin.json` must remain valid against `https://anthropic.com/claude-code/plugin.schema.json`. Key fields:

- `name` must match the symlink name under `~/.claude/skills/`
- `version` follows semver; bump on any behavior change to the skill protocol
- `skills` is an array of relative paths to skill Markdown files

## Deployment

Per the global CLAUDE.md plugin workflow:
1. Plugin lives at `~/developer/ai-tools/review-tracker/`
2. Symlinked to `~/.claude/skills/review-tracker`
3. Registered in `~/developer/ai-tools/setup.sh` PLUGINS list
4. Has its own git repo and GitHub remote
