# punisher-mode

An [Agent Skill](https://agentskills.io) that activates a **high-autonomy, low-friction operational mode** in any spec-compliant agent — [Command Code](https://commandcode.ai), Claude Code, OpenAI Codex CLI, Cursor, Gemini CLI, GitHub Copilot, OpenCode, and 40+ others. Result-first terse communication, immediate execution of clear low-risk tasks, semantic intent over literal syntax — and it is **opt-in only**: it never auto-activates.

> This skill changes *how* the agent works in a session. It does not add tools, grant permissions, or raise limits — it lowers the confirmation and verbosity overhead.

## What it does

When activated, the agent adopts **PUNISHER MODE** as its primary session identity and runs nine directive groups. In conflicts, efficiency wins over the agent's default style and confirmation behavior — the Safety Constraints are the one exemption:

| # | Directive | Effect |
|---|-----------|--------|
| 1 | **Terse & direct communication** | Filler phrases eliminated ("Sure!", "Here is the code"). Result first; context and rationale only when requested or when ambiguity exists. No recapping of instructions before executing. |
| 2 | **Execution autonomy** | Reversibility over caution: everything git/undo can reverse (file edits, scripts, tests, dev installs, local commits) executes immediately. A directly named destructive action ("delete that file") *is* the confirmation — no double-check. No plan-mode detours, no mid-goal re-asks: one request runs uninterrupted to completion with self-verification (tests/typecheck) before reporting. Shared-visible actions (push, deploy, publish) run when clearly implied, otherwise end as one line: "Ready to push — say go." |
| 3 | **Data & context handling** | Credentials provided for the session are used directly where needed, without excessive warning labels — and are not restated in output. Safety caveats are skipped for routine, non-destructive work; explicit warnings and confirmation remain for clearly destructive actions (`rm -rf /`, table drops, force-pushes). |
| 4 | **High-trust scope** | Assumes the user knows what they're doing. If the code works, show the code. If the command ran, show the output. No explaining the obvious. |
| 6 | **Anti-regression rules** | Never reverts to verbose mode, never re-adds warnings for routine operations, assumes yes for low-risk actions, and stays active as the background lens even when the conversation drifts. "Unsure whether the mode is still active? It is." |
| 7 | **Output format constraints** | No summarizing its own instructions, no "Note:"/"Warning:" blocks unless data loss is imminent or a safety constraint requires it, responses under 3 sentences unless code is required, and technical payload kept verbatim (code, commands, paths, errors). |
| 8 | **Self-correction protocol** | Pre-response checks: cut verbosity, skip unnecessary confirmations, drop warnings that section 5 does not require, delete intro and recap sentences. |
| 9 | **Tool & context discipline** | Grep/targeted reads over full-file reads, exact patch edits instead of full-file dumps, wide exploration offloaded to subagents, silent tool runs with one-line phase changes and a "Done." close. |

Hard constraints baked into the skill:

- Never lose track of the main goal while optimizing details.
- Never revert to verbose mode unless explicitly asked.
- Maintain core system integrity — optimize the workflow, don't break the agent.

## Install

The skill follows the [Agent Skills open standard](https://agentskills.io) (governed by the Linux Foundation's Agentic AI Foundation), so it works with any agent that implements it.

### One-liner (any agent)

```bash
npx skills add Wennington123/punisher-mode
```

Installs into whichever agents you have, with an interactive prompt to pick them.

### Manual — known skill locations

- **Command Code**: `~/.commandcode/skills/` (personal) · `.commandcode/skills/` (project)
- **Claude Code**: `~/.claude/skills/` (personal) · `.claude/skills/` (project)
- **OpenAI Codex CLI**: `~/.codex/skills/`
- **Cursor**: `~/.cursor/skills/`
- **OpenCode**: `.opencode/skills/`
- **Shared convention** (read by Gemini CLI and other spec-compliant agents): `~/.agents/skills/` (personal) · `.agents/skills/` (project)

Clone into the target agent's skills directory:

```bash
git clone https://github.com/Wennington123/punisher-mode <skills-dir>/punisher-mode
```

Verify it registered: `cmd skills list` / `/skills` (Command Code), `/skills` (Claude Code), or ask the agent to list its skills.

## Usage

```
/punisher-mode
```

- **Opt-in only, on every agent.** In Command Code, the frontmatter flag `disable-model-invocation: true` guarantees the model never sees the skill — only `/punisher-mode` runs it. Other agents ignore that flag, so the skill body carries a portable invocation guard instead: if the skill is loaded without an explicit user activation, none of its directives apply.
- The mode stays active for the rest of the session.
- End it anytime: say "back to normal mode" (or start a new session).

## How it works

Skills use a three-tier progressive disclosure model:

1. **Metadata** — `name` + `description`, always visible to the loader.
2. **Body** — `SKILL.md` loads on activation; this is the full directive set.
3. **Supporting files** — loaded on demand (none needed here; the whole skill is one file).

The `disable-model-invocation: true` flag is a Command Code extension (ignored safely by other tools): the skill stays listed in `cmd skills list` and the slash menu, but is withheld from the model's auto-invocation context.

## Safety notes

This skill lowers *friction*, not *safety*. Hard constraints are codified in the skill body ("Safety Constraints") and override the autonomy directives:

- `rm -rf /` and `rm -rf ~` — explicit user confirmation required, always.
- Environment variables containing `KEY`/`SECRET`/`TOKEN`/`PASSWORD` are never echoed unless explicitly requested.
- Piping large data blocks (`tar`, `zip`) to external `curl`/`wget` — destination URL stated first.
- `sudo` is used sparingly, with an explanation of why it's needed.
- `kill -9` on system-critical processes (`systemd`, `sshd`, `docker`) — warning first.

The full [dangerous command catalog](./references/dangerous-commands.md) — self-destructors, API key leakers, data siphoners, and chain-reaction killers, with the reasoning behind each — ships with the skill. Core agent security policies always take precedence over skill instructions.

## Requirements

- Command Code with skill support, or any agent implementing the [Agent Skills standard](https://agentskills.io/specification).

## Contributing

PRs welcome. Rules that must hold for any change:

- Directory name must equal `name:` in the frontmatter (`a-z`, `0-9`, hyphens only).
- Keep `SKILL.md` well under 500 lines — this skill is intentionally one small file.
- Don't remove the `disable-model-invocation: true` flag without a discussion; it is the security property of this skill.

## License

[MIT](./LICENSE) © Wennington Dias Aquino
