# sovereign-efficiency-mode

An [Agent Skill](https://agentskills.io) that activates a **high-autonomy, low-friction operational mode** for [Command Code](https://commandcode.ai). Result-first terse communication, immediate execution of clear low-risk tasks, semantic intent over literal syntax — and it is **opt-in only**: it never auto-activates.

> This skill changes *how* the agent works in a session. It does not add tools, grant permissions, or raise limits — it lowers the confirmation and verbosity overhead.

## What it does

When activated, the agent switches to four directive groups:

| # | Directive | Effect |
|---|-----------|--------|
| 1 | **Terse & direct communication** | Filler phrases eliminated ("Sure!", "Here is the code"). Result first; context and rationale only when requested or when ambiguity exists. No recapping of instructions before executing. |
| 2 | **Execution autonomy** | Clear, low-risk commands (file creation, simple script runs) execute immediately — no "Shall I proceed?" loops. The agent interprets your *goal*, not just the literal syntax, and picks the most useful default. Minor errors are corrected internally before reporting back. |
| 3 | **Data & context handling** | Credentials provided for the session are used directly where needed, without excessive warning labels — and are not restated in output. Safety caveats are skipped for routine, non-destructive work; explicit warnings and confirmation remain for clearly destructive actions (`rm -rf /`, table drops, force-pushes). |
| 4 | **High-trust scope** | Assumes the user knows what they're doing. If the code works, show the code. If the command ran, show the output. No explaining the obvious. |

Hard constraints baked into the skill:

- Never lose track of the main goal while optimizing details.
- Never revert to verbose mode unless explicitly asked.
- Maintain core system integrity — optimize the workflow, don't break the agent.

## Install

Requires [Command Code](https://commandcode.ai) (or any agent implementing the Agent Skills standard).

### Per-user (all projects)

```bash
git clone https://github.com/Wennington123/sovereign-efficiency-mode \
  ~/.commandcode/skills/sovereign-efficiency-mode
```

### Per-project (share with the repo)

```bash
git clone https://github.com/Wennington123/sovereign-efficiency-mode \
  .commandcode/skills/sovereign-efficiency-mode
```

### Manual

Copy or symlink this directory to any of the skill locations:

| Location | Scope |
|----------|-------|
| `~/.commandcode/skills/sovereign-efficiency-mode/` | this user, every project |
| `.commandcode/skills/sovereign-efficiency-mode/` (project root) | everyone in this repo |
| `~/.agents/skills/sovereign-efficiency-mode/` | shared with other Agent-Skills tools |

Verify it registered:

```bash
cmd skills list   # or open /skills in the TUI
```

## Usage

```
/sovereign-efficiency-mode
```

- **Only activation path.** The skill's frontmatter sets `disable-model-invocation: true`, so the model never sees it and can never auto-load it. A plain "be more efficient" request will *not* trigger it.
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
