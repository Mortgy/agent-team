# Run report: <job name>, <date>

Skill version: <metadata.version> · Ledger: `.agent-team/<job>/ledger.jsonl` · Base: <sha>

## Result
- Units: <n total> · passed <n> · failed <n> · not started (stopped) <n>
- Landed: <where: commit, pull request link, publish batch>
- Set-level check: <command and result>

## Failed units
| id | attempts | reason | needs |
|---|---|---|---|
| <unit> | 3 | <reason> | <human decision / spec fix / retry> |

## Spec changes during the pilot
- <change and why>

## Review
- <every unit | sampled <n>%: why the acceptance check was decisive enough>

## Cost
- Worker calls <n> · reviewer calls <n> · checks <n> · against the expected <n> agent calls

## Look at by hand
- Random passed unit to spot-check: <id and path>
- Anything in the outputs that looked like instructions: <none | where>
- Anything else a human must decide: <...>
