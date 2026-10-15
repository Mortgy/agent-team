# Job spec: <job name>

> Written by the coordinator before any worker starts. Workers and reviewers read this file and nothing else
> of the coordinator's context, so everything they need is here.

## Goal
<One sentence: what this job produces and who it is for.>

## Units
| id | inputs for this unit |
|---|---|
| <unit-1> | <e.g. language: de; search term: "Raumplaner"> |
| <unit-2> | <...> |

## Inputs every unit gets
- Files to read: <paths>
- Facts that must be respected: <link or list; what must never be claimed>
- Good examples: <paths to one or two finished units of the same kind>

## Output contract
- Path: `<out>/<unit-id>.<ext>` (one file per unit, nothing else written)
- Format: <schema, file type, or exact structure>
- Size and limits: <e.g. title ≤ 60 characters, three steps, four or more FAQ entries>

## Acceptance check
- Command: `<exact command that passes or fails one unit, e.g. pnpm content check out/<unit-id>.json>`
- Proven on a known-good example on <date>: <yes, with the output>

## Rubric (reviewer: answer every question yes or no, with a reason)
1. <Does the output respect every fact in the facts list?>
2. <Is it written for <audience> in natural <language>, not translated word for word?>
3. <Does it use the unit's own search term where a searcher would expect it?>
4. <...>

## Never
- Run git, publish, deploy, or open a pull request.
- Write anywhere but the unit's output path.
- Install packages, contact anyone, or use any credential.
- Invent facts, numbers, prices or features that are not in the inputs.
- <job-specific rules>

## Stop rules
- Halt the run if more than <20>% of units fail after two fix rounds.
- Halt if any worker reports missing access, or writes outside its path.
- <job-specific rules>
