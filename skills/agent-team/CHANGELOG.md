# Changelog

## 0.3.0 — 2026-10-15

Revised after a second independent review, and per-agent notes added.

- **Agent notes** in `references/agents.md`: Claude Code, Codex (OpenAI), the ChatGPT app, Cursor, Gemini CLI,
  OpenCode and GitHub Copilot, each checked against its official documentation on 2026-10-15, with links; and a
  checklist for any other agent. `SKILL.md` no longer carries Claude Code specifics.
- **Isolated workspaces**: workers get absolute paths to the job folder; groundwork and dependencies are copied
  into a fresh worktree; outputs are copied out before cleanup. In 0.2.0 a worktree worker could not see the spec.
- **Changed-files check** now includes ignored paths and a hash of the job folder; an unattributable change re-runs
  the batch one unit at a time.
- **All outputs are staged in the job folder** in every mode, including mode A; nothing reaches the live tree
  before landing, and a halted run leaves nothing behind.
- **Ledger** has explicit states and allowed moves (`fixing` added, `reviewed` removed), and resume rules per state
  that keep the attempt count and the previous output.
- **Fit test**: if a deterministic tool can do the job, use it and give agents only the exceptions.
- **"No subagents"** no longer contradicts the unit threshold: do not run a team; the spec, check and ledger may
  still be used.
- **Budget stop** measured in agent calls against the expected count, not "spending".
- **Generated code**: every unit is searched, the pilot, a sample and flagged units are read in full; workers that
  run commands follow the same no-secrets rule.
- **Codemods** use a per-batch or set-level check; existing per-language files are mode B; a pre-approved script is
  the default for external services at scale; binary files in a pull request need a size check.
- Reviewer judges only Never-list breaches visible in the output.
- This changelog moved into the skill folder, so installed copies include it.

## 0.2.0 — 2026-10-15

Revised after an independent review against the skill's own scope.

- **Output modes**: A (new files), B (staged edit or patch, for codemods and migrations), C (fragment and
  merge, for translation catalogues and data files). Version 0.1.0 could not handle units that edit existing
  files or share one file.
- **Check workspace** (in place, per-unit copy, per batch) and a separate **set-level check** at landing.
- **Capabilities table**: what to do with or without parallel subagents, a model choice, tool restriction or
  isolated workspaces. Claude Code specifics moved to one section.
- **Shared groundwork** made by the coordinator before the run, and **waves** for units that depend on others.
- **Run ledger** (`ledger.jsonl`) for resume, idempotence and keeping the coordinator's context small.
- **Safety**: no credentials or outside-account tools for workers; a changed-files check after every batch;
  generated code read before a check runs it; checks without production secrets; external services only
  through the coordinator or a pre-approved script; the whole change read before landing; never land straight
  to a deploying branch.
- **Fix loop** made exact: a new worker reads and corrects the previous output; at most three attempts in total;
  every fix is checked and reviewed again by a new reviewer. The check now runs before the reviewer.
- **Cost**: unit size guidance, a budget stop at 1.5× the estimate, a larger pilot for big runs, optional
  sampled review when the check is decisive.
- Binary outputs (images, audio) and allowed commands for workers.
- README no longer overclaims: safety rules are instructions, not enforcement; subagent support varies by agent.

## 0.1.0 — 2026-10-15

- First version: spec, pilot, parallel workers, separate reviewer, acceptance check, coordinator-only landing,
  report.
