# Run ledger

Repos: R1 a2dplaya, R2 AudioBurgerSonnet, R3 AudioRouter, R4 Aurilis, R5 awdeo, R6 SonicFlow. OUT = /home/user/audio.

Result values: DONE (file written with tally), ACCEPTED, FAIL n / MISSING n (verifier totals), INCOMPLETE, DISPUTED, BLOCKED, RUNNING.

| # | Phase | Repo | Role | File | Result | Rerun count |
|---|-------|------|------|------|--------|-------------|
| 1 | 0 | all | Orchestrator preflight | 00-manifest.md, _harness/rules.md, _harness/stages.md, _harness/templates.md | DONE. Configuration complete, OUT empty before run, six repos snapshotted, harness read back (12 rules; 12 stages, 48 stage questions, 8 history questions; 2 templates, 1 illustration), web search and fetch tools available | 0 |
| 2 | 1 | R1 | Mapper | repos/R1/01-map.md | RUNNING | 0 |
| 3 | 1 | R2 | Mapper | repos/R2/01-map.md | RUNNING | 0 |
| 4 | 1 | R3 | Mapper | repos/R3/01-map.md | RUNNING | 0 |
| 5 | 1 | R4 | Mapper | repos/R4/01-map.md | RUNNING | 0 |
| 6 | 1 | R5 | Mapper | repos/R5/01-map.md | RUNNING | 0 |
| 7 | 1 | R6 | Mapper | repos/R6/01-map.md | RUNNING | 0 |
| 8 | 2 | n/a | Interface researcher | interfaces/01-products.md, interfaces/02-patterns.md | RUNNING | 0 |
