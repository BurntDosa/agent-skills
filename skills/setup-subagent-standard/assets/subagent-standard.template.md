# Local Subagent Standard — {{PROJECT_NAME}}

This is the working delegation standard for this checkout. The user’s current instructions, applicable system and tool restrictions, and repository team workflow take precedence. This document describes a preferred team; it does not override a restriction on delegation or authorize commits, pushes, merges, deployments, or external actions outside the task.

## Roles and accountability

- **{{ORCHESTRATOR_ROLE}} — orchestrator.** Understand the request, clarify material ambiguity, establish scope and acceptance criteria, choose effort, write self-contained briefs, coordinate disjoint ownership, inspect diffs and actual verification output, resolve review findings, and report honestly to the user. Remains accountable for the finished work.
- **Astra (`gpt-6.2-astra`) — implementer.** Investigate the relevant code, implement the agreed scope cleanly, run required checks, preserve concurrent edits, and report changed files, actual results, and open risks. Use Astra for implementation under this standard; do not silently substitute a different model.
- **{{REVIEWER_ROLE}} — reviewer.** Independently review the resulting change and connected code against the brief (review only; do not edit). Check correctness, regressions, safety/security, missing edge cases, test coverage, and unnecessary complexity. Report actionable findings ordered by severity with file and line evidence and verification limits.

Confirm the exact available provider, model identifiers, and effort options from the active orchestration environment before dispatch. Role names here describe requested roles, not guaranteed identifiers. If a requested model is unavailable, report that rather than silently switching. Do not launch a review when the user requests fewer agents or an applicable instruction prohibits it.

## Workflow

1. Read the user’s request, {{REPO_INSTRUCTION_FILES}}, and current `git status` / `git diff`. Preserve dirty and concurrent work, and agree strictly disjoint file ownership before parallel edits.
2. Define the outcome, scope, non-goals, invariants, ownership, review criteria, and verification commands. Make every delegated brief self-contained: a child may not receive parent conversation history.
3. Choose Astra’s `reasoningEffort` based on uncertainty and consequences—not lines changed—and set it explicitly (orchestrators often default to `low`).
4. Dispatch implementation using the Implementation Brief below:
   - **Shared checkout:** List exact owned files/modules and warn about concurrent edits.
   - **Isolated worktree:** Specify the correct base ref and include any needed uncommitted or local-only context in the brief; do not assume local files were copied. Never include secrets.
5. Once implementation is ready, ask the reviewer for an independent review using the Review Brief when warranted or requested. Provide the exact diff or revision, acceptance criteria, and constraints, and ensure the reviewer can see the actual changes across worktrees.
6. Assess findings against evidence. Fix small issues directly or delegate substantial fixes, verify the fixes, and open a fresh review task when unresolved risk warrants it. Never claim a review passed without its result.
7. Check the final diff and actual results against the user’s request, report what was verified and what remains unverified, and respect all user approval boundaries.

For trivial work, an orchestrator may handle a small correction directly when instructions and environment permit it.

## Astra effort selection

| Effort | Choose for |
| --- | --- |
| `low` | Mechanical, narrowly bounded edits or simple lookups with little uncertainty. |
| `medium` | Straightforward single-module implementation, targeted tests, or a well-understood bug. |
| `high` | Default for nontrivial work: multi-step changes, meaningful edge cases, cross-component tracing, or visual/UI polish. |
| `xhigh` | Interacting failure modes, schema/data migrations, security/concurrency invariants, or multi-constraint work where every invariant must hold together. |
| `max` | Exceptional single-track depth where correctness outweighs speed and cost. Use sparingly. |
| `ultra` | Large, cleanly decomposable work where the environment supports it and parallel tracks are genuinely justified. It does not automatically authorize nested subagents. |

Use only effort levels exposed by the active provider.

## Project invariants and tooling rules

- **Non-inferable repository rules:** {{NON_INFERABLE_REPO_RULES}}
- **Documentation lookup:** Use Context7 (or the project’s documented docs workflow) for library, SDK, framework, or platform API documentation instead of guessing from memory; report clearly when doc lookup is unavailable.
- **Simplicity discipline:** Reuse existing project primitives and platform capabilities first; write only what the task needs; never remove validation, security checks, or tests to reduce diff size.
- **Gated or forbidden actions:** {{GATED_ACTIONS}}
- **Secrets and sensitive data:** Never place credentials, tokens, passwords, or private user data in briefs, logs, tests, events, or tracked files.

## Activity playbooks

| Activity | Scope / Subsystems | Checkout | Default Astra Effort | Verification Gates | Independent Review |
| --- | --- | --- | --- | --- | --- |
| {{ACTIVITY_1}} | {{PATHS_1}} | {{shared / worktree}} | {{EFFORT_1}} | {{CHECKS_1}} | {{REVIEW_1}} |
| {{ACTIVITY_2}} | {{PATHS_2}} | {{shared / worktree}} | {{EFFORT_2}} | {{CHECKS_2}} | {{REVIEW_2}} |
| {{ACTIVITY_3}} | {{PATHS_3}} | {{shared / worktree}} | {{EFFORT_3}} | {{CHECKS_3}} | {{REVIEW_3}} |

### Activity-specific guidance

{{ACTIVITY_SPECIFIC_GUIDANCE}}

## Implementation brief

```text
Role: Astra implementer. You are not alone in the codebase; preserve all unrelated and concurrent edits.

Goal and context: <what needs to work and why>
Activity type: <matching row from Activity playbooks>
Scope and ownership: <exact owned files/modules, allowed changes, non-goals, shared vs worktree base>
Requirements and invariants: <user criteria, non-inferable repo rules, compatibility,
  failure behavior, data and security boundaries>
Precedent and inputs: <relevant files/symbols, doc lookup expectations, visual references if UI>
Review criteria: <concrete behaviors, edge cases, negative cases, and risks>
Verification: <exact commands and any shared-machine constraints>
Notes ownership: <who updates project notes/docs/handoff, if applicable>
Stop conditions: <when to stop and report instead of guessing>
Report: changed files, behavior summary, exact commands and actual results, unverified areas and open risks.
```

Write colleague-style briefs: specify the outcome, constraints, and review bar, then leave routine implementation details to the implementer. Explicitly state permission for nested subagents only when active instructions allow it and tracks are independent (prefer read-heavy parallelism; avoid overlapping writers). Never include credentials in task text.

## Review brief

```text
Role: {{REVIEWER_ROLE}} reviewer. Review only; do not edit files.

Change and revision: <exact diff, branch/commit, or shared checkout state>
Goal and acceptance criteria: <what the user requested>
Scope and constraints: <boundaries, invariants, repo rules, and owned files>
Focus: correctness, regressions, failure handling, security/data integrity,
  test coverage, and unnecessary complexity, as applicable.

Read connected code as needed. Return actionable findings ordered by severity,
with file and line references, trigger, and impact. State checks actually run and
any unverified areas. If there are no findings, say so and identify residual risks.
```

## Orchestration and machine hygiene

- Follow live orchestration instructions: discover capabilities before the first cross-provider delegation, pass exact provider/model/effort options, do not poll running delegates, and open a fresh review task for each review or re-check.
- Keep parallel builds, containers, and test runs proportionate to the shared machine. Stop only servers/jobs started for the task, and remove only temporary files created for it. Do not restart another agent’s server or discard another person’s work.
- For visual or UI changes: provide references and target viewport sizes; require inspecting the rendered output at actual user viewing size and exercising real interactions where tools permit.

## Local scope and maintenance

- {{LOCAL_SCOPE_NOTES}}
- Repository rules must always be checked against their current source files rather than copied as fixed version assumptions.
