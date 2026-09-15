---
name: Bergamota
description: >-
  Bergamota default agent conventions — ctx prior-session search before acting,
  surgical code, explicit git consent, planning before large builds, worktree
  isolation, parallel subagents, and evidence-based verification. Use when the
  user says Bergamota, /bergamota-mode, or asks to work in their style. Not a
  substitute for /poteto-mode unless they attach both.
disable-model-invocation: true
mode: true
icon: circles
color: orange
reminder: Bergamota — search ctx history before acting, surgical diffs only, no commit/push without permission, plan before large builds, verify before claiming done.
---

# Bergamota

Operational defaults for the user. Lighter than **poteto-mode**; attach **poteto-mode** for full pstack rigor (principles in replies, playbooks, unslop prose).

## Prior agent history (ctx)

**Default first move on non-trivial work:** search local coding-agent history with **ctx** before planning, implementing, debugging, or answering from memory. Follow `~/.cursor/skills/ctx-agent-history-search/SKILL.md` in full (CLI `ctx` and/or MCP `user-ctx`).

- **When (almost always):** continuing a project or feature; bugs and regressions; "why / how did we…"; prior decisions, attempts, failures, commands, or file touch history; planning or brainstorming that may reuse past choices; anything where another session may already hold the answer.
- **How:** read the ctx skill, then run several targeted searches — vary wording, paths, error text, branch/feature names. Prefer `ctx search` / MCP `search`; narrow with `--workspace`, `--file`, `--since`, `--provider`, `--session`. Use `--include-subagents` when reviews, implementation attempts, or failure traces may live in delegated sessions. Inspect hits with `show_event` / `show_session` before relying on them. Use `ctx sql` only when search cannot express the question.
- **Cite:** when history affects the answer or implementation, cite provider, ctx session ID, and ctx event ID when available. Summarize; do not dump raw transcripts or secrets.
- **Unavailable:** if ctx is missing or the index is empty, say local history search is unavailable and continue without inventing prior decisions.
- **Skip only when clearly history-free:** single-turn typos, pure formatting, or the user explicitly says not to search history.

## Code discipline

- **Surgical diffs only.** Smallest change that solves the request. No drive-by refactors, unrelated cleanup, or scope expansion.
- **Anti-slop.** Follow repo-root `CLAUDE.md` when present. For merges they may invoke **anti-slop** nuclear mode (`~/.cursor/skills/anti-slop/`). No narrating comments, no one-caller wrappers, no debug instrumentation left behind.
- **Implement exactly what was asked.** No unrequested features, tests, docs, or libraries.
- **Portuguese in code, English to the user.** Identifiers and product UI copy in Portuguese unless the project dictates otherwise. Chat replies in English unless they say `reply in <language>`.
- **Follow repo conventions.** Read `{repo}/AGENTS.md` and `.cursor/rules/` before editing. Match existing patterns (UI parity, service thickness, naming) instead of inventing parallel structures.
- **Fix data at the source.** Do not paper over bad API or persistence with frontend hacks. Verify and fix upstream.
- **Push back on slop.** Over-layered structure ("AI slop") → deletion and consolidation first. Say so directly.

## Delegation

- **`/poteto-mode` only when attached.** Do not auto-apply poteto playbooks or principle citations unless they invoked it in the same turn.
- **Product/design forks → brainstorm first.** Superpowers **brainstorming**; offer A/B/C choices and wait for a pick.
- **Large or cross-cutting work → planning first.** **planning** skill → `.cursor/plans/{objective}.plan.md` with phased, delegatable todos.
- **Spec then plan in sequence** when they ask for both.
- **Execute written plans** via **subagent-build-plan** unless they want a single-threaded pass.
- **Big features → isolated worktrees** when using Superpowers (`using-git-worktrees`); merge back to the canonical checkout when done. Local specs under `docs/superpowers/` stay gitignored unless they say otherwise.
- **Parallel subagents** for independent slices. Default model: **composer-2.5** unless they specify another.
- **`move_agent_to_root`** once the target project path is known.

## Process and git

- **Never `git commit` or `git push` without explicit permission.**
- **When they ask to commit:** sample `git log -15 --oneline` on the active repo (or a **shell** subagent) and match their Brazilian Portuguese subject style (past participle / noun phrase, not English `feat:`).
- **PRs via `gh`.** Check `gh pr list --head` or `gh pr view` before `gh pr create`.
- **No emojis** in replies or commit messages.
- **No new test suites** unless they explicitly ask.
- **Deploy:** use the project's deploy skill or script when one exists; do not improvise deploy steps.

## Verification

- **Reproduce before fixing.** Bugs follow report → reproduce → fix. Use **debugger** when attached. Do not ship from static analysis alone.
- **History before guessing.** On bugs, regressions, and "how did this work before", search ctx for prior attempts and decisions before inventing a theory.
- **Persistence bugs → verify storage.** For empty or wrong saved values, confirm what the backend or database actually holds before patching display-only code.
- **UI bugs → verify on the real surface.** Use the browser or runtime the user uses; remove temporary debug styling before done.
- **FINAL REVIEW** on multi-step work: requested behavior shipped, no manufactured scope, no debug leftovers, no scope creep.
- **Claim "done" only with evidence.** Run relevant project checks (build, lint, typecheck, manual smoke) when the repo provides them. Say what you ran and what passed.
- **Attached audit skills** (Thermos, poteto-mode, code-reviewer): run when they invoked them; triage findings on merit, do not churn on noise.

## UI and interaction

- **Browser-targeted edits:** short imperatives when they pick a `browser_element`. Match surrounding UI patterns.
- **Modals and copy:** when they specify title and button labels, implement exactly.

## Autonomy

- **Product/design forks:** A/B/C, wait for pick.
- **Implementation, debug, written plans:** proceed on reversible work without asking.
- **`/poteto-mode` or Thermos attached:** broader execute posture for audits; still pause on irreversible git, deploy, or data actions.
- **Pause for irreversible actions:** force-push, deploy, data deletion, commit/push without permission.

## Memory and skills

- User-level: `~/AGENTS.md`. Per-repo: `{repo}/AGENTS.md`.
- **ctx first:** `ctx-agent-history-search` (`~/.cursor/skills/ctx-agent-history-search/SKILL.md`) — extensive use is mandatory under Bergamota; MCP server `user-ctx` is an equivalent interface.
- **Related skills:** poteto-mode, planning, subagent-build-plan, debugger, unslop, anti-slop. Prefer path references over inlined copies.
