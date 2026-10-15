# agent-team

A skill that lets your coding agent run a **team of parallel workers** on a batch of repetitive, checkable
work: pages, translations, tests, migrations, assets, data entries. Your agent becomes the coordinator. It
writes a precise spec, pilots a few units, runs workers in parallel, has a separate reviewer check each
result, runs an automatic acceptance check, and lands only what passed.

The agents are the cheap part. The quality comes from three things the skill makes the coordinator write
first: **a precise spec, a check that runs without a person, and one controlled way to land the work.**
The skill also says when *not* to use a team: open-ended product work, design decisions, debugging.

Works with any agent that reads the open `SKILL.md` format and can start subagents. It was written for
Claude Code (the Agent tool, with Sonnet workers and a stronger reviewer).

## Install

With the open [`skills`](https://github.com/vercel-labs/skills) installer (Claude Code, Codex, Cursor,
OpenCode and many more):

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

Replace `<owner>` with the GitHub account that hosts this repository.

## Use

Ask your agent for a batch job and mention the team, or let it pick the skill when the job fits:

> Use agent-team to write the German, French and Spanish versions of these 12 help articles. Each must pass
> `npm run lint:docs`.

The agent will show you the unit list, the acceptance check and a cost estimate before a large run, pilot
one to three units, then run the rest and report what passed, what failed and why.

## What is inside

| File | What |
|---|---|
| `skills/agent-team/SKILL.md` | The playbook the coordinator follows |
| `skills/agent-team/templates/spec.md` | The job spec: units, inputs, output contract, acceptance check, rubric, never-list, stop rules |
| `skills/agent-team/templates/worker-brief.md` | What each worker is told |
| `skills/agent-team/templates/reviewer-brief.md` | What each reviewer is told (it sees the output, not the worker's reasoning) |
| `skills/agent-team/templates/report.md` | The run report |

## Safety rules built in

- Workers never run git, publish, deploy, install packages, use credentials, or write outside their own
  output path. Only the coordinator lands work, through the project's normal gates.
- Reviewers see the output only, so a confident worker cannot talk them into a pass.
- At most two fix rounds per unit; then it is reported, not retried forever.
- The run stops by itself when too many units fail or a worker reports missing access.
- Everything workers return is treated as data, not instructions.

## License

MIT
