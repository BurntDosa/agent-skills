# agent-skills

Personal cross-harness [Agent Skills](https://agentskills.io) repository for Codex, Claude Code, Antigravity, and other compatible agents.

## Included skills

| Skill | Path | Purpose |
| --- | --- | --- |
| `setup-subagent-standard` | `skills/setup-subagent-standard/` | Inspect any repository and generate a tailored Orchestrator → Astra implementer → Independent Reviewer subagent standard and activity playbooks. |

## Install anywhere

### Recommended: `npx skills` (works across Codex, Claude Code, Antigravity, Cursor)

Install globally on a machine (uses your `gh` / Git credentials for this private repo):

```bash
npx skills add https://github.com/BurntDosa/agent-skills.git --skill setup-subagent-standard -g
```

Or install into a single repository (`.agents/skills/`):

```bash
npx skills add https://github.com/BurntDosa/agent-skills.git --skill setup-subagent-standard
```

Update later:

```bash
npx skills update
```

### Manual / offline install (no `npx`)

Clone once and symlink into whichever harnesses you use:

```bash
git clone https://github.com/BurntDosa/agent-skills.git ~/.agent-skills

# Shared discovery location (~/.agents/skills)
mkdir -p ~/.agents/skills
ln -sfn ~/.agent-skills/skills/setup-subagent-standard ~/.agents/skills/setup-subagent-standard

# Codex CLI (~/.codex/skills)
mkdir -p ~/.codex/skills
ln -sfn ~/.agent-skills/skills/setup-subagent-standard ~/.codex/skills/setup-subagent-standard

# Claude Code (~/.claude/skills)
mkdir -p ~/.claude/skills
ln -sfn ~/.agent-skills/skills/setup-subagent-standard ~/.claude/skills/setup-subagent-standard

# Antigravity (~/.gemini/config/skills)
mkdir -p ~/.gemini/config/skills
ln -sfn ~/.agent-skills/skills/setup-subagent-standard ~/.gemini/config/skills/setup-subagent-standard
```

## Using `setup-subagent-standard`

In any project, ask your agent:

> "Use `setup-subagent-standard` to set up a local subagent standard tailored to this repository's activities."

## Adding more skills

Add a new folder under `skills/<skill-name>/` with a valid `SKILL.md` (YAML frontmatter `name` + `description`), optional `assets/`, `references/`, `scripts/`, and `agents/openai.yaml`, then commit and push.
