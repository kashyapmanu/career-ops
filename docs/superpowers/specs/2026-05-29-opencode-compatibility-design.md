# Design: OpenCode Full Parity + Compatibility Fix

**Date:** 2026-05-29
**Status:** Approved
**Scope:** Make OpenCode a first-class citizen alongside Claude Code

---

## Problem

Career-ops mentions OpenCode support in docs and README but has no `.opencode/` directory. The skill is discovered via `.agents/skills/` fallback path, batch mode references `claude -p` (which doesn't exist in OpenCode), and there are no OpenCode commands defined. OpenCode works partially by accident, not by design.

## Goals

1. OpenCode is a first-class citizen — not a fallback path
2. Platform-specific SKILLs — each platform gets an optimized SKILL.md
3. Single `/career-ops` command — same UX as Claude Code
4. Batch mode uses `opencode run` — replace `claude -p` references
5. No `opencode.json` — rely on AGENTS.md + SKILL.md
6. Modes files stay shared — no duplication of evaluation logic

## Non-Goals

- No changes to `modes/*.md` (shared evaluation logic)
- No changes to user data files (`cv.md`, `config/profile.yml`, `portals.yml`)
- No changes to `*.mjs` scripts (platform-agnostic Node.js)
- No changes to `.claude-plugin/` (Claude Code marketplace)
- No changes to `.qwen/skills/` (Qwen support, out of scope)

---

## Design

### 1. Platform-Specific SKILLs

**Rename** `.agents/skills/career-ops/SKILL.md` → `.claude/skills/career-ops/SKILL.md`

This is Claude Code's native skill path. The existing SKILL.md content stays mostly the same, with these Claude Code-specific patterns preserved:
- `arguments: mode` frontmatter
- `Agent(subagent_type="general-purpose", ...)` for subagent dispatch
- `claude -p` for batch/headless mode

**Create** `.opencode/skills/career-ops/SKILL.md`

OpenCode's native skill path. Same routing logic and mode instructions, but with OpenCode-specific patterns:
- No `arguments` frontmatter (OpenCode doesn't use it)
- `@general` for subagent dispatch (OpenCode's native subagent invocation)
- `opencode run` for batch/headless mode
- References `$ARGUMENTS` routing from the command

**Shared:** All `modes/*.md` files remain platform-agnostic. The SKILLs just load them differently.

### 2. OpenCode Command

**Create** `.opencode/commands/career-ops.md`

```markdown
---
description: AI job search command center — evaluate offers, generate CVs, scan portals, track applications
---

Read and follow the skill instructions in .opencode/skills/career-ops/SKILL.md

Mode argument: $ARGUMENTS
```

Single entry point, same as Claude Code. The skill handles all mode routing. No separate `/career-ops-scan`, `/career-ops-pdf` etc.

### 3. Batch/Headless Mode

Update all `claude -p` references to support both platforms:

| Platform | Command |
|----------|---------|
| Claude Code | `claude -p "prompt"` |
| OpenCode | `opencode run "prompt"` |

**Files to update:**
- `AGENTS.md` — Headless / Batch Mode table (document both)
- `modes/batch.md` — batch processing instructions
- `batch/batch-prompt.md` — worker invocation instructions

Each platform's SKILL.md uses its own command. Shared docs document both.

### 4. AGENTS.md and CLAUDE.md Split

- **`AGENTS.md`** — Stays shared. Remove Claude Code-specific defaults. Document both `claude -p` and `opencode run` in the batch table. Reference both slash command patterns (`/career-ops` for both platforms).
- **`CLAUDE.md`** — Becomes Claude Code-only. Keep `@AGENTS.md` reference plus Claude Code-specific additions (`.claude-plugin/` marketplace). Remove full content duplication.
- **`GEMINI.md`** — Stays as-is (`@./AGENTS.md`).

### 5. README and Docs Updates

- **`README.md`** — Update OpenCode section to reflect new `.opencode/` setup. Show single `/career-ops` command pattern and correct SKILL.md path.
- **No new docs** — existing structure is fine, just needs accuracy fixes.

---

## Files Changed

| File | Action |
|------|--------|
| `.opencode/skills/career-ops/SKILL.md` | **Create** — OpenCode-specific skill |
| `.opencode/commands/career-ops.md` | **Create** — Single master command |
| `.claude/skills/career-ops/SKILL.md` | **Rename from** `.agents/skills/career-ops/SKILL.md` |
| `.agents/skills/career-ops/SKILL.md` | **Delete** — OpenCode now uses `.opencode/skills/`, Claude Code uses `.claude/skills/`. The `.agents/` fallback is no longer needed. |
| `AGENTS.md` | **Edit** — Make platform-agnostic, document both CLIs |
| `CLAUDE.md` | **Edit** — Remove duplication, keep Claude Code-only notes |
| `README.md` | **Edit** — Fix OpenCode section accuracy |
| `modes/batch.md` | **Edit** — Update batch command references |
| `batch/batch-prompt.md` | **Edit** — Update worker invocation |

## Files NOT Changed

- `modes/*.md` — Shared evaluation logic
- `config/profile.yml`, `cv.md`, `portals.yml` — User data
- `*.mjs` scripts — Platform-agnostic Node.js
- `.claude-plugin/` — Claude Code marketplace
- `.qwen/skills/` — Qwen support
- `data/`, `reports/`, `output/` — Data files

---

## Verification

After implementation:
1. OpenCode: `opencode` → `/career-ops` → should show discovery menu
2. OpenCode: `/career-ops scan` → should load SKILL.md and execute scan mode
3. Claude Code: `/career-ops` → should still work as before (no regression)
4. Both platforms: batch mode references are correct in docs
5. `node verify-pipeline.mjs` — pipeline integrity still passes
