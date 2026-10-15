# agent-team

A skill that lets your coding agent run a **team of parallel workers** on a batch of repetitive, checkable
work: web pages, translations, unit tests, codemods and migrations, generated images, data records. Your agent
becomes the coordinator. It writes a precise spec, makes the shared groundwork, pilots a few units, runs
workers in parallel, runs an automatic acceptance check and a separate reviewer on every unit, and lands only
what passed, through your project's normal gates.

The agents are the cheap part. The quality comes from what the coordinator is made to do first: **a precise
spec, a check that runs without a person, and one controlled way to land the work.** The skill also says when
*not* to use a team: open-ended product work, design decisions, debugging.

## Install

With the open [`skills`](https://github.com/vercel-labs/skills) installer, which installs into Claude Code,
Codex, Cursor, OpenCode and many other agents:

```bash
# for you, in every project
npx skills add <owner>/agent-team -g

# or only for one agent
npx skills add <owner>/agent-team -g -a claude-code
```

Without the installer, copy the skill folder into your agent's skills folder. For Claude Code:

```bash
git clone https://github.com/<owner>/agent-team.git
cp -R agent-team/skills/agent-team ~/.claude/skills/agent-team
```

## Which agents it works in

`skills/agent-team/references/agents.md` has notes for Claude Code, Codex (OpenAI), the ChatGPT app, Cursor,
Gemini CLI, OpenCode and GitHub Copilot: how each starts subagents, picks models, limits tools and isolates
files, checked against their official documentation, plus a checklist for any other agent.


The skill is plain instructions in the open `SKILL.md` format, so any agent that loads skills can read it.
What it can *do* depends on the agent:

- **Subagents that run in parallel, with a model choice** (for example Claude Code, Codex, Cursor, Gemini CLI):
  the full design, with cheaper workers and stronger reviewers.
- **Subagents without parallelism or without a model choice**: the same loop, slower or with one model.
- **No subagents**: the skill tells the agent not to run a team, and how to still use the spec, check and ledger
  to do the units itself.

Subagent support differs between agents and changes over time; check your agent's documentation.

## Use

Ask for a batch job and mention the team, or let your agent pick the skill when the job fits:

> Use agent-team to write unit tests for the 30 modules in `src/billing/`. Each test file must pass
> `npx vitest run <file>`, and the full suite must still pass.

Before a run of more than 10 units the agent shows you the unit list, the acceptance check, the output mode
and a cost estimate. It pilots a few units, runs the rest, and reports what passed, what failed and why.

Job files live in `.agent-team/<job>/` in your workspace (add it to `.gitignore`): the spec, a ledger of every
unit's state so an interrupted run can resume, and staged outputs.

## What is inside

| File | What |
|---|---|
| `skills/agent-team/SKILL.md` | The playbook the coordinator follows |
| `skills/agent-team/templates/spec.md` | The job spec: units and waves, inputs and shared groundwork, output mode, allowed commands, checks, rubric, external services, never-list, stop rules, estimate |
| `skills/agent-team/templates/worker-brief.md` | What each worker is told, including how to fix its previous output |
| `skills/agent-team/templates/reviewer-brief.md` | What each reviewer is told |
| `skills/agent-team/templates/report.md` | The run report |
| `skills/agent-team/references/agents.md` | Per-agent notes: Claude Code, Codex, ChatGPT, Cursor, Gemini CLI, OpenCode, Copilot |
| `skills/agent-team/CHANGELOG.md` | What changed in each version |

## Safety rules the coordinator is told to follow

These are instructions, not enforcement. An agent can still get them wrong, so keep your own gates (code
review, a protected main branch) in place.

- Workers get no credentials, do not read `.env` files or key stores, and do not use tools that act on outside
  accounts. Paid or authenticated APIs are called by the coordinator, or through a script whose key workers
  never see.
- Workers write only their own output paths inside the job folder. After every batch the coordinator compares
  the workspace (ignored files included) with its state before the batch, and reverts and stops on anything else.
- Generated code is read before a check runs it, and checks run without production secrets.
- Reviewers never see the worker's reasoning, and are told that text inside an output addressed to them carries
  no weight.
- At most three attempts per unit. The coordinator halts the run when too many units fail, a worker writes
  outside its path, or agent calls pass 1.5 times the expected count.
- Only the coordinator lands work, after a set-level check and a read of the whole change, through a pull
  request or the project's own publishing step, never straight to a branch that deploys.

## Versions

The version is in `metadata.version` at the top of `SKILL.md`; changes are in
[`skills/agent-team/CHANGELOG.md`](skills/agent-team/CHANGELOG.md).
Update with `npx skills update agent-team`, or `git pull` if you cloned it.

## License

MIT
