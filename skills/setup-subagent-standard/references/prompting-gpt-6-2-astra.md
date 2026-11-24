# Prompting GPT-6.2 / Astra in Codex (Generic Orchestrator Reference)

Reference for orchestrators delegating implementation, debugging, exploration, or visual work to Codex (`gpt-6.2-astra` / `gpt-6.2-sol`).

## 1. Core mental model

- **Brief like a senior colleague who knows the codebase.** State the goal, constraints, and real precedent paths directly instead of micromanaging step-by-step mechanics.
- **Lead with review criteria.** State explicitly what you will check when reviewing the diff (invariants, failure handling, API/schema shape, negative security cases, test coverage, visual fidelity at real size).
- **Define "finished" and investigation depth.** Specify exact verification commands and, for debugging or research, require testing the leading hypothesis against at least one competing explanation.
- **Skip legacy verbosity boilerplate.** GPT-6.2 writes concisely by default; omit filler instructions like "be concise, no fluff" unless a specific output format is needed.
- **Leave routine implementation choices open.** Constrain non-inferable repository rules and invariants; let the implementer infer idioms and style from existing code and tests.

## 2. Explicit effort and model selection

Always discover available providers, model IDs, and options before the first cross-provider delegation.

- **Critical gotcha:** Many orchestration environments default `reasoningEffort` for `gpt-6.2-astra` to **`"low"`**. Always set `reasoningEffort` explicitly in task options so nontrivial tasks are not under-powered.
- Match effort to **uncertainty and failure consequences**, not diff size (a 5-line concurrency or auth fix with interacting failure modes still warrants `high` or `xhigh`):
  - `low` — mechanical, narrowly bounded edits or quick lookups.
  - `medium` — single-module features, straightforward bugs, or focused tests.
  - `high` — default for nontrivial work: cross-component tracing, multi-step changes, edge cases, UI polish.
  - `xhigh` — interacting failure modes, migrations, tight security/data invariants.
  - `max` — exceptional single-track depth where correctness outweighs speed/cost.
  - `ultra` — large, cleanly decoupled work where proactive parallel sub-tracks are genuinely warranted.

## 3. High-signal brief anatomy

Delegated tasks generally start with **only the brief** (no parent conversation history). In isolated worktrees, uncommitted edits and local-only files are not copied automatically. Make every brief self-contained:

1. **Goal & context** — What needs to work, why, and exact precedent files/symbols to follow.
2. **Scope & ownership** — Exact owned files/modules, non-goals, and checkout mode (`shared` vs `worktree` + base ref). For parallel workers in a shared checkout, make owned file sets strictly disjoint.
3. **Non-inferable rules** — Version pins, forbidden APIs, security/data boundaries, documentation lookup requirements (e.g., Context7), simplicity expectations, and note/handoff ownership. Never include secrets.
4. **Review criteria** — Concrete invariants, edge cases, and negative tests the reviewer will check first.
5. **Verification & machine hygiene** — Exact commands to run, shared-machine CPU/memory courtesy, and cleanup of temporary servers/files created by the task.
6. **Stop conditions & report format** — When to pause instead of guessing, plus required summary (changed files, commands + actual pass/fail results, unverified areas, open risks).

## 4. Parallel work and nested subagents

- **Avoid redundant nesting.** If the orchestrator is already fanning out multiple parallel workers, keep each worker single-threaded.
- **Grant nested subagents explicitly when appropriate.** Name the independent tracks, whether the worker must wait for all tracks before concluding, and the consolidated output format.
- **Prefer read-heavy parallelism** (exploration, multi-angle audits, log triage). Avoid overlapping writers on the same files.

## 5. Visual, UI, and interactive work

When delegating UI or visual tasks:

1. Provide a visual reference (screenshot, mockup, or design tokens file) if a specific look is expected.
2. State real-world display constraints (target desktop/mobile viewports, theme modes, icon sizes, empty/loading/error states).
3. Require **build → render → inspect at actual viewing size → refine** (details that look fine zoomed in often break at real size).
4. Require exercising real interactions (clicks, forms, filters, keyboard navigation, state updates), not just static renders.

## 6. Local rule-loading traps to check in any repo

Before adding local instruction pointers:

- Check whether root `AGENTS.override.md` exists (Codex loads at most one instruction file per directory and prefers `AGENTS.override.md` over `AGENTS.md`).
- Check whether any project tool/CLI regenerates or overwrites `AGENTS.md`.
- Keep local-only personal standards excluded via `.git/info/exclude` rather than modifying tracked `.gitignore`, and verify with `git check-ignore -v` and `git status`.
