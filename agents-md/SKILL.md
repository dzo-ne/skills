---
name: agents-md
description: Create or tighten a minimal AGENTS.md (plus a CLAUDE.md that imports it) for the current repository. Leaner alternative to /init that also serves non-Claude agents.
argument-hint: "[optional focus, e.g. 'monorepo: only packages/api']"
disable-model-invocation: true
---

# Minimal agent instructions

Goal: one short AGENTS.md at the repo root that any coding agent (Claude Code,
Codex, Cursor, Copilot, Gemini CLI…) reads, plus a CLAUDE.md that starts with
`@AGENTS.md`. The file is loaded into every session, so every line must earn its tokens.

Extra focus from the user, if any: $ARGUMENTS

## Process
1. Survey before writing: get an overview of tracked files without dumping them all
   (`git ls-files | head -100`, or a depth-limited tree; outside git, `find -maxdepth 2`),
   read README and any docs/, build/test config (package.json, pyproject, Makefile,
   pytest.ini, CI workflows, lint/format config, env files), and the top docstring
   or header of each main module. Read deeper only where a rule or boundary needs confirming.
2. Find existing agent instructions: AGENTS.md, CLAUDE.md, GEMINI.md, `.cursorrules`,
   `.cursor/rules/`, `.github/copilot-instructions.md`. Improve rather than overwrite:
   merge what is still true into AGENTS.md, and report the leftover files and any
   duplication between them.
3. Monorepos: root AGENTS.md holds only repo-wide rules. Rules that apply to one
   package go in a nested AGENTS.md in that package (most agents load the nearest one).

## What to include (as short as it can be; stop at ~60 lines / ~1k tokens)
- **Project**: one or two lines on what it is and its stack.
- **Layout**: only relations that steer decisions — ownership and data flow
  (single source of truth for X, who writes the DB, which layer talks to which,
  where new X must be added). One line each.
- **Rules**: invariants and footguns an agent would otherwise get wrong:
  data safety, "only place for X", migration rules, test isolation,
  commit conventions. Take them from existing docs and code; don't invent them.
- **Commands**: env setup, test, run, build, dev server, and any flag that
  switches between safe and destructive modes (dry run vs. execute).
- **More**: plain-text pointers to deeper docs (path + one-line scope).
  Never use @imports for them, so they're read only on demand.

## CLAUDE.md
First line `@AGENTS.md`. Below it, only what applies to Claude Code alone
(hooks, skills, slash commands), if anything. Shared rules never go here.

## Leave out
- Anything a grep answers in seconds: file lists, signatures, import/call graphs.
- Anything a linter or formatter already enforces.
- Copies of existing docs, feature lists, marketing text.
- Anything you couldn't verify in the code.

## Verify
- Check that every symbol, path and command you name exists (grep definitions,
  read config). Fix or drop anything that doesn't.
- If the test command is quick and has no side effects, run it once to confirm it works.
- Collect contradictions between docs and code (e.g. README names the wrong env).
  Report them; don't silently pick one.

## Constraints
- Don't commit; leave the changes for the user to review.

## Report
Finish with the line count of AGENTS.md, what you removed from existing
instructions (so nothing disappears unseen), and a short list of anything you
were unsure about or found contradictory.
