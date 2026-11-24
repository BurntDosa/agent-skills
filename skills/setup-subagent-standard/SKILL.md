---
name: setup-subagent-standard
description: >-
  Bootstrap or refresh a tailored local subagent orchestration standard (Orchestrator,
  Astra gpt-6.2-astra implementer, and independent Opus reviewer) for any repository.
  Use when setting up multi-agent delegation rules, mapping a project's recurring
  activities to effort levels and verification gates, or adapting the subagent brief
  templates to a new codebase.
metadata:
  short-description: Tailor a local subagent standard to any repository
---

# Setup Subagent Standard

Generate or refresh a project-specific `subagent-standard.md` tailored to the current repository's real architecture, recurring activities, verification commands, and instruction-file layout.

Do not copy assumptions, versions, commands, or rules from any other repository. Every rule and command written into the target project's standard must come from inspecting that repository.

## Supporting files

- Starter template to adapt: [assets/subagent-standard.template.md](assets/subagent-standard.template.md)
- Detailed GPT-6.2 / Astra prompting playbook (read when writing or tuning complex briefs, effort rubrics, parallel tracks, or UI verification rules): [references/prompting-gpt-6-2-astra.md](references/prompting-gpt-6-2-astra.md)

## Workflow

### 1. Inspect the target repository first

Before writing any file:

1. Locate the repo root (`git rev-parse --show-toplevel`, or cwd if not in Git) and check `git status --short`, `.gitignore`, and `.git/info/exclude`.
2. Read existing instruction and project files present in the repo:
   - Agent instructions: `AGENTS.md`, `AGENTS.override.md`, `CLAUDE.md`, `CLAUDE.local.md`, `GEMINI.md`, `.codex/`, `.claude/`, `.agents/`, `.ai/`.
   - Project manifests and CI: `README.md`, `CONTRIBUTING.md`, `package.json`, `pyproject.toml`, `pom.xml`, `Cargo.toml`, `go.mod`, `Makefile`, `compose*.yaml`, `.github/workflows/`, `docs/`.
3. Identify these five project facts from source:
   - **Instruction-file precedence & traps:** Check whether `AGENTS.override.md` exists (Codex loads at most one instruction file per directory and prefers `AGENTS.override.md` over `AGENTS.md`), whether a framework CLI overwrites root `AGENTS.md`, and which files are tracked vs ignored.
   - **Recurring project activities:** Map real directories and workflows into 5–8 concrete activities (for example: backend/API, database migrations, frontend/UI, mobile/simulator, CLI/daemon, infra/containers, cross-system debugging, security/auth, docs/specs).
   - **Real verification commands & constraints:** Extract exact lint, format, typecheck, build, unit, and integration commands, including required wrappers, container runtimes, or shared-machine limits.
   - **Non-inferable invariants & traps:** Capture pinned versions, deprecated/forbidden APIs, auth/data boundaries, idempotency/transaction rules, naming conventions, protected directories, and gated actions (commits, pushes, deploys, destructive resets).
   - **Notes / handoff conventions:** Note whether the repo maintains handoff logs, ADRs, learning notes, or changelogs, and who owns updating them.

### 2. Choose placement (local-only by default)

- **Default (local-only):** Write `local-notes/subagent-standard.md` and exclude it via `.git/info/exclude` (never modify tracked `.gitignore` for personal local setup).
- **Tracked (only if the user explicitly asks to share/commit it):** Write to the path the user chooses (for example `docs/subagent-standard.md` or `.agents/rules/subagent-standard.md`) and skip `.git/info/exclude`.
- **Non-colliding loader pointers (local-only mode):**
  - Only add lightweight pointers (such as `CLAUDE.local.md` or `GEMINI.md`) if they do not exist as tracked files and will not shadow or break existing instruction files.
  - Never overwrite or shadow tracked `AGENTS.md`, `AGENTS.override.md`, or `CLAUDE.md`.
  - If a file already exists and will change, preserve unrelated instructions rather than clobbering them.

### 3. Generate the tailored `subagent-standard.md`

Start from [assets/subagent-standard.template.md](assets/subagent-standard.template.md) and replace every `{{PLACEHOLDER}}` with verified repository facts:

1. **Roles & accountability:** Keep the Orchestrator → Implementer (Astra / `gpt-6.2-astra`) → Independent Reviewer (Opus) split, while keeping runtime discovery mandatory (confirm exact provider IDs, model IDs, and effort options from the active orchestration environment before dispatch; never silently substitute a model).
2. **Activity Playbooks table:** Add one concrete row per real activity discovered in Step 1. For each row specify:
   - Exact directories or subsystems it covers
   - Default checkout strategy (`shared` with strictly disjoint owned files vs `worktree`)
   - Default Astra `reasoningEffort` (`low`, `medium`, `high`, `xhigh`, `max`, or `ultra`) matched to uncertainty and failure consequences—not diff size
   - Exact verification commands and negative/edge-case evidence (including real-size visual/interactive checks for UI or simulator checks for mobile)
   - Whether independent Opus review is required, conditional, or optional
3. **Activity-specific brief add-ons:** Include concise add-ons for the repo's top 2–4 recurring activities pointing to real precedent files and commands in this repo. Consult [references/prompting-gpt-6-2-astra.md](references/prompting-gpt-6-2-astra.md) when tailoring effort guidance, brief sections, parallel subagent rules, or visual verification criteria.
4. **Invariants, docs & simplicity:** Include verified non-inferable rules, Context7 doc-lookup guidance (for library/SDK/platform docs instead of guessing), simplicity discipline (reuse existing code and platform primitives first; never cut validation, security, or tests), and shared-machine hygiene.

### 4. Validate and report

1. Confirm every file path and command cited in the generated standard actually exists in the repository.
2. Run `git status --short`.
3. For local-only files, run `git check-ignore -v <created-files>` to confirm Git ignores them cleanly and no tracked files were dirtied unintentionally.
4. Preserve all existing uncommitted work; do not commit, push, install dependencies, or modify application source code unless explicitly requested.
5. Report:
   - File paths created or updated (and ignore status)
   - How each active agent harness loads or references the standard
   - Summary of the Activity Playbooks tailored for this repo
   - Any instruction-file precedence caveats or open choices for the user
