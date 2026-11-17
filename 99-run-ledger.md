# Run ledger

Repos: R1 a2dplaya, R2 AudioBurgerSonnet, R3 AudioRouter, R4 Aurilis, R5 awdeo, R6 SonicFlow. OUT = /home/user/audio.

Result values: DONE (file written with tally), ACCEPTED, FAIL n / MISSING n (verifier totals), INCOMPLETE, DISPUTED, BLOCKED, RUNNING.

| # | Phase | Repo | Role | File | Result | Rerun count |
|---|-------|------|------|------|--------|-------------|
| 1 | 0 | all | Orchestrator preflight | 00-manifest.md, _harness/rules.md, _harness/stages.md, _harness/templates.md | DONE. Configuration complete, OUT empty before run, six repos snapshotted, harness read back (12 rules; 12 stages, 48 stage questions, 8 history questions; 2 templates, 1 illustration), web search and fetch tools available | 0 |
| 2 | 1 | R1 | Mapper | repos/R1/01-map.md | DONE. Seven sections present, no BLOCKED lines | 0 |
| 3 | 1 | R2 | Mapper | repos/R2/01-map.md | DONE. Seven sections present, no BLOCKED lines | 0 |
| 4 | 1 | R3 | Mapper | repos/R3/01-map.md | DONE. Seven sections present, no BLOCKED lines | 0 |
| 5 | 1 | R4 | Mapper | repos/R4/01-map.md | DONE. Seven sections present, no BLOCKED lines | 0 |
| 6 | 1 | R5 | Mapper | repos/R5/01-map.md | DONE. Seven sections present, no BLOCKED lines | 0 |
| 7 | 1 | R6 | Mapper | repos/R6/01-map.md | DONE. Seven sections present, no BLOCKED lines | 0 |
| 8 | 2 | n/a | Interface researcher | interfaces/01-products.md, interfaces/02-patterns.md | RUNNING | 0 |
| 9 | 1 | R1 | Bluetooth stage auditor | repos/R1/02-stages-bluetooth.md | RUNNING | 0 |
| 10 | 1 | R1 | Audio stage auditor | repos/R1/03-stages-audio.md | RUNNING | 0 |
| 11 | 1 | R1 | History auditor | repos/R1/04-history.md | DONE. 23 finding records, 3 NOT FOUND records, tally 5 + 3 = 8, no BLOCKED lines. Agent disclosed running git fsck --lost-found --no-reflogs in the repo, a command outside the Rule 1 list; orchestrator check afterwards: HEAD 70ad071 unchanged, git status --porcelain empty, no .git/lost-found directory | 0 |
| 12 | 1 | R5 | Bluetooth stage auditor | repos/R5/02-stages-bluetooth.md | DONE. 18 finding records, 13 NOT FOUND records, tally 11 + 13 = 24, no BLOCKED lines | 0 |
| 13 | 1 | R5 | Audio stage auditor | repos/R5/03-stages-audio.md | RUNNING | 0 |
| 14 | 1 | R5 | History auditor | repos/R5/04-history.md | RUNNING | 0 |
| 15 | 1 | R4 | Bluetooth stage auditor | repos/R4/02-stages-bluetooth.md | RUNNING | 0 |
| 16 | 1 | R4 | Audio stage auditor | repos/R4/03-stages-audio.md | RUNNING | 0 |
| 17 | 1 | R4 | History auditor | repos/R4/04-history.md | RUNNING | 0 |
| 18 | 1 | R3 | Bluetooth stage auditor | repos/R3/02-stages-bluetooth.md | RUNNING | 0 |
| 19 | 1 | R3 | Audio stage auditor | repos/R3/03-stages-audio.md | RUNNING | 0 |
| 20 | 1 | R3 | History auditor | repos/R3/04-history.md | RUNNING | 0 |
| 21 | 1 | R6 | Bluetooth stage auditor | repos/R6/02-stages-bluetooth.md | RUNNING | 0 |
| 22 | 1 | R6 | Audio stage auditor | repos/R6/03-stages-audio.md | RUNNING | 0 |
| 23 | 1 | R6 | History auditor | repos/R6/04-history.md | RUNNING | 0 |
| 24 | 1 | R2 | Bluetooth stage auditor | repos/R2/02-stages-bluetooth.md | RUNNING | 0 |
| 25 | 1 | R2 | Audio stage auditor | repos/R2/03-stages-audio.md | RUNNING | 0 |
| 26 | 1 | R2 | History auditor | repos/R2/04-history.md | RUNNING | 0 |
