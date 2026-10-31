---
name: agents-md
description: Create or tighten a minimal AGENTS.md (plus a CLAUDE.md that only imports it) for the current repository. Use when the user asks to set up, write, refresh or trim agent instructions, AGENTS.md or CLAUDE.md for a repo.
argument-hint: "[optional focus, e.g. 'monorepo: only packages/api']"
disable-model-invocation: true
---

# Minimal agent instructions

Goal: one short AGENTS.md at the repo root that any coding agent (Claude Code,
Codex, Cursor, Copilot, Gemini CLI…) reads, plus a CLAUDE.md containing only
`@AGENTS.md`. The file is loaded on every turn, so every line must earn its tokens.

Extra focus from the user, if any: $ARGUMENTS

## Process
1. Survey before writing: list tracked files (`git ls-files`), read README and any docs/,
   build/test config (package.json, pyproject, Makefile, pytest.ini, CI workflows,
   env files), and the top docstring or header of each main module.
   Read deeper only where a rule or boundary needs confirming.
2. If an AGENTS.md or CLAUDE.md already exists, improve it rather than overwrite.
   If CLAUDE.md has real content, move it into AGENTS.md first.

## What to include (target 30–60 lines, under ~1k tokens)
- **Project**: one or two lines on what it is and its stack.
- **Layout**: only relations that steer decisions — ownership and data flow
  (single source of truth for X, who writes the DB, which layer talks to which,
  where new X must be added). One line each.
- **Rules**: invariants and footguns an agent would otherwise get wrong:
  data safety, "only place for X", append-only migrations, test isolation,
  style constraints, commit conventions. Take them from existing docs and code;
  don't invent them.
- **Commands**: env setup, test, run, build, dev server, plus dry-run/--execute flags.
- **More**: plain-text pointers to deeper docs (path + one-line scope).
  Never use @imports for them, so they're read only on demand.

## Leave out
- Anything a grep answers in seconds: file lists, signatures, import/call graphs.
- Copies of existing docs, feature lists, marketing text.
- Anything you couldn't verify in the code.

## Verify
- Check that every symbol, path and command you name exists (grep definitions,
  read config). Fix or drop anything that doesn't.
- Collect contradictions between docs and code (e.g. README names the wrong env).
  Report them; don't silently pick one.

## Constraints
- Don't commit until the user has seen the file.
- Commits: no Co-Authored-By trailers for AI agents.

## Report
Finish with the line count of AGENTS.md and a short list of anything you were
unsure about or found contradictory.
