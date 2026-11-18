# R5 verification of files 02, 03 and 04

- Repo: /home/user/awdeo, repo ID R5. HEAD e556801cdd966f7bb86b8e7d2400718d38e8866c at the start and at the end of this check (git rev-parse HEAD). `git status --porcelain` printed zero lines at the start and at the end.
- Files checked: /home/user/audio/repos/R5/02-stages-bluetooth.md (31 records), /home/user/audio/repos/R5/03-stages-audio.md (56 records), /home/user/audio/repos/R5/04-history.md (35 records, plus the H1 and H8 lists).
- Records checked: 104 finding records and 18 NOT FOUND records, 122 in all. No record ID occurs twice.
- Role: verifier. No record was changed. No finding was added.
- An earlier 05-verification.md was in this folder when this check began; 04-history.md carries a later modification time than that file. This file replaces it. Every verdict below comes from this check.

## Method

- Read in full at HEAD: all 8 files under `src/`, README.md, CONTRIBUTING.md, Dockerfile, docker-compose.yml, .github/workflows/ci.yml, package.json, tsconfig.json, config.example.json, .gitignore, .dockerignore. package-lock.json was read at every cited line range and searched. LICENSE was read at lines 1-12 and searched. All 4 commit messages were read in full (git log --all --format=%B). Files at older commits were read with `git show REF:PATH`.
- Commands run in the repo, all read-only: `git log` (with `--all`, `--stat`, `--name-status`, `--name-only`, `--grep`, `-G`, `-g`, `-L`, `--merges`, `--diff-filter=D`, `--not`), `git show`, `git diff`, `git grep` (working tree and commits e8801c3, a744781, 2db4cb5, e556801), `git branch -a`, `git tag`, `git stash list`, `git status`, `git rev-parse`, `git shortlog`, `git ls-files`, and, because R5-H4-01 cites them, `git count-objects -v` and `git rev-list --all --objects`. Nothing was checked out, built or run.
- Check A: each cited path:line, commit quotation and manifest line was opened. Each search or git command that a record names as its basis was run again and its result compared with the record.
- Check B: each Outcome evidence grade and each cause grade was compared with Rule 3. A script confirmed that all 104 finding records carry the nine template fields in template order and all 18 NOT FOUND records carry the three.
- Check C: each outcome was compared with its evidence under Rule 4. A script gave the pairs: UNVERIFIED with NONE in 94 records, FAILED with STATED in 8 (R5-S01-06, R5-S06-02, R5-S07-01, R5-S07-03, R5-S08-04, R5-H5-04, R5-H5-05, R5-H5-06), WORKED with STATED in 2 (R5-S01-02, R5-S11-03). No record uses PARTIAL or OBSERVED.
- Check D: the three files were searched for the words listed in Rule 6, for softer forms of advice ("needs to", "must be", "has to", "worth", "rather than"), for ranking words, and for the IDs and names of the other repos. Every Rule 6 word found sits inside a quotation from the repo (02-stages-bluetooth.md:99; 04-history.md:93, 198, 394, 451, 460, 461, 475). No other repo is named.
- Check E: one search with terms the record did not use was run for each NOT FOUND record. Terms and results are in the section "Check E searches".

Standard used for check A. FAIL where a cited location is absent; where quoted words are not at a location they are cited to; or where a statement of fact (a number, a name, a search result, what a path in the code does) is false at the location cited for it or carries no citation and is contradicted by the repo. A paraphrase that leaves out a detail without contradicting the source passes; such points are listed under "Notes".

## Verdicts: file 02 (Bluetooth stages S01 to S06)

- R5-S01-01 PASS
- R5-S01-02 PASS
- R5-S01-03 PASS
- R5-S01-04 PASS
- R5-S01-05 PASS
- R5-S01-06 FAIL A: the quoted words "for audio transcoding" are cited to README.md:37 and CONTRIBUTING.md:75; CONTRIBUTING.md:75 reads "- FFmpeg" and does not hold them.
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
- R5-S11-07 FAIL A: the record says `bug` appears only in CONTRIBUTING.md prose and a file name pattern; the string is also inside `debug` at README.md:158, 160, CONTRIBUTING.md:21, src/airplay/sender.ts:101, 273, src/audio/pipeline.ts:105 and src/chromecast/receiver.ts:161. The sentence quoted after "Commit e8801c3 body ends:" is followed in that body by one more sentence.
- R5-S11-08 FAIL A: the statement "none of the items has code" carries no citation, and one item the record lists, error handling, has code (src/airplay/sender.ts:118-159; src/index.ts:311-346).
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
- R5-H3-01 PASS
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
- R5-H7-02 FAIL A: the record says an exception while starting "tears the browser down"; that path (src/airplay/discovery.ts:45-51) calls `scheduleReconnect()` and rethrows with no call to `cleanup()`. The teardown is on the browser-error path only (src/airplay/discovery.ts:102-110).
- R5-H7-03 PASS
- R5-H7-04 PASS
- R5-H7-05 PASS
- R5-H7-06 PASS
- R5-H7-07 PASS
- R5-H7-08 PASS
- R5-H7-09 PASS

## H1 and H8 lists (no record ID)

templates.md sets H1 and H8 as "short factual lists with citations", so file 04 gives them no record ID. Each list was put through check A. Neither is counted in the record totals.

- H1 list (04-history.md:17-39) holds. Commit hashes, dates, parents and author name; the commits-per-day table; the three intervals (23 h 25 min 3 s; 2 days 1 h 41 min 41 s; 11 min 59 s); the whole span (3 days 1 h 18 min 43 s); the inactivity figure (360 days 22 h 33 min, from 2025-11-21 08:48:35 +0000 to the snapshot time at /home/user/audio/00-manifest.md:18); the `--stat` sizes of all 4 commits; the per-file change counts; the reflog entries and the shallow-repository value all match the repo and /home/user/audio/00-manifest.md:26, 35.
- H8 list (04-history.md:574-590) holds. The 4 subjects, the clean working tree, the absence of stashes and tags, the three quoted code comments (src/audio/pipeline.ts:195; src/airplay/sender.ts:278, 106), the unchecked list counts (README.md:240-245; CONTRIBUTING.md:129-134) and both cited searches match the repo. One wording point is under "Notes".

## Check E searches

Each search was `git grep -n -i -E` over all tracked files except package-lock.json, unless the row says otherwise. "No hit on the question" means no line that answers the question as the record frames it. Every NOT FOUND record passes check E.

| Record | Question | Terms used here | Result |
|--------|----------|-----------------|--------|
| R5-S01-04 | S01.3 | `bluetoothd`, `btmon`, `hcitool`, `hciconfig`, `rfcomm`, `l2cap`, `gatt`, `\bble\b`, `bleak`, `dbus-next`, `btstack`, `bluedroid`, `nimble`, `esp32`, `esp-idf`, `\bsink\b`, `wireless`, `\bbt\b`, `bt_`, `obex`; package-lock.json for `abstract-socket`, `x11`, `node-gyp`, `\busb\b`, `serialport`, `bindings` | Zero hits in both searches. |
| R5-S02-01 | S02.1 | `hci0`, `hci1`, `powered`, `power on`, `udev`, `select`, `\binterface\b`, `/dev/`, `/sys/`, `device id`, `\bradio\b` | 8 hits: the word "interface" in README prose (README.md:15, 97) and five TypeScript `interface` declarations in src, and "selected" at README.md:32. No hit on the question. |
| R5-S02-02 | S02.2 | `broadcom`, `realtek`, `intel\b`, `\bcsr\b`, `cambridge`, `qualcomm`, `\bbcm`, `rtl8`, `cypress`, `\bvendor`, `built-in`, `onboard`, `hardware`, `\bmodel\b` | 1 hit: README.md:40, "macOS: Built-in (Bonjour)". No hit on the question. |
| R5-S02-03 | S02.3 | `unblock`, `soft.?block`, `hard.?block`, `ENODEV`, `no such device`, `not available`, `unavailable`, `unplug`, `hotplug`, `power cycle`, `\breset\b` | 1 hit: src/airplay/discovery.ts:76, a comment on the mDNS reconnect counter. No hit on the question. |
| R5-S03-01 | S03.1 | `advertis`, `visible`, `visibility`, `piscan`, `alias`, `class of device`, `\bcod\b`, `pairable`, `DiscoverableTimeout` | 6 hits, all about the Chromecast-style receiver (README.md:29, 172; src/chromecast/receiver.ts:252, 285, 289; src/index.ts:63). Same subject as the record's nearest related material. No Bluetooth hit. |
| R5-S03-02 | S03.2 | `RequestConfirmation`, `NoInputNoOutput`, `DisplayYesNo`, `KeyboardDisplay`, `simple pairing`, `\bssp\b`, `\bbond`, `authoriz`, `RegisterAgent`, `pincode`, `pin code`, `passcode`, `auto.?accept`, `just.?works` | Zero hits. |
| R5-S03-03 | S03.3 | `bonded`, `trusted`, `untrust`, `forget`, `link ?key`, `/var/lib`, `known devices`, `remembered`, `persist`, `paired-devices` | 2 hits: Dockerfile:10 (apt list clean-up) and README.md:16 ("Persistent configuration"). No hit on the question. |
| R5-S03-04 | S03.4 | `second device`, `another device`, `already connected`, `\bbusy\b`, `reject`, `max.?clients`, `single client`, `one device`, `takeover`, `\bkick` | 11 hits, all the identifier `reject` in promise code or the word "rejections" (src/airplay/sender.ts:171-259; src/chromecast/receiver.ts:245-280; src/index.ts:334-336). No hit on the question. |
| R5-S03-05 | S03.5 | `allow.?list`, `whitelist`, `blacklist`, `block.?list`, `\bdeny`, `permit`, `mac address`, a pattern for six colon-separated hex pairs, `authorized` | 1 hit: LICENSE:9, the word "permit". No hit on the question. |
| R5-S04-01 | S04.1 | `audio ?sink`, `a2dp_sink`, `0000110b`, `110b`, `RegisterEndpoint`, `MediaEndpoint`, `\bprofile`, `uuid`, `module-`, `endpoint`, `\brole\b` | 7 hits: the word "endpoint" for HTTP routes and AirPlay targets (README.md:32, 206; src/chromecast/receiver.ts:66, 99, 105, 145) and the `uuid:` text of the device description (src/chromecast/receiver.ts:205). No hit on the question. |
| R5-S04-02 | S04.2 | `mp3`, `opus`, `vorbis`, `flac`, `\bwav\b`, `\bpcm\b`, `alac`, `bitpool`, `negotiat`, `SetConfiguration`, `capabilit`, `mime`, `bitrate` | 2 hits, both comments naming PCM as the format "required by AirPlay" on the output side (src/audio/pipeline.ts:191; src/bridge.ts:155). No codec offer or negotiation. |
| R5-S04-03 | S04.3 | `connection lost`, `link loss`, `auto.?connect`, `on boot`, `at boot`, `resume`, `last device`, `went offline`, `deviceLost`, `supervision` | 9 hits, all on the AirPlay output side (README.md:136; src/airplay/discovery.ts:81, 83; src/bridge.ts:25, 81, 92, 115, 116, 126). No Bluetooth hit and no input-side reconnect. |
| R5-S04-04 | S04.4 | `multipoint`, `multi-point`, `priority`, `\bswitch\b`, `handover`, `active source`, `preempt`, `concurrent`, `simultaneous` | 6 hits: two section headings (CONTRIBUTING.md:138, 144), two README lines about several AirPlay outputs (README.md:13, 183) and two `switch` statements (src/chromecast/receiver.ts:118; src/index.ts:102). No hit on the question. |
| R5-S07-05 | S07.2 | `millisecond`, `\blag\b`, `real-?time`, `compensat`, a number followed by `ms`, `low.?latency`, `buffer ?size`, `\boffset\b` | 12 hits: the feature label "Real-time Transcoding" (README.md:14), which gives no figure, and the buffer-size lines of the unreferenced `AudioBuffer` class (src/audio/pipeline.ts:256-286), which the record already names. No latency figure, measurement or compensation. |
| R5-S07-06 | S07.3 | `skew`, `\bppm\b`, `adaptive`, `\bpll\b`, `\bpts\b`, `\brtp\b`, `sequence number`, `\balign`, `sample count`, `crystal`, `slew`, `master clock`, `timing` | 2 hits: comments naming RTP among steps the code does not perform (src/airplay/sender.ts:271, 279). No drift or synchronisation code. |
| R5-S11-04 | S11.2 | `phone`, `laptop`, `\bchrome\b`, `browser`, `tablet`, `galaxy`, `\bios\b`, `works with`, `verified`, `confirmed`, `compatible`, `tried`, `\bmac\b`; also `git log --all --format=%B` filtered for `phone`, `android`, `iphone`, `verified on`, `works on`, `tested on`, `tested with`, `homepod`, `airport` | 20 file hits: README lines naming "Phone/Browser" and "Chromecast-compatible" apps (README.md:11, 25, 30, 91; src/index.ts:68) and the variable `browser` in src/airplay/discovery.ts. Commit messages: the product description in commit e8801c3 only. No device named as tested. |
| R5-H4-01 | H4 | `git log --all --oneline --not awdeo_x86_kube`; `git branch -a --merged awdeo_x86_kube`; `git rev-parse --verify --quiet refs/stash`; `git log --all --merges --oneline`; `git log --all --format='%h %D'` | No commit outside the default branch; all 4 refs listed as merged; no stash ref; no merge commit; ref names on e556801 only. |
| R5-H6-01 | H6 | `@todo`, `\btbd\b`, `kludge`, `\bnb:`, `temporar`, `placeholder`, `\bstub`, `unimplemented`, `deprecated`, `caveat`, `known issue`, `\?\?\?`, `quick fix`, `band-?aid`, `revisit`, `for simplicity`; `git ls-files` filtered for `.md`, `.txt`, `.rst`, `.adoc`, `bugs`, `roadmap`, `release` | 1 hit: src/airplay/sender.ts:106, the comment "For simplicity, we'll just log it". It is none of the six markers H6 names, and it is absent from the record's table (b). Document files: README.md and CONTRIBUTING.md only. No marker and no notes, changelog or known-issues file. |

## Notes (no effect on a verdict)

Outcome grading where a commit message reads as a claimed result:

- Six records quote a commit-message line that asserts a result and grade the outcome UNVERIFIED with evidence NONE: R5-H2-04 and R5-S11-05 ("Memory leak rate reduced from ~50-100MB/day to near zero", commit 2db4cb5); R5-H2-05 and R5-S11-05 ("MTBF: Indefinite (log rotation prevents crash)", commit e556801); R5-S10-09 ("MTBF: 3-7 days (disk full crash guaranteed)", commit e556801, said of the removed approach); R5-H7-02 ("Auto-recovery: Discovery reconnects automatically", commit e556801); R5-H3-03 ("Production-ready for 24/7 operation", commit 2db4cb5).
- File 04 states the grading it applied to such lines at 04-history.md:9-13. File 03 applies the same grading without stating it.
- These records pass checks B and C on this reading: Rule 4 names what an outcome of WORKED, PARTIAL or FAILED needs, an outcome of UNVERIFIED claims none of it, and the template value for its evidence is NONE. Each record quotes the author's line, so the claim is on record.
- Rule 3 defines STATED as "the author wrote it" with no further condition, and Rule 5 glosses UNVERIFIED as "no evidence of the result". On a reading where any author line asserting a result counts as STATED evidence, these six records would fail check B. The choice between the two readings is not settled by the rules text.

Presence field with no citation of its own:

- 26 records in file 03 give Presence as the single word CURRENT: R5-S08-08, R5-S08-09, R5-S08-12, R5-S09-01 to R5-S09-05, R5-S09-07, R5-S09-09, R5-S09-10, R5-S10-02 to R5-S10-06, R5-S10-08, R5-S10-10, R5-S10-11, R5-S11-08, R5-S12-01 to R5-S12-06. templates.md words the field as "with citation". None of checks A to D tests field completeness, so no verdict rests on this. In each of the 26, the "What was attempted" field cites the HEAD lines the value rests on, and those lines were opened under check A.

Wording points that pass on the standard above:

- R5-S07-03: "for each connection that has a socket". The condition at src/airplay/sender.ts:89 is `connection.connected && connection.socket`.
- R5-S08-08: "the only deletes are at src/airplay/sender.ts:75 and 321". Line 321 is `this.connections.clear()`.
- R5-S09-03: the search for `gpio`, `led`, `button`, `display`, `lcd`, `oled` gives zero hits with whole-word matching and 46 hits as a substring search (for example "failed", "scheduled"). The record does not say which form was used.
- R5-S09-06: "No HTTP route lists, adds or removes output devices or sets volume". The same field states that `GET /health` returns connected device names (src/bridge.ts:336-338).
- R5-H3-02: "three health routes" added by commit e556801. `/healthz` and `/ready` are new in that commit; `/health` existed in commit e8801c3 and was rewritten.
- H8 list: "its message ends with a list headed PRODUCTION READINESS ... and the line Tested". A "Dependencies Added" block sits between the two in commit e556801.

Counts in the three tallies:

- File 02 tally (18 finding records, 13 NOT FOUND, 24 questions), file 03 tally (53 finding records, 3 NOT FOUND, 24 questions) and file 04 tally (33 finding records, 2 NOT FOUND, 8 questions) match the records present.
- R5-H6-01 table (a) has 73 rows. A script compared each row's location, marker and text with `git grep -n -E 'FIX' -- src` (73 lines): all 73 match and no grep line is absent from the table. The counts 34 numbered, 23 from commit 2db4cb5 and 16 unnumbered from commit e556801 match `git show <commit> -- src`. Table (b) has 12 rows, matching the 12 lines of the search the record cites.

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
| PASS | 100 | 18 | 118 |
| FAIL | 4 | 0 | 4 |
| Total | 104 | 18 | 122 |

- PASS: 118
- FAIL: 4 (R5-S01-06, R5-S11-07, R5-S11-08, R5-H7-02; all check A)
- MISSING: 0. H1 and H8 carry no record ID because templates.md sets them as factual lists; both lists were checked and both hold. Counted strictly as "a question with no record", the figure is 2.
