# R5 verification of files 02, 03 and 04

- Repo: /home/user/awdeo, repo ID R5. HEAD e556801cdd966f7bb86b8e7d2400718d38e8866c at the start and at the end of this check (git rev-parse HEAD). `git status --porcelain` printed zero lines at the start and at the end.
- Files checked: /home/user/audio/repos/R5/02-stages-bluetooth.md (31 records), /home/user/audio/repos/R5/03-stages-audio.md (56 records), /home/user/audio/repos/R5/04-history.md (35 records, plus the H1 and H8 lists).
- Records checked: 104 finding records and 18 NOT FOUND records, 122 in all. No record ID is used twice.
- Role: verifier. No record was changed. No finding was added.

## Method

- Every tracked file except package-lock.json and LICENSE was read in full at HEAD. package-lock.json was read at each cited line range. All 4 commit messages were read in full (git log --all --format=%B).
- Check A: every cited path:line, commit quotation and manifest line was opened. Files at older commits were read with `git show REF:PATH`. Every search or git command that a record cites as its basis was run again and its result compared with the record: `git grep` in the working tree and at commits e8801c3, a744781, 2db4cb5 and e556801, `git log --all` with `--stat`, `--name-status`, `--name-only`, `--grep`, `-G`, `-g`, `-L`, `git show`, `git branch -a`, `git tag`, `git stash list`, `git ls-files`, `git shortlog`, and the two object counts cited in R5-H4-01 (`git count-objects -v`, `git rev-list --all --objects`). Nothing was checked out, built or run.
- Check B: each Outcome evidence grade and each cause grade was compared with Rule 3. A script confirmed that every finding record has all nine template fields in template order and every NOT FOUND record has all three.
- Check C: each outcome was compared with its evidence under Rule 4. A script confirmed the pairing for all 104 finding records: UNVERIFIED with NONE in 95 records, FAILED with STATED in 7, WORKED with STATED in 2. No record uses PARTIAL or OBSERVED.
- Check D: the three files were searched for the words listed in Rule 6 and for the names and IDs of the other repos. The Rule 6 words occur 4 times, each inside a quotation from the repo (02-stages-bluetooth.md:99; 04-history.md:93, 198, 475). No other repo is named or compared.
- Check E: one search with terms the record did not use was run for each NOT FOUND record. The terms and results are in the section "Check E searches".

Verdict standard used for check A: FAIL where a cited location is absent, or where the location does not say what the record claims about it. A wording slip that leaves the stated fact true at one of the locations cited together is listed under "Notes" and does not change the verdict.

## Verdicts: file 02 (Bluetooth stages S01 to S06)

- R5-S01-01 PASS
- R5-S01-02 PASS
- R5-S01-03 PASS
- R5-S01-04 PASS
- R5-S01-05 PASS
- R5-S01-06 PASS
- R5-S01-07 PASS
- R5-S01-08 PASS
- R5-S01-09 PASS
- R5-S02-01 PASS
- R5-S02-02 PASS
- R5-S02-03 PASS
- R5-S03-01 PASS
- R5-S03-02 PASS
- R5-S03-03 PASS
- R5-S03-04 PASS
- R5-S03-05 PASS
- R5-S04-01 PASS
- R5-S04-02 PASS
- R5-S04-03 PASS
- R5-S04-04 PASS
- R5-S05-01 PASS
- R5-S05-02 PASS
- R5-S05-03 PASS
- R5-S05-04 PASS
- R5-S05-05 PASS
- R5-S05-06 PASS
- R5-S05-07 PASS
- R5-S06-01 PASS
- R5-S06-02 PASS
- R5-S06-03 PASS

## Verdicts: file 03 (Audio stages S07 to S12)

- R5-S07-01 PASS
- R5-S07-02 PASS
- R5-S07-03 PASS
- R5-S07-04 PASS
- R5-S07-05 PASS
- R5-S07-06 PASS
- R5-S08-01 PASS
- R5-S08-02 PASS
- R5-S08-03 PASS
- R5-S08-04 PASS
- R5-S08-05 PASS
- R5-S08-06 PASS
- R5-S08-07 PASS
- R5-S08-08 PASS
- R5-S08-09 PASS
- R5-S08-10 PASS
- R5-S08-11 PASS
- R5-S08-12 PASS
- R5-S08-13 PASS
- R5-S08-14 PASS
- R5-S09-01 PASS
- R5-S09-02 PASS
- R5-S09-03 PASS
- R5-S09-04 PASS
- R5-S09-05 PASS
- R5-S09-06 PASS
- R5-S09-07 PASS
- R5-S09-08 PASS
- R5-S09-09 PASS
- R5-S09-10 PASS
- R5-S10-01 PASS
- R5-S10-02 PASS
- R5-S10-03 PASS
- R5-S10-04 PASS
- R5-S10-05 PASS
- R5-S10-06 PASS
- R5-S10-07 PASS
- R5-S10-08 PASS
- R5-S10-09 PASS
- R5-S10-10 PASS
- R5-S10-11 PASS
- R5-S11-01 PASS
- R5-S11-02 PASS
- R5-S11-03 PASS
- R5-S11-04 PASS
- R5-S11-05 PASS
- R5-S11-06 PASS
- R5-S11-07 PASS
- R5-S11-08 PASS
- R5-S12-01 PASS
- R5-S12-02 PASS
- R5-S12-03 PASS
- R5-S12-04 PASS
- R5-S12-05 PASS
- R5-S12-06 PASS
- R5-S12-07 PASS

## Verdicts: file 04 (History H1 to H8)

- R5-H2-01 PASS
- R5-H2-02 PASS
- R5-H2-03 PASS
- R5-H2-04 PASS
- R5-H2-05 PASS
- R5-H2-06 PASS
- R5-H3-01 FAIL A: the record says each of the three commits names a review as its source; the a744781 subject it quotes ("security and reliability vulnerabilities") names no review or analysis, and neither does the a744781 body. Only 2db4cb5 ("comprehensive analysis") and e556801 ("comprehensive reliability engineering analysis") do. The "Depends on" and "Open questions" fields repeat the count of three.
- R5-H3-02 PASS
- R5-H3-03 PASS
- R5-H4-01 PASS
- R5-H5-01 PASS
- R5-H5-02 PASS
- R5-H5-03 PASS
- R5-H5-04 PASS
- R5-H5-05 PASS
- R5-H5-06 PASS
- R5-H5-07 PASS
- R5-H5-08 PASS
- R5-H5-09 PASS
- R5-H5-10 PASS
- R5-H5-11 PASS
- R5-H5-12 PASS
- R5-H5-13 PASS
- R5-H5-14 PASS
- R5-H5-15 PASS
- R5-H6-01 PASS
- R5-H7-01 PASS
- R5-H7-02 PASS
- R5-H7-03 PASS
- R5-H7-04 PASS
- R5-H7-05 PASS
- R5-H7-06 PASS
- R5-H7-07 PASS
- R5-H7-08 PASS
- R5-H7-09 PASS

## H1 and H8 lists (no record ID)

templates.md sets H1 and H8 as "short factual lists with citations", so file 04 gives them no record ID. Each list was checked with check A. They are not counted in the record totals.

- H1 list (04-history.md:17-39) PASS. Commit dates, parents, the commits-per-day table, the three intervals (23 h 25 min 3 s; 2 days 1 h 41 min 41 s; 11 min 59 s), the whole span (3 days 1 h 18 min 43 s), the inactivity figure (360 days 22 h 33 min), the `--stat` sizes of all 4 commits, the per-file change counts, the reflog entries and the shallow-repository value all match the repo and /home/user/audio/00-manifest.md:18, 26, 35.
- H8 list (04-history.md:574-590) PASS. The 4 subjects, the clean working tree, the absence of stashes and tags, the three quoted code comments (src/audio/pipeline.ts:195; src/airplay/sender.ts:278, 106), the unchecked list counts (README.md:240-245; CONTRIBUTING.md:129-134) and both cited searches match the repo.

## Check E searches

For every NOT FOUND record, the searches the record itself lists were run again and gave the results the record states. The table gives the one further search made for this check. Each was `git grep -n -i -E` over all tracked files except package-lock.json, unless the row says otherwise. "No hit on the question" means no line that answers the question as the record frames it.

| Record | Question | Terms used here | Result |
|--------|----------|-----------------|--------|
| R5-S01-04 | S01.3 | `bluetoothd`, `btmon`, `hcitool`, `hciconfig`, `obex`, `rfcomm`, `l2cap`, `gatt`, `\bble\b`, `bluepy`, `bleak`, `dbus-next`, `esp32`, `btstack`, `bluedroid`, `nimble`, `\bsink\b`, `wireless`, `radio`; package-lock.json for `noble`, `bleno`, `dbus`, `btstack`, `abstract-socket`, `usb`, `serialport` | Zero hits in both searches. |
| R5-S02-01 | S02.1 | `hci0`, `powered`, `power on`, `udev`, `\biface\b`, `interface`, `list-adapters`, `select`, `device id`, `/dev/`, `/sys/` | 9 hits, all the word "interface" or "selected" in TypeScript declarations and README prose (README.md:15, 32, 97; five `interface` declarations in src/; src/index.ts:90). No hit on the question. |
| R5-S02-02 | S02.2 | `broadcom`, `realtek`, `intel`, `\bcsr\b`, `cambridge`, `qualcomm`, `\bbcm`, `rtl8`, `cypress`, `ax200`, `vendor`, `built-in`, `onboard`, `hardware` | 1 hit: README.md:40, "macOS: Built-in (Bonjour)". No hit on the question. |
| R5-S02-03 | S02.3 | `unblock`, `soft.?block`, `hard.?block`, `no such device`, `ENODEV`, `unavailable`, `power cycle`, `hotplug`, `not present`, `unplug` | Zero hits. |
| R5-S03-01 | S03.1 | `advertis`, `visible`, `piscan`, `alias`, `class of device`, `\bcod\b`, `pairable`, `DiscoverableTimeout`, `visibility` | 6 hits, all about the Chromecast-style receiver (README.md:29, 172; src/chromecast/receiver.ts:252, 285, 289; src/index.ts:63). Same subject as the record's nearest related material. No Bluetooth hit. |
| R5-S03-02 | S03.2 | `RequestConfirmation`, `NoInputNoOutput`, `DisplayYesNo`, `simple pairing`, `\bbond`, `authoriz`, `RegisterAgent`, `pincode`, `pin code`, `passcode`, `auto.?accept` | Zero hits. |
| R5-S03-03 | S03.3 | `bonded`, `trusted`, `untrust`, `forget`, `link ?key`, `/var/lib`, `known devices`, `remembered`, `persist` | 2 hits: Dockerfile:10 (apt list clean-up) and README.md:16 ("Persistent configuration"). No hit on the question. |
| R5-S03-04 | S03.4 | `second device`, `another device`, `already connected`, `\bbusy\b`, `reject`, `max.?clients`, `single client`, `one device`, `one client`, `takeover` | 11 hits, all the identifier `reject` in promise code or the word "rejections" (src/airplay/sender.ts:171-259; src/chromecast/receiver.ts:245-280; src/index.ts:334-336). No hit on the question. |
| R5-S03-05 | S03.5 | `allow.?list`, `whitelist`, `blacklist`, `block.?list`, `\bdeny`, `permit`, `mac address`, a pattern for six colon-separated hex pairs, `authorized` | 1 hit: LICENSE:9, the word "permit". No hit on the question. |
| R5-S04-01 | S04.1 | `audio ?sink`, `a2dp_sink`, `0000110b`, `110b`, `RegisterEndpoint`, `MediaEndpoint`, `\bprofile`, `uuid`, `module-`, `endpoint` | 7 hits: the word "endpoint" for HTTP routes and AirPlay targets (README.md:32, 206; src/chromecast/receiver.ts:66, 99, 105, 145) and the `uuid:` text of the device description (src/chromecast/receiver.ts:205). No hit on the question. |
| R5-S04-02 | S04.2 | `mp3`, `opus`, `vorbis`, `flac`, `\bwav\b`, `\bpcm\b`, `alac`, `bitpool`, `negotiat`, `SetConfiguration`, `capabilit`, `mime` | 2 hits, both comments naming PCM as the format "required by AirPlay" on the output side (src/audio/pipeline.ts:191; src/bridge.ts:155). No codec offer or negotiation. |
| R5-S04-03 | S04.3 | `connection lost`, `link loss`, `auto.?connect`, `on boot`, `at boot`, `resume`, `last device`, `went offline`, `deviceLost`, `connection abort` | 10 hits, all on the AirPlay output side or the HTTP request (README.md:136; src/airplay/discovery.ts:81, 83; src/bridge.ts:25-126; src/chromecast/receiver.ts:181). Same material as the record's nearest related material. No Bluetooth hit. |
| R5-S04-04 | S04.4 | `multipoint`, `multi-point`, `priority`, `\bswitch\b`, `handover`, `active source`, `preempt`, `concurrent`, `simultaneous` | 6 hits: two section headings (CONTRIBUTING.md:138, 144), two README lines about several AirPlay outputs (README.md:13, 183) and two `switch` statements (src/chromecast/receiver.ts:118; src/index.ts:102). No hit on the question. |
| R5-S07-05 | S07.2 | `millisecond`, `\blag\b`, `real-?time`, `compensat`, a digit followed by `ms`, `low.?latency`, `buffer size`, `bufferSize` | 12 hits: the feature label "Real-time Transcoding" (README.md:14), which gives no figure, and the `bufferSize` lines of the unreferenced `AudioBuffer` class (src/audio/pipeline.ts:256-286), which the record already names. No latency figure, measurement or compensation. |
| R5-S07-06 | S07.3 | `skew`, `\bppm\b`, `rate adjust`, `adaptive`, `\bpll\b`, `\bpts\b`, `rtp`, `sequence number`, `in sync`, `\balign`, `sample count`, `crystal`, `synchron` | 4 hits: the two roadmap lines the record cites (README.md:242; CONTRIBUTING.md:141) and two comments naming RTP among steps the code does not perform (src/airplay/sender.ts:271, 279). No drift or synchronisation code. |
| R5-S11-04 | S11.2 | `phone`, `laptop`, `chrome\b`, `browser`, `tablet`, `galaxy`, `\bios\b`, `works with`, `verified`, `confirmed`, `compatible`, `tried`; also `git log --all --format=%B` filtered for `phone`, `android`, `iphone`, `spotify`, `homepod`, `airport` | 20 file hits: README diagram and prose naming "Phone/Browser" and "Chromecast-compatible" apps (README.md:11, 25, 30, 91; src/index.ts:68) and the variable `browser` in src/airplay/discovery.ts. Commit messages: one hit, the product description in e8801c3. No device named as tested. |
| R5-H4-01 | H4 | `git log --all --oneline --not awdeo_x86_kube`; `git branch -a --merged awdeo_x86_kube`; `git rev-parse --verify --quiet refs/stash`; `git log --all --merges --oneline`; `git log --all --format='%h %D'` | No commit outside the default branch; all 4 refs listed as merged; no stash ref; no merge commit; ref decorations on e556801 only. |
| R5-H6-01 | H6 | `@todo`, `\btbd\b`, `kludge`, `\bnb:`, `temporar`, `placeholder`, `\bstub`, `unimplemented`, `deprecated`, `caveat`, `known issue`, `\?\?\?`, `eventually`, `future`, `not supported`, `limitation`, `for simplicity`; `git ls-files` filtered for `.md`, `.txt`, `.rst`, `.adoc`, `changelog`, `notes`, `todo`, `issues` | 4 hits: LICENSE:7; README.md:210 and 213, both in the record's table (c); src/airplay/sender.ts:106, the comment "For simplicity, we'll just log it", which is not in the record's table (b) and is not one of the six markers H6 names. Document files: README.md and CONTRIBUTING.md only. No marker and no notes, changelog or known-issues file. |

## Notes (no effect on a verdict)

Outcome grading where the author wrote a result-style claim:

- Five records quote a commit-message line that reads as a claimed runtime result and grade the outcome UNVERIFIED with evidence NONE: R5-H2-04 and R5-S11-05 ("Memory leak rate reduced from ~50-100MB/day to near zero", commit 2db4cb5); R5-H2-05, R5-S10-09 and R5-S11-05 ("MTBF: Indefinite (log rotation prevents crash)" and the related lines, commit e556801); R5-H7-02 ("Auto-recovery: Discovery reconnects automatically", commit e556801); R5-H3-03 ("Production-ready for 24/7 operation", commit 2db4cb5).
- File 04 states the grading it applied to such lines at 04-history.md:9-13. File 03 applies the same grading without stating it.
- These records pass check C on this reading of Rule 4: the rule names what an outcome of WORKED, PARTIAL or FAILED needs, and UNVERIFIED claims none of it. Each record quotes the author's line in full, so the claim is on record.

Presence field without a citation of its own:

- 30 records in file 03 give a Presence value with no citation inside that field: R5-S08-08, R5-S08-09, R5-S08-12, R5-S09-01 to R5-S09-05, R5-S09-07, R5-S09-09, R5-S09-10, R5-S10-02 to R5-S10-06, R5-S10-08, R5-S10-10, R5-S10-11, R5-S11-07, R5-S11-08, R5-S12-01 to R5-S12-06. In each, the "What was attempted" field cites the current lines the Presence value rests on, and those lines were opened under check A.

Citation and wording slips:

- R5-S01-06: the quoted purpose "for audio transcoding" is at README.md:37 only. CONTRIBUTING.md:75, cited with it, lists "FFmpeg" with no purpose.
- R5-S08-08: "the only deletes are at src/airplay/sender.ts:75 and 321"; line 321 is `this.connections.clear()`.
- R5-S11-07: "Commit e8801c3 body ends:" introduces a sentence that opens the final paragraph of that body. The last sentence of the body is the one about "future iterations".
- R5-S11-08: the Outcome evidence field says "none of the items has code" with no citation. Two of the listed items name things that are present in the repo in some form: error handling (for example src/airplay/sender.ts:118-159) and documentation (README.md).
- R5-H7-02: "A browser `error` event, or an exception while starting, tears the browser down and schedules a restart". The teardown call is on the browser-error path only (src/airplay/discovery.ts:102-110). The start-exception path schedules a restart with no teardown (src/airplay/discovery.ts:45-51).
- H8 list: "its message ends with a list headed PRODUCTION READINESS ... and the line Tested". A "Dependencies Added" block sits between the two in commit e556801.

Counts in the three tallies:

- File 02 tally (18 finding records, 13 NOT FOUND, 24 questions), file 03 tally (53 finding records, 3 NOT FOUND, 24 questions) and file 04 tally (33 finding records, 2 NOT FOUND, 8 questions) match the records present.
- R5-H6-01 table (a) has 73 rows. A script compared each row's location, marker and text with `git grep -n -E 'FIX' -- src` (73 lines): all 73 match and no grep line is absent from the table. Table (b) has 12 rows, matching the 12 lines of the search the record cites.

## Coverage

Each question is listed with the records whose Question field names it. History records that also say "relates to" a stage question are listed under their history question only. NF marks a NOT FOUND record.

| Question | Records |
|----------|---------|
| S01.1 | R5-S01-01, R5-S01-02 |
| S01.2 | R5-S01-03 |
| S01.3 | R5-S01-04 (NF) |
| S01.4 | R5-S01-05, R5-S01-06 |
| S01.5 | R5-S01-07, R5-S01-08, R5-S01-09 |
| S02.1 | R5-S02-01 (NF) |
| S02.2 | R5-S02-02 (NF) |
| S02.3 | R5-S02-03 (NF) |
| S03.1 | R5-S03-01 (NF) |
| S03.2 | R5-S03-02 (NF) |
| S03.3 | R5-S03-03 (NF) |
| S03.4 | R5-S03-04 (NF) |
| S03.5 | R5-S03-05 (NF) |
| S04.1 | R5-S04-01 (NF) |
| S04.2 | R5-S04-02 (NF) |
| S04.3 | R5-S04-03 (NF) |
| S04.4 | R5-S04-04 (NF) |
| S05.1 | R5-S05-01, R5-S05-02 |
| S05.2 | R5-S05-03, R5-S05-04 |
| S05.3 | R5-S05-05 |
| S05.4 | R5-S05-06, R5-S05-07 |
| S06.1 | R5-S06-01 |
| S06.2 | R5-S06-02 |
| S06.3 | R5-S06-03 |
| S07.1 | R5-S07-01, R5-S07-02, R5-S07-03, R5-S07-04 |
| S07.2 | R5-S07-05 (NF) |
| S07.3 | R5-S07-06 (NF) |
| S08.1 | R5-S08-01, R5-S08-02 |
| S08.2 | R5-S08-03, R5-S08-04 |
| S08.3 | R5-S08-05, R5-S08-06, R5-S08-07, R5-S08-08, R5-S08-09, R5-S08-10, R5-S08-11, R5-S08-12 |
| S08.4 | R5-S08-13, R5-S08-14 |
| S09.1 | R5-S09-01, R5-S09-02, R5-S09-03, R5-S09-04 |
| S09.2 | R5-S09-05, R5-S09-06, R5-S09-07 |
| S09.3 | R5-S09-08 |
| S09.4 | R5-S09-09, R5-S09-10 |
| S10.1 | R5-S10-01, R5-S10-02 |
| S10.2 | R5-S10-03, R5-S10-04 |
| S10.3 | R5-S10-05 |
| S10.4 | R5-S10-06, R5-S10-07 |
| S10.5 | R5-S10-08, R5-S10-09, R5-S10-10 |
| S10.6 | R5-S10-11 |
| S11.1 | R5-S11-01, R5-S11-02, R5-S11-03 |
| S11.2 | R5-S11-04 (NF) |
| S11.3 | R5-S11-05 |
| S11.4 | R5-S11-06, R5-S11-07, R5-S11-08 |
| S12.1 | R5-S12-01, R5-S12-02, R5-S12-03, R5-S12-04 |
| S12.2 | R5-S12-05 |
| S12.3 | R5-S12-06, R5-S12-07 |
| H1 | No record ID. Answered by the factual list at 04-history.md:17-39, the form templates.md sets for H1. Not counted as MISSING. |
| H2 | R5-H2-01, R5-H2-02, R5-H2-03, R5-H2-04, R5-H2-05, R5-H2-06 |
| H3 | R5-H3-01, R5-H3-02, R5-H3-03 |
| H4 | R5-H4-01 (NF) |
| H5 | R5-H5-01, R5-H5-02, R5-H5-03, R5-H5-04, R5-H5-05, R5-H5-06, R5-H5-07, R5-H5-08, R5-H5-09, R5-H5-10, R5-H5-11, R5-H5-12, R5-H5-13, R5-H5-14, R5-H5-15 |
| H6 | R5-H6-01 (NF) |
| H7 | R5-H7-01, R5-H7-02, R5-H7-03, R5-H7-04, R5-H7-05, R5-H7-06, R5-H7-07, R5-H7-08, R5-H7-09 |
| H8 | No record ID. Answered by the factual list at 04-history.md:574-590, the form templates.md sets for H8. Not counted as MISSING. |

Questions listed: 56 (S01.1 to S12.3: 48; H1 to H8: 8). Questions with at least one record: 54. Questions answered by a template list with no record ID: 2 (H1, H8). Questions with neither a record nor a list: 0.

## Totals

| | Finding records | NOT FOUND records | All records |
|---|---|---|---|
| PASS | 103 | 18 | 121 |
| FAIL | 1 | 0 | 1 |
| Total | 104 | 18 | 122 |

- PASS: 121
- FAIL: 1 (R5-H3-01, check A)
- MISSING: 0. H1 and H8 carry no record ID because templates.md sets them as factual lists; both lists were checked and both hold. If a question answered only by such a list is to be counted as MISSING, the count is 2.
