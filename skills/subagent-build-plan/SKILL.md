---
name: subagent-build-plan
description: >-
  Executes planning output under `.cursor/plans/*.plan.md` by dispatching Cursor Task subagents per todo, updates the plan file as the single source of truth for status and execution history, validates completion against each todo's criteria, and retries with escalation until validation passes or the user changes scope. Use when the user says "run the plan", "execute plan", or "subagent-build-plan", or loads this skill to run a planning-produced plan.
disable-model-invocation: true
---

# Subagent build plan

## Upstream contract

Plans MUST come from the bundled **planning** skill. Do not define a competing schema. Executor assumes:

- **Path:** `{workspaceRoot}/.cursor/plans/{objective}.plan.md`
- **Frontmatter:** `name`, `overview`, top-level `todos`, `isProject: true`, `phases` where each phase has `name` and nested `todos`.
- **Each todo:** `id` (e.g. `1-1`), `content`, `status` (`pending` | `in_progress` | `completed` | `cancelled`), `agent_type` (`explore` | `generalPurpose` | `shell`), `parallel_group`, `depends_on`, `inputs`, `outputs`.
- **Body:** H2 `Delegation Map` and H2 `Todos` per planning rules.

**Frontmatter is authoritative for scheduling.** Update the body for readability, audit, and execution history.

## Roles

- **Orchestrator (parent agent):** Read and parse the plan, schedule runnable todos, validate completion, update the plan file on state transitions, invoke `Task` for workers.
- **Worker (subagent):** One attempt for one todo; return evidence tied to success criteria in `content` (files touched, commands run, structured summary for read-only work).

## Scheduling

1. **Phases:** Process `phases` in array order.
2. **Parallel groups:** Within a phase, order groups alphabetically (`A` before `B`). A todo runs only when every id in `depends_on` has `status: completed` in the plan file.
3. **Concurrency:** Todos that share the same `parallel_group`, have satisfied dependencies, and are not `completed`, may run concurrently, **up to four** concurrent `Task` calls.
4. **Cross-phase dependencies:** If `depends_on` references an id from an earlier phase, honor it via status in the plan file.

## Dispatch mapping

| Plan `agent_type` | `Task` `subagent_type` |
|-------------------|------------------------|
| `explore`         | `explore`              |
| `generalPurpose`  | `generalPurpose`       |
| `shell`           | `shell`                |

## Worker prompt (minimum)

Every `Task` prompt MUST include:

- `todo.id` and the verbatim `content` field.
- `inputs` and `outputs` from the plan.
- Absolute path to the plan file under execution.
- Instruction to return evidence aligned with the success criteria embedded in `content` (paths changed, commands with results, deliverable summaries for explore work).

**Retries:** Prepend a short summary of prior failure: validation errors, missing outputs, or ambiguous results.

## Completion validation

Set `completed` only if:

1. Success criteria in `content` are satisfied (for `explore`, the promised summary or artifact exists as stated).
2. Declared `outputs` exist on disk, or artifact paths explicitly required in `content` are present.

If validation is ambiguous or fails, treat the attempt as failed; do not set `completed`. Apply retry and escalation.

## Retry and escalation

- Do **not** stop the overall run because an attempt failed.
- Do **not** set `cancelled` only because attempts failed.
- Use a **new** subagent for each attempt until validation passes.

**Attempt counter:** Per `todo.id`, increment attempts during this run; record each attempt in the body **Execution log**.

**Escalation:**

- **Attempt 1:** `subagent_type` from the table for the todo's `agent_type`.
- **Attempt 2 and later:** Use `generalPurpose`, **except** when the todo is **strictly** command-and-environment automation (no repository file creation or editing) **and** failure is clearly environmental (network, tool missing, transient command error). In that narrow case, allow **one** additional `shell` attempt with enriched context before switching subsequent attempts to `generalPurpose`.

## Plan file as single source of truth

### Status synchronization

When a todo moves `pending` → `in_progress` → `completed` (or `cancelled` per user):

1. Update the matching object in top-level frontmatter `todos`.
2. Update the matching object inside the correct `phases[].todos` list (same `id`).
3. Update the H2 **Todos** section in the body so listed statuses match frontmatter.

After every write, confirm no duplicate `id` entries and no mismatched `status` for the same `id` between top-level `todos` and `phases`.

### Execution log

If the body has no H2 **Execution log**, add it **after** required planning sections (do not remove `Delegation Map` or `Todos`).

For each attempt append one line (bullet or small heading) containing: ISO 8601 timestamp (or session date if timestamps unavailable), `todo.id`, attempt number, `subagent_type` used, one-line outcome `success` or `failed: <short reason>`.

### Cancellation

Set `cancelled` **only** when the user explicitly changes scope. Require explicit user instruction before writing `cancelled`.

## Git and permissions

Do **not** commit or push as part of this workflow unless the user **explicitly** asks. Plan edits remain working-tree changes until the user commits.

## Operational notes

- Read-modify-write YAML carefully to avoid frontmatter corruption; prefer minimal edits.
- Keep worker prompts concise; enrich retries with concrete validation gaps.
- Retries may consume time and tokens; the Execution log is the audit trail.

## Cross-reference

Plan format and producer: bundled **planning** skill; deliverable pattern `.cursor/plans/{objective}.plan.md`. Do not duplicate the full planning schema here.
