# R3 verification: records in files 02, 03 and 04

Repo R3, /home/user/AudioRouter, at commit 1bdb71dfb0e7319db0aa2f6cf41aedc853369204 (the only commit; `git log --all`). Working tree clean before and after verification (`git status --porcelain` empty). Files checked, as they stood at verification: /home/user/audio/repos/R3/02-stages-bluetooth.md (modified 2026-11-17 07:56), 03-stages-audio.md (modified 2026-11-17 18:32), 04-history.md (modified 2026-11-17 18:31).

An earlier file stood at this path, dated 2026-11-17 18:30, which is before the last change to files 03 and 04. This file replaces it. Every check below was run afresh against the three files as they now stand; no verdict was carried over.

## Method

- Read in full: the 12 tracked text files (8 `.md`, 3 `.sh`, Package.swift), except that lines 126-595 of auto_create_project.sh (the inside of the project heredoc) were covered by a `diff` of script lines 100-601 against the archive member `project.pbxproj`, which was read in full, and by `grep -n`. Archive members `project.pbxproj`, `AudioRouter.xcscheme`, `contents.xcworkspacedata` and both `WorkspaceSettings.xcsettings` were read with `unzip -p` to standard output. `UserInterfaceState.xcuserstate` was read with `strings` and decoded in memory with Python's plistlib (`python3 -I`), including the nested archive held in object 436. Nothing was extracted to disk, built or run.
- Git commands: `log`, `show --stat`, `status --porcelain`, `branch -a`, `tag`, `stash list`, `ls-files -s`, `rev-parse`, `shortlog`. `git for-each-ref` and `git rev-list --all --count` were each run once; both only read.
- Check A. Each cited location was opened and compared with the fact or quotation the record attaches to it. Every quotation followed by a citation was also matched against the cited line range by script. FAIL A is given where a cited location does not exist, or where the fact attached to a citation is absent from that location or contradicted beside it. Where a location carries the fact in part, or in other words with the same meaning, the difference is listed under Notes and the verdict stands.
- Statements of search results inside finding records ("zero hits", "the only hits are") were re-run. They are not citations in a Rule 2 form. Where a re-run differed and the record's claim still held, the difference is listed under Notes.
- Check B: evidence grades against Rule 3. Check C: outcomes against Rule 4 and the Rule 5 vocabulary, by reading and by script (every WORKED, PARTIAL or FAILED record carries OBSERVED or STATED evidence; every UNVERIFIED record carries NONE). Check D: every occurrence of a Rule 6 word in the three files was listed by `grep` and read in context, and each record was read for proposals, rankings and references to any repo other than R3.
- Check E: one search per NOT FOUND record with terms the auditor did not use, over the 12 text files, `project.pbxproj`, the scheme and the `strings` output of the state file; for H3 and H4, read-only git commands with other arguments. The terms and the result are given on the verdict line.
- Citations to /home/user/audio/00-manifest.md and /home/user/audio/repos/R3/01-map.md inside the records were opened at the cited lines only.
- Rule 11: identifier values seen during verification (developer team identifier at ZIP!pbxproj:256, 341; hardware device identifier at ZIP!xcuserstate obj 397, 400) are not copied here.

## Verdicts: 02-stages-bluetooth.md (17 finding records, 10 NOT FOUND records)

- R3-S01-01 PASS
- R3-S01-02 PASS
- R3-S01-03 PASS
- R3-S01-04 PASS
- R3-S01-05 PASS
- R3-S01-06 PASS
- R3-S02-01 PASS
- R3-S02-02 PASS
- R3-S02-03 PASS (E: `radio|antenna|transceiver|wireless card|bluetooth version|bluetooth [0-9]|built-?in|internal bluetooth|usb bluetooth|external bluetooth|bluetooth hardware|bluetooth module`; two hits, both on microphones, README.md:128 and PROJECT_SUMMARY.md:159)
- R3-S02-04 PASS
- R3-S03-01 PASS
- R3-S03-02 PASS
- R3-S03-03 PASS (E: `bonded|bonding|paired devices|keychain|forget this|erase|reset|clear (the )?(list|pair)|known device|recent device|previously (paired|connected)|list of (paired|devices)|remember`; hits are SETUP.md:71, the list a source device shows, and the substring in "presets" at CHANGELOG.md:117, 128; none on stored pairings)
- R3-S03-04 PASS (E: `another (device|source|phone)|other device|already (connected|paired)|one at a time|only one|busy|both devices|concurrent|at once|kick|take over`; zero hits)
- R3-S03-05 PASS (E: `approv|authoriz|restrict|permit|only allow|lock|passcode|pairing code|block|owner|guest|hidden|visible to`; hits are Cast console approval at CHROMECAST_SETUP.md:147-151 and compiler settings holding "BLOCK"; none on Bluetooth pairing)
- R3-S04-01 PASS
- R3-S04-02 PASS
- R3-S04-03 PASS (E: `bitpool|kbps|bit ?rate|lossless|high-res|hi-fi|fidelity|compress|encod|quality`; the one Bluetooth hit is README.md:292, already cited in the record; the rest are the Chromecast AAC listing, CHANGELOG.md:135, BUILD_AND_DEPLOY.md:465 and XML headers)
- R3-S04-04 PASS
- R3-S04-05 PASS (E: `two (sources|devices|phones)|several (sources|devices)|same time|active (source|device)|current (source|device)|last connected|first connected|handover|hand off|takeover|multiple (source|device|input)`; one hit, PROJECT_SUMMARY.md:5, on input types)
- R3-S05-01 PASS
- R3-S05-02 PASS (E: `AVAudioFormat|Float32|Int16|interleav|sampleRate|channelCount|commonFormat|[0-9] ?k?Hz|[0-9]+-bit|\bmono\b|bit-depth|lpcm|frames? per`; zero hits)
- R3-S05-03 PASS (E: `isPlaying|\bidle\b|suspend|resume|inactive|stream (begins|ends|started|stopped)|audio (starts|stops|begins|ends)|playback (starts|stops)|state change|when (audio|music) (starts|stops)`; zero hits)
- R3-S05-04 PASS (E: `ring buffer|circular|queue|fifo|jitter|starv|stutter|crackl|discontinu|dropped|overflow|underflow|chunk|bufferSize|frameCount|frame length`; zero hits)
- R3-S06-01 PASS (E: `MPRemoteCommand|remote command|media key|play button|rewind|fast.?forward|previous track|\bstop\b|command center|headphone|play/pause|toggle playback`; one hit, the Xcode Play button at SETUP.md:32)
- R3-S06-02 PASS
- R3-S06-03 PASS

## Verdicts: 03-stages-audio.md (59 finding records, 3 NOT FOUND records)

- R3-S07-01 PASS
- R3-S07-02 PASS
- R3-S07-03 PASS
- R3-S07-04 PASS
- R3-S07-05 PASS
- R3-S07-06 PASS (E: `timestamp|hostTime|\blag\b|align|offset|skew|pitch|rate match|varispeed|in sync|out of sync|ppm|crystal|sample clock`; hits are CSS alignment lines at CHROMECAST_SETUP_GUIDE.md:125, 132 and Xcode editor keys in the state file; none on audio clocks)
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
- R3-S10-03 PASS
- R3-S10-04 PASS
- R3-S10-05 PASS
- R3-S10-06 PASS
- R3-S10-07 PASS (E: `SMAppService|LSUIElement|open at login|\blogin\b|background (app|mode|task)|relaunch|keep.?alive|watchdog|automatically (start|launch|restart)|at startup|RunAtLoad|launch at|auto.?launch`; hits are `firebase login` at CHROMECAST_SETUP.md:31-32 only)
- R3-S10-08 PASS
- R3-S10-09 PASS
- R3-S10-10 PASS
- R3-S10-11 PASS
- R3-S10-12 PASS
- R3-S10-13 PASS
- R3-S10-14 PASS
- R3-S10-15 FAIL A: Depends on cites BUILD_AND_DEPLOY.md:287-312 for "Homebrew or the App Store would carry updates if used"; those lines hold a cask listing and App Store review points and say nothing about updates.
- R3-S11-01 PASS
- R3-S11-02 PASS
- R3-S11-03 PASS
- R3-S11-04 PASS
- R3-S11-05 PASS
- R3-S11-06 PASS
- R3-S11-07 PASS (E: `galaxy|ipad|macbook|bose|jbl|crosley|victrola|technica|pro-ject|rega|tried (with|on)|tested (on|with)|confirmed (with|on)|compatible with|model`; hits are "compatible with" macOS 26 at README.md:274 and CHANGELOG.md:108, the substring in "Technical", and the build-machine key `targetDeviceModelCode` in the state file; no source device)
- R3-S11-08 PASS
- R3-S11-09 PASS
- R3-S11-10 PASS
- R3-S11-11 PASS
- R3-S11-12 PASS
- R3-S11-13 PASS
- R3-S11-14 PASS
- R3-S11-15 PASS
- R3-S12-01 PASS
- R3-S12-02 PASS
- R3-S12-03 PASS
- R3-S12-04 PASS
- R3-S12-05 PASS
- R3-S12-06 PASS

## Verdicts: 04-history.md (18 finding records, 2 NOT FOUND records)

- R3-H2-01 FAIL A: Depends on offers `objectVersion = 56` (ZIP!pbxproj:6) as showing Xcode 15; that line names no Xcode version, and ZIP!pbxproj:162 reads `compatibilityVersion = "Xcode 14.0"`.
- R3-H2-02 PASS
- R3-H2-03 PASS
- R3-H2-04 FAIL A: Depends on labels `objectVersion = 56` (auto_create_project.sh:105) an "Xcode 15 project format" setting; that line names no Xcode version, and auto_create_project.sh:252 reads `compatibilityVersion = "Xcode 14.0"`.
- R3-H2-05 PASS
- R3-H2-06 PASS
- R3-H3-01 PASS (E: `git log --all -i -E --grep='undo|rollback|back out|again|hotfix|patch|repair|correct|bug|broken|oops|typo|retry|wip|temp'`, no output; `git log --all --format='%h parents=[%p] %s'`, one root commit; `git log --all --merges`, no output; `git shortlog -s -n --all`, one commit by one author)
- R3-H4-01 PASS (E: `git log --all --not main --oneline`, no output; `git branch -a --no-merged main`, no output; `git rev-parse --verify -q refs/stash`, exit status 1 and no output; `git tag -l`, no output)
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

templates.md:48 sets H1 and H8 as lists and H6 as a table. They carry no record ID, so they have no verdict line and are not counted in the totals. Their citations were checked under check A.

- H1 list (04-history.md:17-30): citations hold. Hash, author name, author and commit date 2025-11-13 19:56:14 +1100, subject and empty body match `git log --all --date=iso`. One commit, no parent. Four refs at the one commit (`git branch -a -v`). /home/user/audio/00-manifest.md:18 and :35 carry the snapshot date and "R3 1". Every reflog entry is dated 2026-11-17 07:19:41 +0000 (`git log -g --all --date=iso`). CHANGELOG.md:8, 179 and PROJECT_SUMMARY.md:391 carry the quoted dates. Archive member timestamps match `unzip -l`.
- H6 tables (04-history.md:196-393): citations hold. The quotations in 170 rows were matched by script against their cited lines; the one mismatch is the PROJECT_SUMMARY.md:80 row, which states that cell separators are omitted. The line lists in the first table match `grep -n`: auto_create_project.sh (12 lines, `BUG`), ZIP!pbxproj (18 lines, case-insensitive) and ZIP!xcscheme (12 lines). `grep -n -E "TODO|FIXME|HACK|XXX|BUG|WORKAROUND"` over the text files outside auto_create_project.sh returns CHROMECAST_SETUP_GUIDE.md:36 only. No quotation exceeds 25 words.
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

All 48 stage questions have at least one record. Of the 8 history questions, H2, H3, H4, H5 and H7 have records; H1, H6 and H8 are answered by lists and tables that carry no record ID, as templates.md:48 lays down. They are flagged in the table so that the reader can see they sit outside the record count. No question is without an answer, so none is marked MISSING.

Record counts found by script: 02 holds 17 finding records and 10 NOT FOUND records; 03 holds 59 and 3; 04 holds 18 and 2. Total 94 finding records and 15 NOT FOUND records, 109 records. No record ID is used twice. Every finding record has the nine template fields in order, and every NOT FOUND record has the three. Each file's own tally agrees with these counts.

## Notes: differences seen that did not change a verdict

Search statements whose re-run differed. In each case the claim the search supports still holds.

- R3-S01-01, "zero hits" over SCOPE-A for a term list that includes `windows`: the `strings` output of the state file, which SCOPE-A includes, gives two hits, `DocumentWindows` and `SelectedWindows`, both Xcode interface keys.
- R3-S02-01, R3-S02-02, R3-S02-03, R3-S02-04, "zero hits" over SCOPE-A for `adapter|controller|chipset|dongle|firmware|hci|powered|rfkill`: the text files, project file and scheme give zero hits; the `strings` output of the state file gives six hits on "Controller", all Xcode interface keys.
- R3-S06-02 states that every `volume|mute` hit is cited in the record or is CHROMECAST_SETUP_GUIDE.md:265. Hits not cited: README.md:30, 31, 266; SETUP.md:110; CHANGELOG.md:40. None links the slider to a Bluetooth source.
- R3-S11-08 states that the heading at PROJECT_SUMMARY.md:294 is the only hit for its term list. The term `range` also hits "Orange" at PROJECT_SUMMARY.md:223-224 and `CLANG_WARN_RANGE_LOOP_ANALYSIS` at auto_create_project.sh:331, 415, 490, 550. None is a measurement.
- R3-S12-01 states that the only hits for its port and listener terms are CHROMECAST_SETUP.md:162 and CHROMECAST_SETUP_GUIDE.md:187, 268. Other hits: `listen` at CHROMECAST_SETUP.md:127 ("Listening..." status); `:[0-9]{2,5}` at PROJECT_SUMMARY.md:280 (a file-and-line reference); `port\b` on 45 lines holding words such as import, support, export and report. No hit is a port number or a listening service.
- R3-S12-03 gives the `helper` hits as PROJECT_SUMMARY.md:34-35. The term also hits PROJECT_SUMMARY.md:135 and CHANGELOG.md:86, both audio utility descriptions.

Quotations attached to two locations where one location carries other wording with the same meaning.

- R3-S03-02: "Some Bluetooth devices may require additional pairing steps" is at CHANGELOG.md:102; README.md:291 reads "Some devices may require additional pairing steps".
- R3-S04-04: "Audio from the device will now stream through AudioRouter" is at README.md:143; SETUP.md:73 reads "Start playing audio - it will stream through AudioRouter!".
- R3-S06-03: "Match history (50 tracks)" and "Artwork download" are at PROJECT_SUMMARY.md:192-193; CHANGELOG.md:29-30 read "Song match history (up to 50 tracks)" and "Metadata display (title, artist, album, artwork)".
- R3-S07-01: "Audio processing helpers" is at PROJECT_SUMMARY.md:35; line 135 reads "Audio helpers".
- R3-S08-02 and R3-H5-02: README.md:285 reads "Full implementation requires Google Cast SDK" and CHANGELOG.md:101 reads "Full Chromecast implementation requires Google Cast SDK". Each record attaches one of the two wordings to both lines.
- R3-S08-04: "Custom channel messaging" and "Metadata and visualizer data streaming" are at README.md:205-206; PROJECT_SUMMARY.md:181, 183 read "Real-time metadata streaming" and "Custom namespace messaging".
- R3-S09-12: "Check Xcode console for error messages" is at README.md:346; CHROMECAST_SETUP.md:280 reads "Check Xcode console for errors".

Statements that the cited locations carry in part.

- R3-S01-02 states that HTML and JavaScript appear only in the receiver page at CHROMECAST_SETUP_GUIDE.md:116-212. Short HTML and JavaScript snippets are also at CHROMECAST_SETUP.md:185, 218-225, 231-238, 244-246.
- R3-S07-03 and R3-S09-01 call the planned items at PROJECT_SUMMARY.md:325-327 "unticked". Those lines are plain bullets under "Version 1.1 (Planned)" with no tick box; the README.md and CHANGELOG.md lines cited beside them carry `[ ]`.
- R3-S11-06 calls `macosx26.1` an "OS string", and R3-S11-15 says it "shows a build on that OS line". The key that holds the value is `targetSDK` (ZIP!xcuserstate obj 409, 416), inside the `ActiveRunDestination` entry (obj 402).
- R3-S11-06 gives the link between the strings `Build` and `Succeeded` as INFERRED from their adjacency. Decoding the state file shows the link directly: the report at obj 418 has title `Build` (obj 423) and its summary segments (obj 426) hold `Build`, a nested archive whose string is `Succeeded` (obj 436) and a date of 2025-11-11 07:46:07 UTC (obj 444).
- R3-S12-05 names "the three scripts and one guide" as holding an absolute path under one user's home directory. FIX_PROJECT.md:10, 40, 153 hold the same path.
- R3-H2-03 describes the body of `create_project.swift` as one comment and one print statement (create_xcode_project.sh:41-49). Lines 42-45 also hold an import and two constants, which R3-H5-04 states.
- R3-H2-05 states that fix_project_quick.sh:21-28 and create_xcode_project.sh:56-72 print "the same steps in short form". Those lines print the project-creation step, the first of the six; the other five steps are not printed there.
- R3-H5-06 attaches "Uncheck \"Include Tests\"" to FIX_PROJECT.md:36 and create_xcode_project.sh:69; the script line uses single quotation marks.

Statements with no citation, in fields where checks A to D found nothing to open.

- R3-S10-05, Open questions: the clause that hand-numbered identifiers are "not what a project made by Xcode's template would hold" has no citation. The identifier citation beside it (ZIP!pbxproj:10) holds.
- R3-S10-10, Depends on: "macOS per-app preference storage, not named in the repo" has no citation and says so.

Form of a field.

- R3-S12-05, Why it turned out that way: the field gives "UNKNOWN." and then an INFERRED cause with reasoning and citations. The citations hold.
- R3-S08-02, R3-S10-02, R3-S10-03, R3-S11-02, R3-S11-06 and R3-S11-09 follow the outcome word with a scope in brackets, for example "FAILED (as an automated repair)". The outcome word in each is from the Rule 5 list.

Same subject, different outcome in two records. Each record's outcome follows from the evidence that record cites; the records scope the attempt differently.

- fix_project_quick.sh: R3-S10-02 FAILED (STATED, the script's own text at :16, :19); R3-H2-02 UNVERIFIED (NONE).
- create_xcode_project.sh: R3-S10-03 FAILED (STATED, :47, :53); R3-H2-03 UNVERIFIED (NONE).
- Hand-written project file: R3-H2-01 FAILED (STATED, FIX_PROJECT.md:1, 3; fix_project_quick.sh:7); R3-S11-13 PARTIAL (STATED defect plus the OBSERVED build); R3-S10-01 PARTIAL on the build path as a whole.
- Format fields of the project file: R3-S11-13 cites `objectVersion = 56` (ZIP!pbxproj:6) together with `compatibilityVersion = "Xcode 14.0"` (ZIP!pbxproj:162); R3-H2-01 and R3-H2-04 cite the first without the second, which is the ground of their FAIL A verdicts above.

Check D.

- No field makes a proposal or a ranking of its own, and no field refers to a repo other than R3. Every Rule 6 word in the three files sits inside a quotation from the repo: "Should" in guide test steps (R3-S08-05, R3-S09-11, R3-S11-05, R3-H2-05), "Recommended" from FIX_PROJECT.md:14 (R3-S10-05, R3-H2-05, H6 table), and "improvements" and "Improve" from CHANGELOG.md:131, 138, 150, 155 (H6 tables). R3-S10-05 reports the author's own ranking of the three options as a quotation of FIX_PROJECT.md:188.

## Totals

- PASS: 106
- FAIL: 3 (R3-S10-15, R3-H2-01, R3-H2-04)
- MISSING: 0 (H1, H6 and H8 are answered by lists and tables without record IDs, per templates.md:48)
