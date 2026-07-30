# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is **Adam's Builder Club: `build`** — the companion repo for a YouTube series and community teaching people to use Claude Code. It is a content/template repo, not a software project: there is no build, lint, or test suite, no package manager, and no application code. Everything here is Markdown (guides, prompts, `SKILL.md` files) plus one shell installer (`install.sh`).

Treat changes here as documentation/content edits: correctness means "matches how Claude Code and Claude Desktop actually behave," not "passes CI."

The installer is Mac/Linux only (gated on `uname -s`); Windows users are pointed at manual steps in `SETUP.md`.

## Repo layout

```
build/
├── install.sh                       Idempotent installer (see below)
├── SETUP.md                         Three install paths (Claude / terminal / manual)
├── setup-skill/                     The interactive /setup skill (writes ~/.claude/CLAUDE.md)
└── claude-code-beginners/           Companion files for the YouTube series, one folder per video chapter
    ├── 00-installation/
    ├── 01-claude-md/                 Global + project CLAUDE.md templates (*.example files)
    ├── 02-prompts/
    ├── 03-skills-starter/            brainstorm/, prep-meeting/, weekly-review/ — each a SKILL.md
    ├── 04-routines/                  daily-brief/, morning-prep/ — skills meant to run on a schedule
    ├── 05-context-capture/           Architecture writeup only, no source (personal data)
    ├── 06-portfolio-website-demo/
    └── tools-i-use.md
```

## The install flow (how the pieces connect)

1. A new user clones the repo and runs `./install.sh` from the repo root.
2. `install.sh` copies every subfolder of `03-skills-starter/` and `04-routines/` that contains a `SKILL.md` into `~/.claude/skills/`, then copies `setup-skill/` to `~/.claude/skills/setup/`. It backs up an existing `~/.claude/CLAUDE.md` (to `CLAUDE.md.backup-<timestamp>`) but never overwrites it directly, and never touches secrets.
3. The user then runs `/setup` inside Claude, which follows `setup-skill/SKILL.md`: asks ~6-8 questions one at a time, drafts a personalized `~/.claude/CLAUDE.md` from the template embedded in that file, shows the draft for approval, then writes it (backing up any existing file first).
4. Optional add-ons (gstack, compound-engineering) are offered by `/setup` but never installed without explicit yes.

When editing `install.sh`, preserve this contract: never overwrite `~/.claude/CLAUDE.md` without a timestamped backup, never install optional add-ons automatically, and keep it Mac/Linux-only (`uname -s` gate) with a clear failure on other OSes.

## Verifying changes

There is no CI, so verification is manual:
- **`install.sh` edits**: syntax-check with `bash -n install.sh`, then dry-run against a scratch home (`HOME=/tmp/fake-home bash install.sh`) and inspect the resulting `~/.claude/skills/` tree. Run it a second time to confirm idempotency and that the backup logic fires when a `CLAUDE.md` already exists.
- **Skill edits**: re-run `./install.sh` (idempotent), then invoke the skill from Claude and confirm the flow matches the `SKILL.md` — especially the "ask one question at a time" and "show draft before writing" steps.
- **Docs**: no automation. Read for accuracy against actual Claude Code / Claude Desktop behavior; broken instructions here are the main failure mode.

## Working with skills in this repo

Every skill is a folder with a `SKILL.md` containing YAML frontmatter (`name`, `description`) followed by plain-language instructions — no code. The `description` field is what Claude uses to decide when to auto-invoke the skill, so it must state concrete trigger phrases, not vague summaries.

Conventions used consistently across the existing skills (`setup-skill/SKILL.md`, `03-skills-starter/*/SKILL.md`, `04-routines/*/SKILL.md`) — follow them when adding or editing a skill:
- An "Operating principles" or equivalent section stating how to interact with the user (e.g. ask one question at a time, show your work before acting).
- Numbered step-by-step flow as the main body.
- A "Don't do" section listing explicit anti-goals (e.g. never write to disk before user approval, never install anything requiring sudo without asking).
- Routines in `04-routines/` additionally document a config file the skill reads from `~/.claude/<routine-name>.config.md`, since they're designed to run unattended on a schedule (Desktop Scheduled Tasks, not Cloud Routines — see `claude-code-beginners/04-routines/setup-routines.md` for the distinction between the two).

## Content conventions

- `*.example` files (e.g. `claude-code-beginners/01-claude-md/global-CLAUDE.md.example`) are templates meant to be copied and edited by the end user, not filled in with this repo's own values.
- Chapter folders under `claude-code-beginners/` map 1:1 to sections of the YouTube video; keep the chapter table in `claude-code-beginners/README.md` in sync if chapters are added, renamed, or reordered.
- `LAUNCH/` and `ideas-saved-decisions.md` are gitignored — internal launch prep, never commit content there or suggest un-ignoring it.
- License is MIT with no attribution required; don't add attribution requirements to new files.
- The repo layout and install flow are described in three places: `README.md`, `SETUP.md`, and this file. Changes to chapter names, install steps, or the `install.sh` contract must be reflected in all three — none is the single source of truth.
