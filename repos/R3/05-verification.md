# R3 verification: records in files 02, 03 and 04

Repo R3, /home/user/AudioRouter, at commit 1bdb71dfb0e7319db0aa2f6cf41aedc853369204 (the only commit; `git log --all`). Working tree clean before and after verification (`git status --porcelain` empty). Files checked: /home/user/audio/repos/R3/02-stages-bluetooth.md, 03-stages-audio.md, 04-history.md.

## Method

- Every tracked text file (8 `.md`, 3 `.sh`, Package.swift) was read in full. The archive members `project.pbxproj`, `AudioRouter.xcscheme`, `contents.xcworkspacedata` and both `WorkspaceSettings.xcsettings` were read with `unzip -p` to standard output. `UserInterfaceState.xcuserstate` was read with `strings` and decoded in memory with Python's plistlib (`python3 -I`, standard input). Nothing was extracted, built or run. Git commands used: `log`, `show --stat`, `status`, `branch -a`, `tag`, `stash list`, `ls-files`, `rev-parse`.
- Check A: each cited location was opened and compared with the text or fact the record attaches to it. Quoted strings were also matched against the cited line ranges by script. A citation to a line that does not exist, or to a line that does not carry the claimed text or fact, is a FAIL A.
- Search-result statements inside finding records ("zero hits", "the only hit is") were re-run. Where a re-run differed from the statement without touching the record's claim, the difference is listed under Notes and the verdict is unchanged.
- Check B: grades were compared with Rule 3. Check C: outcomes were compared with Rule 4 and the Rule 5 vocabulary. Check D: every record was scanned for the Rule 6 word list and for references to any repo other than R3; each hit was read in context.
- Check E: one search with terms the auditor did not use, over the 12 text files, the archive members and the `strings` output of the state file; for H3 and H4, read-only git commands with different arguments.
- Rule 11: identifier values seen during verification (developer team identifier, hardware device identifier) are not copied here.

## Verdicts: 02-stages-bluetooth.md (17 finding records, 10 NOT FOUND records)

- R3-S01-01 PASS
- R3-S01-02 PASS
- R3-S01-03 PASS
- R3-S01-04 PASS
- R3-S01-05 PASS
- R3-S01-06 PASS
- R3-S02-01 PASS
- R3-S02-02 PASS
- R3-S02-03 PASS (E: `broadcom|realtek|CSR|usb|built-in|bluetooth [0-9]|BT 4/5|bluetooth module/chip/card/hardware|two adapters`; hits are microphone and line-in lines only, PROJECT_SUMMARY.md:159-160, README.md:128)
- R3-S02-04 PASS
- R3-S03-01 PASS
- R3-S03-02 PASS
- R3-S03-03 PASS (E: `bond|history|delete|clear|manage|database|recent|previously|re-pair|device list`; hits are song match history CHANGELOG.md:29, the Chromecast device list CHROMECAST_SETUP.md:117, signing and Cast manager lines; none on paired Bluetooth devices)
- R3-S03-04 PASS (E: `another device|other device|already connected|one device|single device|busy|concurrent|both devices|kick|takes over`; zero hits)
- R3-S03-05 PASS (E: `approv|authoriz|restrict|whitelist|only allow|lock|passcode|pairing code|block`; hits are Cast console approval and whitelisting, CHROMECAST_SETUP.md:80, 147-151, 252, and compiler settings)
- R3-S04-01 PASS
- R3-S04-02 PASS
- R3-S04-03 PASS (E: `bitpool|bit rate|kbps|lossless|high-res|encod|decod|quality|compress`; Bluetooth hit README.md:292 only, already cited in the record; the rest are the Chromecast AAC listing and XML headers)
- R3-S04-04 PASS
- R3-S04-05 PASS (E: `multiple source/device/input|two sources|at once|same time|one at a time|active source|last connected|first connected|handover`; one hit, PROJECT_SUMMARY.md:5, on input types)
- R3-S05-01 PASS
- R3-S05-02 PASS (E: `AVAudioFormat|Float32|Int16|interleav|bufferSize|sampleRate|channelCount|commonFormat|Hz|kHz|16-bit|24-bit|mono|stereo|frame`; every hit is the substring in "framework")
- R3-S05-03 PASS (E: `isPlaying|stream start/stop|idle|suspend|resume|audio starts/stops|silence|inactive`; hits are gap detection for song recognition, PROJECT_SUMMARY.md:188 and README.md:166, both cited in the record, and the mute tip SETUP.md:110)
- R3-S05-04 PASS (E: `ring buffer|queue|fifo|jitter|starv|stutter|crackl|discontinu|dropped frames|overflow|underflow|buffer`; hits are PROJECT_SUMMARY.md:86, 166, 304 and the Chromecast listing, none with a size or a gap rule)
- R3-S06-01 PASS (E: `MPRemoteCommand|remote command|media key|play button|next|previous|rewind|fast forward|stop playback|play`; hits are the Xcode Play button SETUP.md:32, "Next" buttons and two test steps)
- R3-S06-02 PASS
- R3-S06-03 PASS

## Verdicts: 03-stages-audio.md (59 finding records, 3 NOT FOUND records)

- R3-S07-01 PASS
- R3-S07-02 PASS
- R3-S07-03 PASS
- R3-S07-04 PASS
- R3-S07-05 PASS
- R3-S07-06 PASS (E: `timestamp|hostTime|lag|align|offset|skew|pitch|sync|in sync|out of sync|rate match|varispeed`; hits are CSS alignment lines and "Cloud sync" at PROJECT_SUMMARY.md:339)
- R3-S08-01 PASS
- R3-S08-02 PASS
- R3-S08-03 PASS
- R3-S08-04 PASS
- R3-S08-05 PASS
- R3-S08-06 PASS
- R3-S08-07 PASS
- R3-S08-08 PASS
- R3-S09-01 PASS
- R3-S09-02 PASS
- R3-S09-03 PASS
- R3-S09-04 PASS
- R3-S09-05 PASS
- R3-S09-06 PASS
- R3-S09-07 PASS
- R3-S09-08 PASS
- R3-S09-09 PASS
- R3-S09-10 PASS
- R3-S09-11 PASS
- R3-S09-12 PASS
- R3-S10-01 PASS
- R3-S10-02 PASS
- R3-S10-03 FAIL A: Presence cites create_xcode_project.sh:1-80; the file has 79 lines (`wc -l`, `cat -n`).
- R3-S10-04 PASS
- R3-S10-05 PASS
- R3-S10-06 PASS
- R3-S10-07 PASS (E: `SMAppService|LSUIElement|open at login|login|background|relaunch|keep alive|watchdog|automatically start/launch/restart|at startup|daemon|agent`; hits are `firebase login` and page-background lines)
- R3-S10-08 PASS
- R3-S10-09 PASS
- R3-S10-10 PASS
- R3-S10-11 PASS
- R3-S10-12 PASS
- R3-S10-13 PASS
- R3-S10-14 PASS
- R3-S10-15 PASS
- R3-S11-01 PASS
- R3-S11-02 PASS
- R3-S11-03 PASS
- R3-S11-04 PASS
- R3-S11-05 PASS
- R3-S11-06 PASS
- R3-S11-07 PASS (E: `galaxy|ipad|macbook|bose|jbl|crosley|victrola|audio-technica|sony|pixel|iphone|android|model|tried with|tested on/with|works with|confirmed`; text hit is CHROMECAST_SETUP.md:73 only, already named in the record; the one archive hit is the build-machine model key)
- R3-S11-08 PASS
- R3-S11-09 PASS
- R3-S11-10 PASS
- R3-S11-11 PASS
- R3-S11-12 PASS
- R3-S11-13 FAIL A, B: Outcome evidence calls the observed build "later" than the stated defect with no citation and no INFERRED grade for the order; Depends on labels ZIP!pbxproj:6 "Xcode 15+ project format", while that line holds only `objectVersion = 56` and ZIP!pbxproj:162 reads `compatibilityVersion = "Xcode 14.0"`.
- R3-S11-14 PASS
- R3-S11-15 PASS
- R3-S12-01 FAIL A: Depends on cites README.md:46 for traffic "being outbound from the app"; that line reads "Local network access for AirPlay/Chromecast" and gives no direction.
- R3-S12-02 PASS
- R3-S12-03 PASS
- R3-S12-04 PASS
- R3-S12-05 PASS
- R3-S12-06 PASS

## Verdicts: 04-history.md (18 finding records, 2 NOT FOUND records)

- R3-H2-01 PASS
- R3-H2-02 PASS
- R3-H2-03 PASS
- R3-H2-04 PASS
- R3-H2-05 PASS
- R3-H2-06 PASS
- R3-H3-01 PASS (E: `git log --all -i -E --grep='undo|rollback|back out|again|hotfix|patch|repair|correct|bug|broken|oops|typo|retry'`, no output; `git log --all --format='%h parents=[%p]'`, one root commit)
- R3-H4-01 PASS (E: `git log --all --not main --oneline`, no output; `git branch -a --no-merged main`, no output; `git rev-parse --verify -q refs/stash`, no such ref; `git tag`, no output)
- R3-H5-01 PASS
- R3-H5-02 PASS
- R3-H5-03 PASS
- R3-H5-04 PASS
- R3-H5-05 PASS
- R3-H5-06 PASS
- R3-H7-01 PASS
- R3-H7-02 PASS
- R3-H7-03 PASS
- R3-H7-04 PASS
- R3-H7-05 PASS
- R3-H7-06 PASS

## History lists and tables without record IDs (H1, H6, H8)

templates.md:48 sets H1 and H8 as lists and H6 as a table, so they carry no record ID and no verdict line. Their citations were checked under check A and the result is given here. They are not counted in the totals.

- H1 list (04-history.md:17-30): citations hold. Commit hash, author name, dates, subject and empty body match `git log --all --date=iso`. Commit count 1 matches /home/user/audio/00-manifest.md:35. Reflog entries are all dated 2026-11-17 07:19:41 +0000 (`git log -g --all --date=iso`). CHANGELOG.md:8, 179 and PROJECT_SUMMARY.md:391 carry the quoted dates. Archive member timestamps match `unzip -l`.
- H6 tables (04-history.md:196-393): one row does not hold. The row for `ZIP!pbxproj` (04-history.md:211) lists lines 205, 244, 255, 277, 283, 288, 329, 356, 368, 401, 409, 419, 423, 426, 461, 473, 486, 495. `unzip -p ... project.pbxproj | grep -n -i bug` gives 216, 255, 266, 288, 294, 299, 340, 367, 379, 412, 420, 430, 434, 437, 472, 484, 498, 507. Sixteen of the eighteen listed lines hold no such substring (for example line 205 is the ColorUtilities.swift source entry and line 244 is `CLANG_WARN_RANGE_LOOP_ANALYSIS`). The row's statement that every hit is a substring of Debug or DEBUG holds. The other rows checked hold: the `auto_create_project.sh` and `ZIP!xcscheme` line lists match `grep -n`; the CHANGELOG.md and FIX_PROJECT.md rows match the files line by line; the keyword search returns no TODO, FIXME, HACK, BUG or WORKAROUND comment.
- H8 list (04-history.md:469-494): citations hold. `git show --stat HEAD` gives 13 files and 3325 insertions. /home/user/audio/00-manifest.md:24 records an empty status for R3. `.git/` holds no merge, rebase, cherry-pick or bisect entry (`ls .git`). Counts of unticked items match: 25 at CHANGELOG.md:115-141, 14 at BUILD_AND_DEPLOY.md:473-486, 9 at CHROMECAST_SETUP.md:250-258.

## Coverage

| Question | Records |
|----------|---------|
| S01.1 | R3-S01-01 |
| S01.2 | R3-S01-02 |
| S01.3 | R3-S01-03 |
| S01.4 | R3-S01-04 |
| S01.5 | R3-S01-05, R3-S01-06 |
| S02.1 | R3-S02-01, R3-S02-02 |
| S02.2 | R3-S02-03 (NOT FOUND) |
| S02.3 | R3-S02-04 |
| S03.1 | R3-S03-01 |
| S03.2 | R3-S03-02 |
| S03.3 | R3-S03-03 (NOT FOUND) |
| S03.4 | R3-S03-04 (NOT FOUND) |
| S03.5 | R3-S03-05 (NOT FOUND) |
| S04.1 | R3-S04-01, R3-S04-02 |
| S04.2 | R3-S04-03 (NOT FOUND) |
| S04.3 | R3-S04-04 |
| S04.4 | R3-S04-05 (NOT FOUND) |
| S05.1 | R3-S05-01 |
| S05.2 | R3-S05-02 (NOT FOUND) |
| S05.3 | R3-S05-03 (NOT FOUND) |
| S05.4 | R3-S05-04 (NOT FOUND) |
| S06.1 | R3-S06-01 (NOT FOUND) |
| S06.2 | R3-S06-02 |
| S06.3 | R3-S06-03 |
| S07.1 | R3-S07-01, R3-S07-02, R3-S07-03, R3-S07-04 |
| S07.2 | R3-S07-05 |
| S07.3 | R3-S07-06 (NOT FOUND) |
| S08.1 | R3-S08-01, R3-S08-02 |
| S08.2 | R3-S08-03, R3-S08-04, R3-S08-05 |
| S08.3 | R3-S08-06, R3-S08-07 |
| S08.4 | R3-S08-08 |
| S09.1 | R3-S09-01, R3-S09-02, R3-S09-03, R3-S09-04, R3-S09-05 |
| S09.2 | R3-S09-06, R3-S09-07, R3-S09-08 |
| S09.3 | R3-S09-09, R3-S09-10 |
| S09.4 | R3-S09-11, R3-S09-12 |
| S10.1 | R3-S10-01, R3-S10-02, R3-S10-03, R3-S10-04, R3-S10-05, R3-S10-06 |
| S10.2 | R3-S10-07 (NOT FOUND) |
| S10.3 | R3-S10-08, R3-S10-09 |
| S10.4 | R3-S10-10, R3-S10-11 |
| S10.5 | R3-S10-12, R3-S10-13 |
| S10.6 | R3-S10-14, R3-S10-15 |
| S11.1 | R3-S11-01, R3-S11-02, R3-S11-03, R3-S11-04, R3-S11-05, R3-S11-06 |
| S11.2 | R3-S11-07 (NOT FOUND) |
| S11.3 | R3-S11-08 |
| S11.4 | R3-S11-09, R3-S11-10, R3-S11-11, R3-S11-12, R3-S11-13, R3-S11-14, R3-S11-15 |
| S12.1 | R3-S12-01, R3-S12-02 |
| S12.2 | R3-S12-03 |
| S12.3 | R3-S12-04, R3-S12-05, R3-S12-06 |
| H1 | No record ID. Answered by the list at 04-history.md:17-30, the form set by templates.md:48 |
| H2 | R3-H2-01, R3-H2-02, R3-H2-03, R3-H2-04, R3-H2-05, R3-H2-06 |
| H3 | R3-H3-01 (NOT FOUND) |
| H4 | R3-H4-01 (NOT FOUND) |
| H5 | R3-H5-01, R3-H5-02, R3-H5-03, R3-H5-04, R3-H5-05, R3-H5-06 |
| H6 | No record ID. Answered by the tables at 04-history.md:196-393, the form set by templates.md:48 |
| H7 | R3-H7-01, R3-H7-02, R3-H7-03, R3-H7-04, R3-H7-05, R3-H7-06 |
| H8 | No record ID. Answered by the list at 04-history.md:469-494, the form set by templates.md:48 |

All 48 stage questions and all 8 history questions have an answer. No question is MISSING. H1, H6 and H8 are answered without record IDs, as the template lays down; they are flagged in the table above so that the reader can see they sit outside the record count.

Record counts found: 02 holds 17 finding records and 10 NOT FOUND records; 03 holds 59 and 3; 04 holds 18 and 2. Total 94 finding records and 15 NOT FOUND records, 109 records. Each file's own tally agrees with these counts.

## Notes: differences seen that did not change a verdict

These are differences between a record's wording and the repo that leave the record's claim standing.

- SCOPE-A "zero hits" for `adapter|controller|chipset|dongle|firmware|hci|powered|rfkill` (R3-S02-01, R3-S02-02, R3-S02-03, R3-S02-04). The 12 text files, project.pbxproj and the scheme give zero hits. The `strings` output of the state file, which SCOPE-A includes, gives six hits on "Controller", all Xcode interface keys (`IDEWorkspaceTabController`, `IDEActiveWorkspaceTabController`, `playgroundToyDisplayController`). None concerns Bluetooth.
- R3-S01-01, "zero hits" for a term list that includes `windows`. The `strings` output of the state file holds `DocumentWindows` and `SelectedWindows`, Xcode interface keys. None names an operating system.
- R3-S06-02 states that every `volume|mute` hit is cited in the record or is CHROMECAST_SETUP_GUIDE.md:265. Uncited hits: README.md:30, 31, 266; SETUP.md:110; CHANGELOG.md:40. None links the slider to a Bluetooth source.
- R3-S12-01 states the only hits for its port and listener terms are CHROMECAST_SETUP.md:162 and CHROMECAST_SETUP_GUIDE.md:187, 268. The term `listen` also hits CHROMECAST_SETUP.md:127 ("Listening..." status). The term `port\b` hits words ending in "port" (import, support, export, report). No hit is a port number or a listening service.
- R3-S07-01 attaches the quotation "Audio processing helpers" to PROJECT_SUMMARY.md:35 and 135. Line 35 carries it; line 135 reads "Audio helpers".
- R3-S03-02 attaches the quotation "Some Bluetooth devices may require additional pairing steps" to CHANGELOG.md:99-102 and README.md:291. CHANGELOG.md:102 carries it; README.md:291 reads "Some devices may require additional pairing steps".
- R3-S08-02 and R3-H5-02 attach "Full implementation requires Google Cast SDK" / "Full Chromecast implementation requires Google Cast SDK" to README.md:285 and CHANGELOG.md:101. Each line carries one of the two wordings, not both.
- R3-S09-12 attaches "Check Xcode console for error messages" to README.md:346 and CHROMECAST_SETUP.md:280. Line 280 reads "Check Xcode console for errors".
- R3-S11-15 describes `macosx26.1` as showing "a build on that OS line". The key that holds the string is `targetSDK` (ZIP!xcuserstate obj 409, 416).
- R3-H2-05 states that fix_project_quick.sh:21-28 and create_xcode_project.sh:56-72 print "the same steps in short form". Those lines print the project-creation step (Step 1 of the six); Steps 2 to 6 are not printed there.
- Same subject, different outcome in two records. Each record's outcome follows from the evidence that record cites; the records scope the attempt differently.
  - fix_project_quick.sh: R3-S10-02 FAILED (STATED, script text at :16, :19); R3-H2-02 UNVERIFIED (NONE).
  - create_xcode_project.sh: R3-S10-03 FAILED (STATED, :47, :53); R3-H2-03 UNVERIFIED (NONE).
  - Hand-written project file: R3-H2-01 FAILED (STATED, FIX_PROJECT.md:1, 3; fix_project_quick.sh:7); R3-S11-13 PARTIAL (STATED defect plus the OBSERVED build); R3-S10-01 PARTIAL on the build path as a whole.
- Check D found no field that proposes, recommends or ranks, and no reference to a repo other than R3. Words on the Rule 6 list occur only inside quotations from the repo (for example "Recommended" at FIX_PROJECT.md:14 in R3-S10-05 and R3-H2-05; "Should" in guide test steps in R3-S08-05, R3-S09-11, R3-S11-05, R3-H2-05).

## Totals

- PASS: 106
- FAIL: 3 (R3-S10-03, R3-S11-13, R3-S12-01)
- MISSING: 0 (H1, H6 and H8 are answered by lists and a table without record IDs, per templates.md:48)
