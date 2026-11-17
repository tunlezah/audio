# R2 verification: AudioBurgerSonnet

Repo: /home/user/AudioBurgerSonnet (R2), read at commit 1d878c2 (`git rev-parse HEAD` = 1d878c2e1b15c4cdb2baf342c30fbb247d491737). Repo paths below are relative to that root.
Role: Verifier. Files checked: /home/user/audio/repos/R2/02-stages-bluetooth.md, 03-stages-audio.md and 04-history.md.
Records checked: 171. Finding records: 163 (56 in file 02, 63 in file 03, 44 in file 04). NOT FOUND records: 8 (3 in file 02, 3 in file 03, 2 in file 04). These counts equal the tallies at the end of each file (02-stages-bluetooth.md:682, 03-stages-audio.md:773, 04-history.md:704).
This file reports. It changes no record and adds no finding.

## How the checks were run

- Check A. Every `path:line` and `HASH:path:line` citation in the three files was compared with the cited file. Citations of `.md` files and of older versions (`git show HASH:path`) were printed line by line beside each record. Citations of code and configuration files were checked against full reads of those files. These files were read in full: `audioburger/bluetooth/a2dp_sink.py`, `audioburger/audio_plane/audio_pipeline.py`, `audioburger/chromecast/chromecast_output.py`, `audioburger/web_ui/api_server.py`, `audioburger/main.py`, `audioburger/config/config_manager.py`, `audioburger/logging/logger.py`, `install.sh`, `config.toml`, `requirements.txt`, `setup.py`, `audioburger.service`, `.gitignore`, and the tracked frontend files other than the lockfile. Quotations from commit messages were compared with `git show -s --format=%B` for 70fc363, eb02d59, add5474, b61e1ec, 70b5a5a, deb899b, d51fcef, 1491214, cec17a7, e181276, d72817b and 6445123. Search statements in the records ("no hit", "one hit", "definition only") were re-run with `git grep`. Counts, dates and intervals in R2-H1-01 and R2-H8-01 were recomputed from `git log --all --date=iso`, `git rev-list --count`, `git shortlog -sn --all`, `git show --cc` on the 15 merges and `git log --all --name-only`. Citations of /home/user/audio/00-manifest.md and /home/user/audio/repos/R2/01-map.md were opened at the cited lines.
- A record is marked FAIL A where a cited location is absent, or where a statement about what the code or a document contains is contradicted by the cited file. A loosely worded description of a search result is not marked where the conclusion drawn from the search was confirmed.
- Check B. Each OBSERVED, STATED and INFERRED label was compared with the kind of material cited (Rule 3), and each INFERRED label was checked for a sentence of reasoning.
- Check C. Each WORKED, PARTIAL or FAILED outcome (27 records) was checked for OBSERVED or STATED evidence of a result (Rule 4). Each UNVERIFIED outcome (133 records) was checked against the evidence the record itself cites.
- Check D. The three files were searched, case-insensitive, for `should`, `recommend`, `suggest`, `consider`, `better`, `best`, `ideal`, `improv`, `fix by`, `instead`, `next step`, `prefer`, `superior`, `worse`, `unlike`, `other repo`, and for repo IDs R1 and R3 to R9. Every occurrence of a listed word lies inside quotation marks (02-stages-bluetooth.md:65, :87, :131, :212; 03-stages-audio.md:45, :108; 04-history.md:86, :135, :163, :174, :474, :482). No other repo is named.
- Check E. One search with terms different from the record's own was run for each NOT FOUND record. The terms and results are listed after the verdicts.
- Not graded here, because checks A to E do not cover them: the choice of Presence term, the Confidence level, and template form.

Departure from Rule 1, reported as a fact: during check E for R2-H4-01 one command outside the Rule 1 list was run in the repo, `git fsck --lost-found --no-reflogs`. It printed nothing. Afterwards no `.git/lost-found` directory exists, `git rev-parse HEAD` is unchanged, `git status --porcelain` is empty and `git count-objects -v` shows 0 loose objects and 134 packed. Its output is not used in any verdict.

## Verdicts

### Records in 02-stages-bluetooth.md

- R2-S01-01  PASS
- R2-S01-02  PASS
- R2-S01-03  PASS
- R2-S01-04  PASS
- R2-S01-05  PASS
- R2-S01-06  PASS
- R2-S01-07  PASS
- R2-S01-08  PASS
- R2-S01-09  PASS
- R2-S01-10  PASS
- R2-S01-11  PASS
- R2-S01-12  PASS
- R2-S02-01  PASS
- R2-S02-02  PASS
- R2-S02-03  PASS
- R2-S02-04  PASS
- R2-S02-05  PASS
- R2-S02-06  PASS
- R2-S02-07  PASS
- R2-S02-08  PASS
- R2-S03-01  PASS
- R2-S03-02  PASS
- R2-S03-03  PASS
- R2-S03-04  PASS
- R2-S03-05  PASS
- R2-S03-06  PASS
- R2-S03-07  PASS
- R2-S03-08  PASS
- R2-S03-09  PASS
- R2-S03-10  PASS
- R2-S03-11  PASS
- R2-S03-12  PASS
- R2-S03-13  PASS
- R2-S03-14  PASS
- R2-S03-15  PASS
- R2-S04-01  PASS
- R2-S04-02  PASS
- R2-S04-03  PASS
- R2-S04-04  PASS
- R2-S04-05  PASS
- R2-S04-06  PASS
- R2-S04-07  PASS
- R2-S04-08  PASS
- R2-S04-09  PASS
- R2-S04-10  PASS
- R2-S04-11  PASS
- R2-S05-01  PASS
- R2-S05-02  PASS
- R2-S05-03  PASS
- R2-S05-04  PASS
- R2-S05-05  PASS
- R2-S05-06  PASS
- R2-S05-07  PASS
- R2-S05-08  PASS
- R2-S05-09  PASS
- R2-S05-10  PASS
- R2-S06-01  PASS
- R2-S06-02  PASS
- R2-S06-03  PASS

### Records in 03-stages-audio.md

- R2-S07-01  PASS
- R2-S07-02  PASS
- R2-S07-03  PASS
- R2-S07-04  PASS
- R2-S07-05  PASS
- R2-S07-06  PASS
- R2-S07-07  PASS
- R2-S07-08  PASS
- R2-S07-09  PASS
- R2-S08-01  PASS
- R2-S08-02  PASS
- R2-S08-03  PASS
- R2-S08-04  PASS
- R2-S08-05  PASS
- R2-S08-06  PASS
- R2-S08-07  PASS
- R2-S08-08  PASS
- R2-S08-09  PASS
- R2-S08-10  PASS
- R2-S08-11  PASS
- R2-S08-12  PASS
- R2-S09-01  PASS
- R2-S09-02  PASS
- R2-S09-03  PASS
- R2-S09-04  PASS
- R2-S09-05  PASS
- R2-S09-06  PASS
- R2-S09-07  PASS
- R2-S09-08  PASS
- R2-S09-09  PASS
- R2-S09-10  PASS
- R2-S09-11  PASS
- R2-S09-12  PASS
- R2-S09-13  PASS
- R2-S09-14  PASS
- R2-S09-15  PASS
- R2-S10-01  PASS
- R2-S10-02  PASS
- R2-S10-03  PASS
- R2-S10-04  PASS
- R2-S10-05  PASS
- R2-S10-06  PASS
- R2-S10-07  PASS
- R2-S10-08  PASS
- R2-S10-09  PASS
- R2-S10-10  FAIL A: the record says `web_ui.static_path` is "validated"; the value is assigned from the file with no check (audioburger/config/config_manager.py:358) and `_validate_config` has no line for it (audioburger/config/config_manager.py:202-262). The other statements in the record were confirmed.
- R2-S10-11  FAIL A: the record says the seven settings are "parsed and validated"; `logging.systemd_integration` is assigned with no check (audioburger/config/config_manager.py:374) and `_validate_config` has no line for it (audioburger/config/config_manager.py:202-262). The no-reader statement was confirmed for all seven.
- R2-S10-12  PASS
- R2-S10-13  PASS
- R2-S10-14  PASS
- R2-S11-01  PASS
- R2-S11-02  PASS
- R2-S11-03  PASS
- R2-S11-04  PASS
- R2-S11-05  PASS
- R2-S11-06  PASS
- R2-S11-07  PASS
- R2-S11-08  PASS
- R2-S11-09  PASS
- R2-S12-01  PASS
- R2-S12-02  PASS
- R2-S12-03  PASS
- R2-S12-04  PASS
- R2-S12-05  PASS
- R2-S12-06  PASS
- R2-S12-07  PASS

### Records in 04-history.md

- R2-H1-01  PASS
- R2-H2-01  PASS
- R2-H2-02  FAIL A: the Presence field says "Four "Future implementation will" docstrings that name PipeWire remain" and cites audioburger/audio_plane/audio_pipeline.py:361-365 and :424-428; four such docstrings exist (audioburger/audio_plane/audio_pipeline.py:361, :392, :424, :445) and two of them name PipeWire (audioburger/audio_plane/audio_pipeline.py:362, :426). The other statements in the record were confirmed.
- R2-H2-03  PASS
- R2-H2-04  PASS
- R2-H2-05  PASS
- R2-H2-06  PASS
- R2-H2-07  PASS
- R2-H2-08  PASS
- R2-H2-09  PASS
- R2-H2-10  PASS
- R2-H2-11  PASS
- R2-H2-12  PASS
- R2-H2-13  PASS
- R2-H2-14  PASS
- R2-H2-15  PASS
- R2-H3-01  PASS
- R2-H4-01  PASS
- R2-H5-01  PASS
- R2-H5-02  PASS
- R2-H5-03  PASS
- R2-H5-04  PASS
- R2-H5-05  PASS
- R2-H5-06  PASS
- R2-H5-07  PASS
- R2-H5-08  PASS
- R2-H5-09  FAIL A: the record says the listed keys are "range-checked since b61e1ec"; `web_ui.static_path` and `logging.systemd_integration` have no check (audioburger/config/config_manager.py:358, :374; no line for either in audioburger/config/config_manager.py:202-262). The no-reader statement was confirmed for every key.
- R2-H5-10  PASS
- R2-H5-11  PASS
- R2-H5-12  PASS
- R2-H5-13  PASS
- R2-H5-14  PASS
- R2-H5-15  PASS
- R2-H5-16  PASS
- R2-H6-01  PASS
- R2-H7-01  PASS
- R2-H7-02  PASS
- R2-H7-03  PASS
- R2-H7-04  PASS
- R2-H7-05  PASS
- R2-H7-06  PASS
- R2-H7-07  PASS
- R2-H7-08  PASS
- R2-H7-09  PASS
- R2-H7-10  PASS
- R2-H8-01  PASS

## Check E searches

All searches were run over tracked files with `git grep -n -i -E`, excluding `audioburger/web_ui/frontend/package-lock.json`, unless another command is named.

- R2-S06-01 (S06.1). Terms: `remote control|play/pause|playerctl|mpris|keypress|key press|media key|RegisterPlayer|org\.bluez\.Media|rewind|fast.?forward|resume playback|a/v remote|passthrough`. Result: no hit.
- R2-S06-02 (S06.2). Terms: `absolute.?volume|AbsoluteVolume|SetVolume|set_volume|amixer|wpctl|pactl|attenuat|loudness|louder|quieter|volume_|soft.?vol|master vol`. Result: seven hits, none about volume control. Six are the word "loudness" in a survey of other software (audio-framework-research-report.md:258, :266, :280-281, :514-515); one is `pactl set-card-profile` in a note on profile switching (bluetooth-a2dp-sink-research.md:104).
- R2-S06-03 (S06.3). Terms: `now.?playing|nowplaying|\bsong\b|cover art|album art|id3|GetProperties|duration|position|playback state|currently playing|media info|media_info`. Result: no hit about track metadata or the source's playback status. "playback state" occurs twice and "position" once, all for the Chromecast side (architecture-and-build-plan.md:218, :1961; research-chromecast-output.md:349). The other hits are "duration" in schedule headings and sample log lines (for example architecture-and-build-plan.md:1768; logging-observability-report.md:151, :523), "positions" in a shell comment (install.sh:299) and the word "Composition" (research-web-ui-visualization.md:59, :66).
- R2-S11-04 (S11.2). Terms: `oneplus|xiaomi|huawei|nokia|motorola|\blg\b|airpods|macos|windows 1[01]|laptop|tablet|handset|smartphone|phone model|tested devices|compatib.* list|verified working|confirmed working|works with`. Result: no source device named as tested. Hits are plan items (architecture-and-build-plan.md:1637, :1753; audio-framework-research-report.md:477; bluetooth-a2dp-sink-research.md:361; research-chromecast-output.md:516), one device named inside the description of an outside bug report (bluetooth-a2dp-sink-research.md:294), and unrelated uses of "macOS", "smartphones", "Tablet" and "works with" (for example project-context.md:48; architecture-and-build-plan.md:1037; bluetooth-a2dp-sink-research.md:209).
- R2-S11-05 (S11.3). Terms: `ms measured|we measured|was measured|observed|recorded|results?:|result was|took [0-9]|[0-9]+ ?% (cpu|load)|rss|profil|htop|perf stat|timing|elapsed|throughput of|soak`. Result: no recorded measurement of this project. Hits are the word "profile"; the processor budget lines inside the range the record already cites (architecture-and-build-plan.md:708-712); plan items for soak tests and profiling (architecture-and-build-plan.md:1664, :1669, :1692, :1730, :2148, :2373, :2465; project-context.md:206; fixes.md:1543, an unticked item); a design check of the heartbeat interval against a 30-second watchdog (patch1.md:382); sample log lines (logging-observability-report.md:413); and a user report about other software (research-chromecast-output.md:422-425).
- R2-S12-07 (S12.3). Terms: `passphrase|\.pem|\.key\b|bearer|jwt|oauth|hmac|encrypt|keyring|link.?key|/var/lib/bluetooth|htpasswd|certificate|cookie|session id|signing`; also `git ls-files` and `git log --all --diff-filter=A --name-only` filtered for file names containing `auth`, `cred`, `key`, `pem`, `token`, `secret`, `env`. Result: no secret or key stored or read by the project, and no such file in any commit. Hits: a proposed key file path inside the range the record cites (fixes.md:971, :999-1000); an operator note (INSTALLER.md:642); a diagram label for the Cast protocol (architecture-and-build-plan.md:749); the host BlueZ directory named in a list of workarounds from outside reports (bluetooth-a2dp-sink-research.md:304); "session ID" in the logging design (logging-observability-report.md:339, :562, :701; architecture-and-build-plan.md:163, :1304; audioburger/logging/logger.py:48, :185); the word "encrypted" in a non-goal (project-context.md:50).
- R2-H3-01 (H3). Command: `git log --all -i -E --grep='undo|roll ?back|back ?out|again|still|hotfix|regress|broke|workaround|temporar|wrong|oops|typo|correct'`. Result: three commits match on unrelated words: deb899b ("against"), b61e1ec ("against", "rollback on error", "No performance regressions"), 70fc363 ("correctly"). None reverts or repeats a change.
- R2-H4-01 (H4). Commands: `ls .git/refs/heads .git/refs/remotes/origin`; a read of `.git/packed-refs` (absent); `ls .git/refs/stash .git/worktrees` (both absent); `git log --all --format='%h %D'`. Result: the only ref names are `main` and the session branch with their origin counterparts, all on 1d878c2. No stash ref, no worktree, no tag.

## Coverage

Each question is listed with the records whose Question field names it. History records that name a stage question as related are listed after "related".

- S01.1: R2-S01-01, R2-S01-02
- S01.2: R2-S01-03
- S01.3: R2-S01-04, R2-S01-05, R2-S01-06, R2-S01-07, R2-S01-08; related: R2-H2-04
- S01.4: R2-S01-09, R2-S01-10, R2-S01-11
- S01.5: R2-S01-12; related: R2-H5-13, R2-H7-09
- S02.1: R2-S02-01, R2-S02-02
- S02.2: R2-S02-03, R2-S02-04
- S02.3: R2-S02-05, R2-S02-06, R2-S02-07, R2-S02-08
- S03.1: R2-S03-01, R2-S03-02, R2-S03-03, R2-S03-04; related: R2-H5-05
- S03.2: R2-S03-05, R2-S03-06, R2-S03-07
- S03.3: R2-S03-08, R2-S03-09, R2-S03-10, R2-S03-11; related: R2-H5-02, R2-H5-06
- S03.4: R2-S03-12, R2-S03-13
- S03.5: R2-S03-14, R2-S03-15
- S04.1: R2-S04-01, R2-S04-02, R2-S04-03; related: R2-H2-01
- S04.2: R2-S04-04
- S04.3: R2-S04-05, R2-S04-06, R2-S04-07, R2-S04-08, R2-S04-09
- S04.4: R2-S04-10, R2-S04-11
- S05.1: R2-S05-01, R2-S05-02, R2-S05-03; related: R2-H2-02, R2-H5-01
- S05.2: R2-S05-04, R2-S05-05
- S05.3: R2-S05-06, R2-S05-07
- S05.4: R2-S05-08, R2-S05-09, R2-S05-10; related: R2-H5-07, R2-H7-01, R2-H7-02, R2-H7-04
- S06.1: R2-S06-01 (NOT FOUND)
- S06.2: R2-S06-02 (NOT FOUND)
- S06.3: R2-S06-03 (NOT FOUND)
- S07.1: R2-S07-01, R2-S07-02, R2-S07-03, R2-S07-04, R2-S07-05, R2-S07-06; related: R2-H2-05
- S07.2: R2-S07-07, R2-S07-08
- S07.3: R2-S07-09
- S08.1: R2-S08-01, R2-S08-02, R2-S08-03; related: R2-H5-03, R2-H5-08
- S08.2: R2-S08-04, R2-S08-05, R2-S08-06; related: R2-H2-03, R2-H2-13, R2-H2-14, R2-H5-04
- S08.3: R2-S08-07, R2-S08-08, R2-S08-09, R2-S08-10; related: R2-H7-03
- S08.4: R2-S08-11, R2-S08-12
- S09.1: R2-S09-01, R2-S09-02, R2-S09-03; related: R2-H5-16
- S09.2: R2-S09-04, R2-S09-05, R2-S09-06, R2-S09-07, R2-S09-08, R2-S09-09; related: R2-H2-06, R2-H2-07, R2-H5-11, R2-H5-12
- S09.3: R2-S09-10, R2-S09-11, R2-S09-12
- S09.4: R2-S09-13, R2-S09-14, R2-S09-15
- S10.1: R2-S10-01, R2-S10-02; related: R2-H7-07, R2-H7-08, R2-H7-10
- S10.2: R2-S10-03, R2-S10-04, R2-S10-05, R2-S10-06; related: R2-H2-15, R2-H7-05, R2-H7-06
- S10.3: R2-S10-07; related: R2-H5-14
- S10.4: R2-S10-08, R2-S10-09, R2-S10-10, R2-S10-11; related: R2-H2-09, R2-H5-09
- S10.5: R2-S10-12, R2-S10-13; related: R2-H5-15
- S10.6: R2-S10-14; related: R2-H2-08
- S11.1: R2-S11-01, R2-S11-02, R2-S11-03; related: R2-H5-10
- S11.2: R2-S11-04 (NOT FOUND)
- S11.3: R2-S11-05 (NOT FOUND)
- S11.4: R2-S11-06, R2-S11-07, R2-S11-08, R2-S11-09; related: R2-H2-10
- S12.1: R2-S12-01, R2-S12-02, R2-S12-03, R2-S12-04; related: R2-H2-12
- S12.2: R2-S12-05, R2-S12-06
- S12.3: R2-S12-07 (NOT FOUND)
- H1: R2-H1-01
- H2: R2-H2-01, R2-H2-02, R2-H2-03, R2-H2-04, R2-H2-05, R2-H2-06, R2-H2-07, R2-H2-08, R2-H2-09, R2-H2-10, R2-H2-11, R2-H2-12, R2-H2-13, R2-H2-14, R2-H2-15
- H3: R2-H3-01 (NOT FOUND)
- H4: R2-H4-01 (NOT FOUND)
- H5: R2-H5-01, R2-H5-02, R2-H5-03, R2-H5-04, R2-H5-05, R2-H5-06, R2-H5-07, R2-H5-08, R2-H5-09, R2-H5-10, R2-H5-11, R2-H5-12, R2-H5-13, R2-H5-14, R2-H5-15, R2-H5-16
- H6: R2-H6-01
- H7: R2-H7-01, R2-H7-02, R2-H7-03, R2-H7-04, R2-H7-05, R2-H7-06, R2-H7-07, R2-H7-08, R2-H7-09, R2-H7-10
- H8: R2-H8-01

Questions listed: 56 (48 stage questions and 8 history questions). Questions with at least one record: 56. Questions with no record: 0.

## Totals

- PASS: 167
- FAIL: 4 (R2-S10-10, R2-S10-11, R2-H2-02, R2-H5-09; all on check A)
- MISSING: 0
- Records checked: 171
