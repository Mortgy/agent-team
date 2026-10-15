# Changelog

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
