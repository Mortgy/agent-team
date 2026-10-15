# Agent notes

How the capabilities in `SKILL.md` §0 map onto specific agents. Read only the section for the agent you are
running in. Features change often: these notes were checked against each agent's official documentation on
**2026-10-15**, and the links are there so you can check again. If your agent is not listed, or a note no
longer matches what you can do, go by the capabilities table in `SKILL.md`, not by this file.

Every agent below loads skills from a folder containing `SKILL.md`, so the playbook itself is the same
everywhere. What differs is how you start workers and reviewers, pick their models, limit their tools and
isolate their files.

---

## Claude Code (Anthropic)

- **Start workers and reviewers** with the **Agent** tool, one call per worker. Several Agent calls in one
  message run in parallel.
- **Model per call**: the Agent tool takes `model`, for example `"sonnet"` for workers, `"haiku"` for very
  simple mechanical units, `"opus"` for reviewers.
- **Isolation**: `isolation: "worktree"` gives a worker its own git worktree, checked out from the last commit:
  it has no ignored or uncommitted files (no job folder, no `node_modules`). Follow `SKILL.md` §2 step 6, and copy
  outputs out before the worktree is cleaned up (an unchanged worktree is removed automatically).
- **Tool limits**: define worker and reviewer types as custom subagents in `.claude/agents/<name>.md` with a
  `tools:` list (the reviewer: read and search only), and start them with the Agent tool's `subagent_type`.
  Without that, workers inherit every tool and connector the session has.
- **Workflows**: where the Workflow tool is available and the user has explicitly asked for a multi-agent
  workflow, the worker, check and reviewer steps may run as a Workflow script instead of separate Agent calls.
  Do not start one otherwise.
- Skills folder: `~/.claude/skills/` (personal) or `.claude/skills/` (project).

## Codex (OpenAI): CLI, IDE extension and app

Docs: [Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents) ·
[Skills](https://developers.openai.com/codex/skills)

- **Start workers**: ask for them in plain words ("spawn 5 agents, one per unit, in parallel"). Codex also
  delegates when a skill's instructions ask it to, which this skill does. `/subagents` shows running threads.
- **Parallel limit**: `agents.max_concurrent_threads_per_session` in the configuration caps concurrent
  subagents. Keep each batch at or under it.
- **Model per worker**: name the model or reasoning effort when you spawn, or set
  `agents.default_subagent_model`. For a fixed reviewer, define a custom agent with its own `model`.
- **Custom agents**: one TOML file each in `~/.codex/agents/` or `.codex/agents/`, with `name`, `description`
  and `developer_instructions` (and optionally `model`, `model_reasoning_effort`). A `reviewer` agent built from
  `templates/reviewer-brief.md` is a good fit.
- **Tool limits**: you can turn features off per custom agent (`features.shell_tool = false`, and `apps`,
  `plugins`, `memories`, `request_permissions_tool`). A custom agent **cannot** set its own sandbox: subagents
  inherit the parent's sandbox and approval settings, and instructions such as "do not edit" do not create a
  read-only sandbox. So rely on the changed-files check after every batch.
- Skills folder: `$HOME/.agents/skills/` (personal) or `.agents/skills/` (repository).

## ChatGPT (the chat app)

The skill needs files, commands and a workspace. In the plain chat app, without a connected workspace that can
read and write files and run commands, use Codex instead. ChatGPT's own skills load the same `SKILL.md` format,
but this playbook cannot run its checks or land work there.

## Cursor

Docs: [Subagents](https://cursor.com/docs/subagents) · [Agent Skills](https://cursor.com/docs/skills)

- **Start workers**: the agent sends several Task calls in one message and they run at the same time. You can
  also call a named subagent with `/name`.
- **Custom subagents**: Markdown files with YAML frontmatter in `.cursor/agents/` (project) or `~/.cursor/agents/`
  (personal); Cursor also reads `.claude/agents/` and `.codex/agents/`.
- **Model per subagent**: `model` in the frontmatter (defaults to `inherit`, the parent's model).
- **Read-only reviewer**: `readonly: true` stops a subagent from editing files or running state-changing
  commands. Use it for the reviewer.
- **Tool limits**: subagents inherit all of the parent's tools, including MCP tools; there is no per-subagent
  tool list. Keep the Never list strict and rely on the changed-files check.
- **Isolation**: by default subagents share the parent's checkout. On request each can get its own git worktree
  or a cloud machine, which suits output mode B.
- Skills folder: `.cursor/skills/` or `.agents/skills/`; Cursor also reads `.claude/skills/` and `.codex/skills/`.

## Gemini CLI (Google)

Docs: [Subagents announcement](https://developers.googleblog.com/subagents-have-arrived-in-gemini-cli/) ·
[Remote subagents](https://geminicli.com/docs/core/remote-agents/)

- **Start workers**: ask in plain words to run a subagent over several items in parallel, or call one with
  `@name`. `/agents` lists them. Parallel subagents are supported; Google warns that several agents editing
  code at once can conflict, which is why this skill gives each unit its own output path.
- **Custom subagents**: Markdown with YAML frontmatter in `.gemini/agents/` (project) or `~/.gemini/agents/`
  (personal), each with its own instructions, `tools:` list and MCP servers.
- **Model**: the frontmatter has a `model` field (`inherit` uses the parent's).
- **Tool limits**: the `tools:` list. Give the reviewer read and search tools only.
- Skills folder: `.gemini/skills/` or `.agents/skills/` (workspace), `~/.gemini/skills/` or `~/.agents/skills/`
  (user).
- Google's product line here has been changing; if you are on a successor to Gemini CLI, check its docs.

## OpenCode

- **Start workers**: primary agents launch subagents with the Task tool, each in its own child session; you can
  also call one with `@name`.
- **Custom subagents**: Markdown files in `.opencode/agents/` (project) or `~/.config/opencode/agents/`
  (global), with `mode: subagent` and per-agent tool and permission settings (for example, edit and write turned
  off for a reviewer).
- These details come from OpenCode's agent docs and community guides; check the current
  [OpenCode docs](https://opencode.ai/docs) for exact field names.

## GitHub Copilot (coding agent, CLI, VS Code agent mode)

Docs: [Customization cheat sheet](https://docs.github.com/enterprise-cloud@latest/copilot/reference/customization-cheat-sheet)

- Copilot can spawn subagents at run time, in an isolated context, but they are not files you configure.
  Custom agents (a persona and tool set) are defined separately.
- Skills folder: `.github/skills/` or `.claude/skills/` (project), `~/.copilot/skills/` (personal).
- Parallelism and per-subagent model choice vary by surface: if you cannot confirm them, follow the "one at a
  time" or "no model choice" rows of the capabilities table.

## Any other agent

Map it onto the table in `SKILL.md` §0:

1. Can it start a separate agent with its own context? If not, use the "no subagents" row.
2. Can several run at once? If not, run sequentially.
3. Can you choose a model per agent? If not, one model, stricter rubric.
4. Can you limit a subagent's tools or make it read-only? If not, rely on the changed-files check.
5. Can a subagent get its own worktree or sandbox? If not, use staged outputs (mode B) and apply them yourself.
