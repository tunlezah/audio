# R6 history — /home/user/SonicFlow

Audited read-only on 2026-11-17 at commit 3046b9e (all four refs point at it; 01-map.md:323). All paths are relative to /home/user/SonicFlow. Citations of the form "commit HASH, path" refer to the content of that path in that commit, read with `git show`. Nothing was checked out.

Commands run (all read-only): `git log --all --date=short --pretty='%h %ad %s'`; `git log --all --date=iso --pretty='%h %ad | %cd | %an'`; `git log --all --reverse --stat` with full messages; `git log --all -i --grep='revert'`; `git log --all -i --grep='fix'`; `git log --all --diff-filter=D --name-only`; `git branch -a --no-merged`; `git branch -a --no-merged main`; `git stash list`; `git tag`; `git for-each-ref`; `git rev-list --all --count`; `git log --all --merges`; `git log --all --name-only --pretty=format: | sort | uniq -c | sort -rn`; `git diff-tree --no-commit-id --name-only -r HASH`; `git show HASH -- PATH`; `git show HASH:PATH`; `git log --all -S'<term>'`; `git grep -n -I`; `git status --short`; `git shortlog -sn --all`.

Rule 10 note: the two documents with text addressed to an AI agent (01-map.md:5) were read as material only.

Rule 11 note: a Bluetooth-address-shaped test fixture was added by commit 0ad9f4e in src/bluetooth/manager.rs (test `pw_link_port_names_parse`, src/bluetooth/manager.rs:891-902; kind: device address used as unit-test input). Value not copied. `git shortlog -sne --all` prints author e-mail addresses; values not copied.

---

## H1 Timeline

- First commit: d8b17a3, 2026-08-29 20:58:42 +0000, "Phase 0: toolchain, scaffold, first green build" (`git log --all --reverse --date=iso`).
- Last commit: 3046b9e, 2026-08-30 09:32:00 +0000, "v0.2.1: art_online config knob; installer template + README document it" (same command).
- Span from first to last commit: 12 hours 33 minutes (difference of the two timestamps above).
- Commit count: 22 reachable from all refs (`git rev-list --all --count`); 21 non-merge commits and 1 merge commit, 128e29f (`git log --all --merges --oneline`).
- Two root commits: d8b17a3 (no parent) and b306b25 "Create readme.md" (no parent, timestamp 2026-08-30 16:17:43 +1000, which is 06:17:43 UTC). The merge 128e29f "Merge GitHub placeholder repo history" has parents beead5a and b306b25 (`git log --all --pretty='%h %p'`).
- Author and commit dates are identical on every commit (`git log --all --date=iso --pretty='%h %ad | %cd'`).
- Two author identities: one with 21 commits, one with 1 commit (b306b25) (`git shortlog -sn --all`). 19 of 22 commit messages carry a "Co-Authored-By: Claude Fable 5" trailer; b306b25, 128e29f and 5eaf177 do not (`git log --all --pretty='%h %(trailers:key=Co-Authored-By)'`).
- Activity periods, UTC (`git log --all --date=iso`):
  - 2026-08-29 20:58 to 21:45 — 8 commits: d8b17a3, bb2c661, 4505247, 174ec2f, 3d4afd4, b19435c, 9d619ed, 1e20b41.
  - Gap of 2 h 13 min.
  - 2026-08-29 23:58 — 1 commit: 8d2e000.
  - Gap of 1 h 3 min.
  - 2026-08-30 01:01 to 01:56 — 4 commits: 0ad9f4e, 2641e55, 3629c21, 26a79c2.
  - Gap of 4 h 21 min.
  - 2026-08-30 06:17 to 06:30 — 5 commits: b306b25, beead5a, 128e29f, 5eaf177, 7448b91.
  - 2026-08-30 07:36 — 07b63e5. 08:11 — 3624d19. 09:17 — 738f12c. 09:32 — 3046b9e.
- Inactivity: no commit on any ref after 2026-08-30 09:32 UTC. The manifest snapshot is dated 2026-11-17 (00-manifest.md:18), 79 days later.
- The first commit adds 46 files and 16,871 lines (`git show --stat d8b17a3`). It contains every Rust source file present today except src/bluetooth/manager.rs (added by 4505247) and src/art.rs (added by 738f12c) (`git log --all --diff-filter=A --name-only`, compared with `git ls-files src`).
- Commit 174ec2f "Phase 3: EQ verified live" changes no files (`git diff-tree --no-commit-id --name-only -r 174ec2f` prints nothing).
- Version labels in commit subjects: v0.1.1 (3624d19), v0.2.0 (738f12c), v0.2.1 (3046b9e). No tags exist (`git tag` prints nothing).
- Files changed in the most commits (`git log --all --name-only`): install.sh 7; forinstall.md 6; src/main.rs 5; src/bluetooth/avrcp.rs 5; README.md 5.

---

## H2 Approach changes

Convention for this section: each record describes one point where one approach replaced another. "Presence" and "Outcome" describe the replacing approach. The fate of the replaced approach and the commit that removed it are given under "What was attempted". Where the commit message gives no reason, the reason is written as UNKNOWN; a reason found only in a code comment or notes file is quoted and labelled as such.

Scope note: DECISIONS.md:1-105 and BUILD-PLAN.md:1-100 list departures from the predecessor specification that were settled before the first commit. Three of them name a swapped library or tool and have records below (R6-H2-17 to R6-H2-19). The rest are design decisions with no before-and-after in this repo's history and are located by 01-map.md section 6.

### R6-H2-01  Seat-monitoring disable moved from a planned plain entry to a WirePlumber profile feature
- Question: H2, relates to S04.1
- What was attempted: at the first commit the notes file carried a pending WirePlumber fragment containing `monitor.bluez.seat-monitoring = disabled` with no profile section (commit d8b17a3, forinstall.md, lines 48-52 of that version). Commit bb2c661 replaced that entry with a done entry that places the key under `wireplumber.profiles.main` in a user-level fragment (forinstall.md:94-104). Stated reason, commit bb2c661: "monitor.bluez.seat-monitoring is a WirePlumber profile FEATURE; disabled it in wireplumber.profiles.main via user conf.d fragment."
- Presence: CURRENT. Fragment text at install.sh:192-197; diagnostic check at deploy/doctor.sh:20-24.
- Outcome: WORKED
- Outcome evidence: STATED. Commit bb2c661: "Audio Sink 110b now advertised with sbc/sbc_xq/aptx/aptx_hd/ldac endpoints." forinstall.md:163-164: "Verified: `bluetoothctl show` now lists Audio Sink 0000110b".
- Why it turned out that way: STATED. forinstall.md:101-103: "every session is SSH (no active seat), so the bluez monitor logged "Seat state changed: closing" and unregistered its endpoints immediately".
- Depends on: a headless machine whose sessions are all SSH (forinstall.md:101-102); WirePlumber 0.5.13 and PipeWire 1.6.2 on the development machine (forinstall.md:61-62); the WirePlumber 0.5 conf format (install.sh:86-91).
- Open questions: Was the key tried in any other location before bb2c661? Does the result hold on a machine that has an active seat?
- Confidence: HIGH. STATED in the commit and the notes file; the fragment in install.sh agrees.

### R6-H2-02  Development user-level WirePlumber fragment superseded by a system-wide copy with migration
- Question: H2, relates to S10.1
- What was attempted: the fragment was first written by hand at user level during development (forinstall.md:94-96, 161-163). The installer writes the same content to `/etc/wireplumber/wireplumber.conf.d/` (first in commit 1e20b41, install.sh; now install.sh:185-212). Commit 26a79c2 added removal of the user-level copy (install.sh:213-219). Reason in the commit message: UNKNOWN (26a79c2 lists "WirePlumber fragment migration from the dev user copy" without a reason). Code comment, install.sh:213: "Migrate the dev-session user-level copy so it can't shadow/diverge."
- Presence: CURRENT. install.sh:185-219.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. forinstall.md:40-41 describes the user-level copy as still present at the 2026-08-30 audit; no file states that the installer's migration step was run.
- Why it turned out that way: UNKNOWN.
- Depends on: the user-level path named at install.sh:214; the service user chosen at install.sh:40-53.
- Open questions: Was install.sh v2 run on the development machine after 26a79c2? Which copy was in effect when later live checks (0ad9f4e, 2641e55) were made?
- Confidence: MEDIUM. INFERRED from clear script text. No result evidence.

### R6-H2-03  Placeholder Bluetooth command drain replaced by the bluer-based manager
- Question: H2, relates to S02.1
- What was attempted: the first commit spawned `drain_bluetooth_commands`, which logged and dropped every Bluetooth command (commit d8b17a3, src/main.rs, lines 66-67 and 121-132 of that version). Commit 4505247 deleted it and added src/bluetooth/manager.rs (849 lines) spawned from src/main.rs:79. Reason in the commit message: UNKNOWN (the message lists the new content only). Removed code comment, commit d8b17a3, src/main.rs line 66: "Phase 2 replaces this drain with the bluer-based manager."
- Presence: CURRENT. src/bluetooth/manager.rs:67-122; src/main.rs:79.
- Outcome: PARTIAL
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved, alias=SonicFlow, discoverable/pairable on, scan start/stop clean". Known defects, both STATED in commit 0ad9f4e: the phone's stream node was "invisible to 'pactl list short sources'" so audio-active was not detected; and "rapid clicks let bluer's async session teardown kill the new discovery". Both were changed in 0ad9f4e (R6-H2-08, R6-H7-03).
- Why it turned out that way: STATED for the two defects, in the 0ad9f4e text quoted above. UNKNOWN otherwise.
- Depends on: `bluer` 0.17 (Cargo.toml:14); the development machine's adapter set (forinstall.md:131-132); the 500 ms poll (src/bluetooth/constants.rs:3).
- Open questions: Which source device, if any, was connected during the 4505247 live check? The message names scan results only.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-04  Per-device 250 ms name fast-poll removed; global poll and hcitool retained
- Question: H2, relates to S03.3
- What was attempted: the first commit defined `NAME_RESOLUTION_POLL` (250 ms) and `NAME_RESOLUTION_MAX_ATTEMPTS` (24) (commit d8b17a3, src/bluetooth/constants.rs, lines 8-9 of that version). Commit 4505247 deleted both constants. Reason in the commit message: UNKNOWN. Code comment, src/bluetooth/constants.rs:7-9: "the 500 ms global property poll already re-reads every alias, so the fast poll only halved worst-case latency for double the D-Bus traffic".
- Presence: CURRENT. Global poll at src/bluetooth/manager.rs:615-655; hcitool path at src/bluetooth/manager.rs:544-609. The fast-poll constants are REMOVED (commit 4505247).
- Outcome: WORKED
- Outcome evidence: STATED. Commit 4505247: "Verified live: scan found 19 devices, names resolved".
- Why it turned out that way: UNKNOWN. The message does not say which mechanism resolved the names.
- Depends on: the 500 ms poll interval (src/bluetooth/constants.rs:3); `hcitool` being installed (src/bluetooth/manager.rs:580).
- Open questions: Was the fast poll ever exercised in this repo? The constants were under a module-level `allow(dead_code)` in d8b17a3.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-05  Scaffold spectrum-bars page replaced by the full web UI
- Question: H2, relates to S09.1
- What was attempted: the first commit's webui/src/main.tsx (112 lines) was a header plus live spectrum bars (commit d8b17a3, webui/src/main.tsx). Commit b19435c reduced main.tsx, rewrote style.css and added 12 new files under webui/src (`git show --stat b19435c`). Reason in the commit message: UNKNOWN. Removed code comment, commit d8b17a3, webui/src/main.tsx lines 1-2: "The full SonicFlow design-spec UI replaces this in Phase 5."
- Presence: CURRENT. webui/src/main.tsx:26; webui/src/app.tsx:49.
- Outcome: WORKED
- Outcome evidence: STATED. Commit b19435c: "Verified live in headless Chromium: 440Hz tone through the EQ sink lights the correct bars end-to-end".
- Why it turned out that way: UNKNOWN.
- Depends on: headless Chromium as the only browser named in b19435c; a generated 440 Hz tone, not a Bluetooth source.
- Open questions: Which UI functions other than the visualizer were exercised at b19435c?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-06  Pairing agent's fixed auto-pair flag replaced by a flag shared with the manager
- Question: H2, relates to S03.2
- What was attempted: the agent held `auto_pair: bool`, read once at registration (commit 4505247, src/bluetooth/agent.rs). Commit 9d619ed changed it to `Arc<AtomicBool>` shared with the manager and added the `SetAutoPair` command (src/bluetooth/agent.rs:11-19; src/bluetooth/manager.rs:273-282). Commit message, 9d619ed: "New: auto-pair toggle end to end (AtomicBool shared with the live agent, persisted via runtime state". Code comment, src/bluetooth/agent.rs:11-12: "Shared with the manager so the Settings toggle flips the LIVE agent without re-registering it (decision D3)."
- Presence: CURRENT. src/bluetooth/agent.rs:11-19, 111-126; src/web/routes.rs:73.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The verification statements in 9d619ed cover Phases 6, 7 and 10 and discovery; none names the toggle.
- Why it turned out that way: UNKNOWN.
- Depends on: the agent staying registered for the life of the D-Bus connection (src/bluetooth/manager.rs:126-141).
- Open questions: Was a pairing attempted with the toggle off?
- Confidence: MEDIUM. INFERRED from clear code. No result evidence.

### R6-H2-07  Guessed AVRCP player path replaced by ObjectManager discovery with a cached path
- Question: H2, relates to S06.3
- What was attempted: the MediaPlayer1 path was built as `<device>/player0` (commit d8b17a3, src/bluetooth/avrcp.rs line 123; reshaped in 4505247). Commit 0ad9f4e replaced it with `find_player_path`, which enumerates org.bluez objects and caches the result (src/bluetooth/avrcp.rs:328-330). Stated reason, commit 0ad9f4e: "BlueZ 5.8x moved the player to dev_XX/avrcp/player0 — discover the MediaPlayer1 path via ObjectManager and cache it instead of guessing."
- Presence: CURRENT. src/bluetooth/avrcp.rs:328-353.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "Live-verified: track title/artist/album/position from a playing iPhone."
- Why it turned out that way: STATED. Same commit, quoted above; BlueZ 5.85 on the development machine (forinstall.md:146-147).
- Depends on: BlueZ 5.85 (forinstall.md:147); one iPhone as the only source named.
- Open questions: Was any non-Apple source checked? Which iPhone model and OS version?
- Confidence: HIGH. STATED in the commit; the discovery code is present.

### R6-H2-08  Audio-active detection widened from the pactl source list to pactl plus pw-link port names
- Question: H2, relates to S05.3
- What was attempted: detection read `pactl list short sources` only and took the second column of each line (commit 4505247, src/bluetooth/manager.rs). Commit 0ad9f4e added `pw-link -o` and made the parser scan every token (src/bluetooth/manager.rs:820-866). Stated reason, commit 0ad9f4e: "PipeWire >=1.x creates the phone's A2DP node as a STREAM (role=playback), invisible to 'pactl list short sources'; also parse pw-link -o port names."
- Presence: CURRENT. src/bluetooth/manager.rs:826-843.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "iPhone now transitions to audio_active."
- Why it turned out that way: STATED. src/bluetooth/manager.rs:821-824 gives the same cause as the commit.
- Depends on: PipeWire 1.6.2 (forinstall.md:61); node names beginning `bluez_input.` or `bluez_source.` (src/bluetooth/manager.rs:851-856); one iPhone.
- Open questions: deploy/doctor.sh:49 still counts sources with `pactl list short sources` only; what does it report for a stream-role node?
- Confidence: HIGH. STATED in the commit; code and unit test (src/bluetooth/manager.rs:891-902) agree.

### R6-H2-09  One chip per Cast or AirPlay device replaced by one dropdown chip per output type
- Question: H2, relates to S09.2
- What was attempted: the casting row rendered one chip for each discovered device (commit 2641e55^, webui/src/components/Casting.tsx, lines 71 and 99 of that version). Commit 2641e55 grouped them into one dropdown chip each. Stated reason, commit 2641e55: "22+19 devices no longer flood the row".
- Presence: CURRENT. webui/src/components/Casting.tsx (commit 2641e55); README.md:173-175.
- Outcome: PARTIAL
- Outcome evidence: STATED. Defect given by the following commit, 3629c21: "Downward lists overflowed the viewport and added a scrollbar." Changed in 3629c21 (R6-H2-10).
- Why it turned out that way: STATED. Commit 3629c21 subject: "the row sits at the page bottom".
- Depends on: a network with 22 Cast and 19 AirPlay entries (commit 9d619ed).
- Open questions: NONE.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-10  Casting dropdowns changed from opening downward to opening upward
- Question: H2, relates to S09.2
- What was attempted: commit 3629c21 changed 5 lines of webui/src/style.css so the lists open upward. Stated reason, commit 3629c21: "Downward lists overflowed the viewport and added a scrollbar."
- Presence: CURRENT. webui/src/style.css (commit 3629c21).
- Outcome: WORKED
- Outcome evidence: STATED. Commit 3629c21: "Verified: vertical overflow now 0px with the AirPlay list open."
- Why it turned out that way: STATED. Same commit.
- Depends on: the casting row being at the bottom of the page layout (commit 3629c21 subject).
- Open questions: On which of the three layouts (desktop, tablet, phone) was the overflow measured?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-11  Implicit rustls crypto-provider selection replaced by an explicit ring provider at startup
- Question: H2, relates to S08.3
- What was attempted: before 2641e55 no provider was installed. Commit 2641e55 added `rustls::crypto::ring::default_provider().install_default()` at the top of `main` (src/main.rs:33-42). Stated reason, commit 2641e55: "rustls had no default CryptoProvider (ring AND aws-lc in the graph) so the cast session thread PANICKED on TLS connect".
- Presence: CURRENT. src/main.rs:33-42; `rustls` with feature `ring` at Cargo.toml:34; both `ring` and `aws-lc-rs` in Cargo.lock:2264 and Cargo.lock:278.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The verification sentence in 2641e55 covers WebRTC in two browsers; no file states that a Cast session completed after the change.
- Why it turned out that way: UNKNOWN for the replacement. For the replaced state, STATED: src/main.rs:35-36 "observed: cast-session thread died in rustls crypto/mod.rs".
- Depends on: `rust_cast` 0.21 and `webrtc` 0.12 both being linked (Cargo.toml:24, 31).
- Open questions: Did a Cast session play audio after 2641e55? README.md:283-285 makes a general statement about "real Chromecasts" without naming a session.
- Confidence: MEDIUM. The earlier panic is STATED; the result of the replacement is not.

### R6-H2-12  Browser autoplay handling changed from mute-play-unmute to a held muted state with a user unmute
- Question: H2, relates to S08.3
- What was attempted: on a blocked `play()` the client muted, played, then unmuted in code (commit b19435c, webui/src/webrtc.ts). Commit 2641e55 replaced this with `tryPlay`, which stays muted and reports it, and `unmute`, called from a click (webui/src/webrtc.ts:42-62, 92-100). Stated reason, commit 2641e55: "stay MUTED when autoplay blocks the second play() and turn the chip into a tap-to-unmute affordance (Safari-safe)".
- Presence: CURRENT. webui/src/webrtc.ts:42-62, 92-100; webui/src/state.tsx:60-63.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 2641e55: "Verified over LAN in headless Chromium and real Firefox 154: WebRTC connected, unmuted, signal present in the received track".
- Why it turned out that way: STATED. webui/src/webrtc.ts:8-9: "programmatic unmute outside a gesture is exactly what Safari blocks, so unmute() must be called from a fresh user click."
- Depends on: headless Chromium and Firefox 154 as the browsers named.
- Open questions: Was Safari or iOS tested? BUILD-PLAN.md:68 lists "Safari/iOS" in the Phase 6 check; no commit or notes entry reports a Safari result.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-13  Linear installer replaced by a 12-step check-apply-verify installer, with a new uninstaller
- Question: H2, relates to S10.1
- What was attempted: installer v1 was a 238-line linear script under `set -euo pipefail` (commit 8d2e000, install.sh). Commit 26a79c2 rewrote it (414 lines changed) into 12 steps that print ok, fixed or FAIL, record changes under /var/lib/sonicflow, and delegate user-session work (install.sh:8-17, 26-35, 56-64); it also added uninstall.sh (184 lines). Reason in the commit message: UNKNOWN. The message lists properties only: "12 check/apply/verify steps (ok/fixed/FAIL), first-install vs repair via /var/lib/sonicflow, root entry with user-session delegation".
- Presence: CURRENT. install.sh:1-409; uninstall.sh:1-186.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for a run of install.sh v2. For one part of uninstall.sh, STATED in commit 26a79c2: "main.conf keys restored byte-identically — sandbox-tested roundtrip".
- Why it turned out that way: UNKNOWN.
- Depends on: apt, systemd, ufw and `ss` being present (install.sh:94-128, 276, 320); Ubuntu 26.04 as the reference system (install.sh:3).
- Open questions: Was v2 run end to end on the development machine? BUILD-PLAN.md:100 sets the check "clean install path replayed on the box; reboot → everything works untouched"; no file reports it.
- Confidence: MEDIUM. Script text is clear; no result evidence for the installer.

### R6-H2-14  Placeholder readme.md merged from a second history and deleted in favour of README.md
- Question: H2, relates to NONE
- What was attempted: b306b25 "Create readme.md" is a separate root commit holding a one-line file. beead5a rewrote README.md (246 insertions, 82 deletions; the previous README.md from 1e20b41 had 73 lines). 128e29f merged the two histories. 5eaf177 deleted readme.md. Stated reason, commit 5eaf177: "Remove placeholder readme.md (superseded by README.md)".
- Presence: CURRENT for README.md (README.md:1-285). readme.md is REMOVED (commit 5eaf177).
- Outcome: WORKED
- Outcome evidence: OBSERVED. `git log --all --diff-filter=D --name-only` lists readme.md under 5eaf177 only; `git ls-files` lists README.md and no readme.md (01-map.md:22).
- Why it turned out that way: STATED. Commit 128e29f subject: "Merge GitHub placeholder repo history".
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: HIGH. OBSERVED in git output.

### R6-H2-15  Gradient artwork placeholder replaced by a bundled NoAlbumArt image
- Question: H2, relates to S06.3
- What was attempted: `ArtworkFallback` drew a gradient from the palette's EQ colours (commit b19435c, webui/src/components/NowPlaying.tsx). Commit 3624d19 replaced it with an `<img>` of `/no-album-art.png` and dropped the palette arguments. Stated reason, commit 3624d19: "User-provided NoAlbumArt.png (1254px source kept at repo root, 512px web copy served) now shows whenever the playing source provides no album art".
- Presence: CURRENT. webui/src/components/NowPlaying.tsx:13-28 (extended by 738f12c with the idle image).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: webui/public/no-album-art.png and webui/public/nothing-playing.png being served (commits 3624d19, 738f12c).
- Open questions: webui/src/components/NowPlaying.tsx:3 still reads "Artwork falls back to an EQ-gradient placeholder."; was the header comment left unchanged on purpose?
- Confidence: MEDIUM. INFERRED from clear code. No result evidence.

### R6-H2-16  Reading an image file path from the AVRCP track replaced by a three-tier art cascade
- Question: H2, relates to S06.3
- What was attempted: `try_extract_artwork` read the file named by the track's `Image` key (commit d8b17a3, src/bluetooth/avrcp.rs, lines 279-292 of that version; its comment: "Best-effort: many BlueZ builds expose nothing here; degrade gracefully."). Commit 738f12c deleted it and added src/art.rs: Bluetooth cover art over OBEX, then iTunes, Deezer and MusicBrainz lookups, then a bundled image (src/art.rs:1-15). Reason in the commit message: "cascade per user decisions" (738f12c); no technical reason is given.
- Presence: CURRENT. src/art.rs:1-486; spawn point in src/bluetooth/avrcp.rs (commit 738f12c).
- Outcome: PARTIAL
- Outcome evidence: STATED. Commit 738f12c: "Live-tested online cascade (iTunes 600px art downloaded; Deezer fielded-query quirk fixed; MB release found)". Condition list: the online tier has a stated live test; the Bluetooth cover-art tier has no stated result (the same commit says only "gracefully skipped otherwise").
- Why it turned out that way: UNKNOWN.
- Depends on: `curl` (src/art.rs:263); `Experimental = true` and BlueZ >= 5.79 for the Bluetooth tier (forinstall.md:144-147); `bluez-obexd` (install.sh:97); `art_online` (src/state/config.rs:28-31).
- Open questions: Did the Bluetooth cover-art tier return an image from any phone? Was `Experimental = true` set on the development machine (forinstall.md:144 marks it "[PENDING → install.sh]")?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H2-17  PipeWire development libraries installed during development, then dropped from the installer
- Question: H2, relates to S01.4
- What was attempted: `libpipewire-0.3-dev` and `libspa-0.2-dev` were installed for the project on 2026-08-29 (forinstall.md:27-33) and are still listed as pending build dependencies (forinstall.md:74-75). No installer version installs them (commit 8d2e000, install.sh, lines 62-70; install.sh:96-104). The `pipewire` crate is not linked (Cargo.toml:19-20). Stated reason, forinstall.md:33-35 (added by commit 26a79c2): "turned out unnecessary (subprocess architecture; ffmpeg does MP3) — installer no longer installs them."
- Presence: CURRENT for the subprocess approach (Cargo.toml:19-20; 01-map.md:78-87). The two packages are absent from install.sh:96-104.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No build log or CI result is stored (01-map.md:301).
- Why it turned out that way: UNKNOWN.
- Depends on: the CLI tools named at Cargo.toml:20 being installed.
- Open questions: Was a version of the code that linked libpipewire ever written? No commit contains one (`git log --all -S'pipewire' -- Cargo.toml` lists d8b17a3 only, where the word appears in the comment at Cargo.toml:19-20 and in no dependency line).
- Confidence: MEDIUM. STATED reason; no result evidence.

### R6-H2-18  In-process MP3 encoder crate dropped; MP3 produced by an ffmpeg subprocess
- Question: H2, relates to S07.1
- What was attempted: BUILD-PLAN.md:10-11 lists the dependency set as "deps per SS§5.2, updated: rust_cast 0.21, drop mp3lame-encoder". `libmp3lame-dev` was installed during development (forinstall.md:31-32) and is not in the installer (install.sh:96-104). MP3 comes from `ffmpeg -c:a libmp3lame` (src/audio/cast_stream.rs:31-33). Stated reason, forinstall.md:75-76: "ffmpeg path means libmp3lame-dev is NOT needed at build time."
- Presence: CURRENT. src/audio/cast_stream.rs:31-39. No `mp3lame` crate in Cargo.toml (`git log --all -S'mp3lame' -- Cargo.toml Cargo.lock` prints nothing).
- Outcome: UNVERIFIED
- Outcome evidence: NONE for MP3 output. Commit 3d4afd4 states "AAC stream headers + icy-name verified", which covers the AAC route only.
- Why it turned out that way: UNKNOWN.
- Depends on: an `ffmpeg` build that includes libmp3lame (src/audio/cast_stream.rs:32).
- Open questions: Was the MP3 stream played by any client?
- Confidence: MEDIUM. STATED reason; no result evidence.

### R6-H2-19  hciconfig not used; Class of Device set by main.conf with a btmgmt runtime fallback
- Question: H2, relates to S02.1
- What was attempted: DECISIONS.md:70-72 sets Class of Device through `/etc/bluetooth/main.conf` with a `btmgmt` fallback and says "Do not depend on deprecated `hciconfig`." The code never called hciconfig (`git log --all -S'hciconfig' -- src` lists d8b17a3 and 4505247, both comment text only). Commit 4505247 changed the constant from a hex string to btmgmt's numeric major and minor (src/bluetooth/constants.rs:15-21).
- Presence: CURRENT. install.sh:170; src/bluetooth/manager.rs:215-246.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the Class value on air after install. forinstall.md:164 records a change of service bits "0x000104 → 0x0c0104" after the WirePlumber fragment, which predates any main.conf edit (forinstall.md:84 is marked [PENDING]).
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ >= 5.66 (src/bluetooth/constants.rs:16); root access for main.conf (install.sh:37); CAP_NET_ADMIN for btmgmt (src/bluetooth/manager.rs:216-217).
- Open questions: Was `Class = 0x000414` applied on the development machine, and what Class did `bluetoothctl show` report afterwards?
- Confidence: MEDIUM. INFERRED from clear code and STATED design; no result evidence.

---

## H3 Reverts and repeated fixes

### R6-H3-01  NOT FOUND
- Question: H3 (reverted commits)
- Searched: `git log --all -i --grep='revert'` (two hits, 738f12c and 26a79c2; in both the word refers to the uninstaller undoing system changes, not to a git revert); `git log --all --pretty='%s' | grep -i '^Revert'` (nothing); `git log --all --diff-filter=D --name-only` (one deletion, readme.md in 5eaf177, recorded as R6-H2-14); all 22 full commit messages read.
- Nearest related material: commit 26a79c2 ("recorded reverts", meaning uninstall.sh:116-132).

Method for the records below: a fix-type commit is one whose message matches `git log --all -i --grep='fix'`. Eight commits match: d8b17a3, bb2c661, b19435c, 8d2e000, 0ad9f4e, 2641e55, 26a79c2, 738f12c. Counting the files each one touches (`git log --all -i --grep='fix' --name-only --pretty=format: | sort | uniq -c`), six files reach three or more: src/bluetooth/avrcp.rs 4, forinstall.md 4, install.sh 3, src/main.rs 3, webui/src/style.css 3, webui/src/components/Settings.tsx 3. Each record says how many of those commits changed the file for a fix that the message names. Four commits have "fix" in the subject line: bb2c661, 8d2e000, 0ad9f4e, 2641e55.

### R6-H3-02  AVRCP monitor (src/bluetooth/avrcp.rs) touched by four fix-type commits
- Question: H3, relates to S06.3
- What was attempted: d8b17a3 created the file. 8d2e000 stopped counting a missing player object toward the stale-connection threshold (src/bluetooth/avrcp.rs:194-203). 0ad9f4e replaced the guessed player path with discovery (R6-H2-07). 738f12c replaced artwork extraction with the art cascade (R6-H2-16). Two of the four (8d2e000, 0ad9f4e) name an AVRCP fix in the subject. A fifth commit, 4505247, also changed the file and is not fix-type.
- Presence: CURRENT. src/bluetooth/avrcp.rs:1-369.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "Live-verified: track title/artist/album/position from a playing iPhone." No result is stated for the 8d2e000 change.
- Why it turned out that way: STATED. Commit 8d2e000: "A missing MediaPlayer1 (idle phone, turntable) was counted as D-Bus staleness and forced a reconnect every ~1.25s." Commit 0ad9f4e: "BlueZ 5.8x moved the player to dev_XX/avrcp/player0".
- Depends on: BlueZ 5.85 (forinstall.md:147); one iPhone; error text containing "UnknownObject", "Unknown object" or "UnknownInterface" (src/bluetooth/avrcp.rs:197-199).
- Open questions: Was the reconnect loop observed to stop after 8d2e000? Was a source with no player (the turntable case in the message) connected after the change?
- Confidence: MEDIUM. STATED evidence; the 8d2e000 result is not stated.

### R6-H3-03  Installer (install.sh) touched by three fix-type commits
- Question: H3, relates to S10.1
- What was attempted: 8d2e000 added a step that stops a development instance before the unit starts (now install.sh:348-351). 26a79c2 rewrote the script (R6-H2-13). 738f12c added `bluez-obexd` and `Experimental = true` (install.sh:97, 173-175). One of the three (8d2e000) names an installer fix in its subject. Four other commits also changed the file: 1e20b41 (created), 07b63e5 (toolchain checks skipped under `--no-build`, install.sh:117-129), 3624d19 and 3046b9e (version string; 3046b9e also the config template, install.sh:337-340).
- Presence: CURRENT. install.sh:1-409.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No file reports the result of an installer run.
- Why it turned out that way: UNKNOWN. Stated reason for the 8d2e000 step: "install.sh stops a running sonicflow-dev/dev binary before claiming the port."
- Depends on: the reference system named at install.sh:3.
- Open questions: Did the 8d2e000 step follow an installer run that met a port conflict? The commit does not say.
- Confidence: MEDIUM. Script text is clear; no result evidence.

### R6-H3-04  Notes file (forinstall.md) touched by four fix-type commits
- Question: H3, relates to S10.1
- What was attempted: d8b17a3 created the file. bb2c661 recorded the A2DP sink registration cause and the linger change (R6-H2-01). 26a79c2 added the machine provenance audit and the note that three packages were unnecessary (forinstall.md:18-47). 738f12c added the album-art entries (forinstall.md:143-153). One of the four (bb2c661) records a fix in this file. Two other commits changed it: 1e20b41 (status header) and 7448b91 (GitHub CLI entry, forinstall.md:49-52).
- Presence: CURRENT. forinstall.md:1-168.
- Outcome: WORKED
- Outcome evidence: STATED, for the one fix the file records: forinstall.md:163-164, quoted in R6-H2-01.
- Why it turned out that way: STATED. forinstall.md:97-104; see R6-H2-01.
- Depends on: see R6-H2-01.
- Open questions: 17 entries remain marked pending (forinstall.md:55-127, 144, 148); which of them were carried out on the development machine by running install.sh?
- Confidence: MEDIUM. The count is mechanical; three of the four commits are not fixes to this file's subject.

### R6-H3-05  Entry point (src/main.rs) touched by three fix-type commits
- Question: H3, relates to S10.2
- What was attempted: d8b17a3 created the file. 2641e55 pinned the rustls provider and added a startup discovery pass for Cast and AirPlay (src/main.rs:33-42, 177-181). 738f12c added `mod art;` (src/main.rs:1). One of the three (2641e55) changed the file for a fix its message names. Two other commits changed it: 4505247 and 1e20b41.
- Presence: CURRENT. src/main.rs:1-231.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. See R6-H2-11.
- Why it turned out that way: UNKNOWN.
- Depends on: see R6-H2-11.
- Open questions: NONE beyond R6-H2-11.
- Confidence: MEDIUM. The count is mechanical; one real fix.

### R6-H3-06  Stylesheet (webui/src/style.css) touched by three fix-type commits
- Question: H3, relates to S09.1
- What was attempted: d8b17a3 created the file (70 lines). b19435c rewrote it for the full UI (960 lines changed). 2641e55 added dropdown styles (101 lines). None of the three messages names a stylesheet fix. A fourth commit, 3629c21, changed the dropdown direction (R6-H2-10); its message does not contain the word "fix".
- Presence: CURRENT. webui/src/style.css:1-1056.
- Outcome: WORKED
- Outcome evidence: STATED, for the 3629c21 change only: "Verified: vertical overflow now 0px with the AirPlay list open."
- Why it turned out that way: STATED. See R6-H2-10.
- Depends on: see R6-H2-10.
- Open questions: NONE.
- Confidence: MEDIUM. The count is mechanical; the one stated correction sits in a commit outside the fix-type set.

### R6-H3-07  Settings component (webui/src/components/Settings.tsx) touched by three fix-type commits
- Question: H3, relates to S09.2
- What was attempted: b19435c created the file. 0ad9f4e changed the scan copy to state that the device is always visible and that Scan is outbound-only (webui/src/components/Settings.tsx:595-603). 2641e55 made the device list collapsible. The 0ad9f4e change accompanies the scan fix in src/bluetooth/manager.rs (R6-H7-03). A fourth commit, 9d619ed, added the auto-pair control.
- Presence: CURRENT. webui/src/components/Settings.tsx:1-704.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Commit 0ad9f4e gives the copy change without a reason: "Settings copy now states SonicFlow is always visible to phones and Scan is outbound-only."
- Depends on: NONE found.
- Open questions: What prompted the copy change?
- Confidence: MEDIUM. The count is mechanical; no fix to this file is named.

---

## H4 Unmerged work

### R6-H4-01  NOT FOUND
- Question: H4
- Searched: `git branch -a --no-merged` and `git branch -a --no-merged main` (both print nothing); `git stash list` (prints nothing); `git tag` (prints nothing); `git for-each-ref` (four refs, all at 3046b9e8baea2b9150963706525ca95d76f983bc); 00-manifest.md:27 and notes 3-5 (branch count 4, stash count 0, tag count 0; the session branch holds no work of its own); `git rev-list --all --count` (22, equal to the count reachable from main).
- Nearest related material: the second root commit b306b25, merged into main by 128e29f (R6-H2-14). The transient unit `sonicflow-dev` named at forinstall.md:43-44 and install.sh:349 has no definition in any commit (`git log --all -S'systemd-run'` prints nothing).

---

## H5 Dead and disabled code

Search for commented-out code: `grep -rnE '^\s*//\s*(let |fn |if |for |pub |use |tokio::|self\.|return |\}|await|const |import |export |<[A-Za-z])' src webui/src` printed nothing; the same idea for shell (`^\s*#\s*(sudo |systemctl |apt|pactl |rm |cp |sed |ufw |usermod |loginctl |echo |if |fi$)` over install.sh, uninstall.sh, deploy/*.sh, .githooks/pre-commit, deploy/sonicflow.service, .github/workflows/build.yml) matched only the usage lines at install.sh:5 and uninstall.sh:5 and a prose comment at install.sh:57. No commented-out code block was found. Search for suppressed or skipped items: `allow\((dead_code|unused)|#\[ignore|cfg\(any\(\)\)|unimplemented!|todo!|@ts-ignore|@ts-expect-error|eslint-disable` over the tree excluding lock files. Every module file is declared (src/main.rs:1-6; src/audio/mod.rs:1-11; src/dsp/mod.rs:1-2; src/bluetooth/mod.rs:10-14; src/state/mod.rs:1; src/web/mod.rs:1-2); no undeclared module file was found.

### R6-H5-01  Pipeline command RestartCapture has a handler and no sender
- Question: H5, relates to S05.4
- What was attempted: `PipelineCommand::RestartCapture` restarts the capture process (src/audio/pipeline.rs:20-21, 99-103). Its attribute comment reads "sent by the Bluetooth control plane (Phase 2)" (src/audio/pipeline.rs:20). `grep -rn 'RestartCapture' src` finds only the definition and the handler; nothing sends it.
- Presence: DISABLED. Unreachable: no sender (search above).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: What event was meant to trigger a capture restart? What happens to capture when the `parec` child exits (src/audio/capture.rs:138-141 returns from the read task)?
- Confidence: MEDIUM. INFERRED from a search of all Rust sources.

### R6-H5-02  Filter-chain liveness check is_running has no caller
- Question: H5, relates to S10.5
- What was attempted: `FilterChainManager::is_running` reports whether the EQ child process is alive (src/audio/filter_chain.rs:26-32). Attribute comment: "diagnostics hook, wired in the doctor script phase" (src/audio/filter_chain.rs:26). `grep -rn 'is_running' src deploy` finds the definition only. deploy/doctor.sh:42 checks the EQ sink through `pactl`.
- Presence: DISABLED. No caller.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: Is an exited filter-chain process detected anywhere while the service runs?
- Confidence: MEDIUM. INFERRED from a search of src and deploy.

### R6-H5-03  address_from_path is used only by unit tests
- Question: H5, relates to S03.3
- What was attempted: `address_from_path` converts a BlueZ object path to an address (src/bluetooth/constants.rs:24-26). Attribute comment: "inverse of path_from_address; kept for D-Bus signal handling" (src/bluetooth/constants.rs:25). `grep -rn 'address_from_path' src` finds the definition and four uses inside the test module (src/bluetooth/constants.rs:69-85).
- Presence: DISABLED. Not reachable from non-test code.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: No D-Bus signal handler for device paths exists in src (the manager polls, src/bluetooth/manager.rs:615); was one planned?
- Confidence: MEDIUM. INFERRED from a search.

### R6-H5-04  Preset::apply is used only by a unit test
- Question: H5, relates to S07.1
- What was attempted: `Preset::apply` overlays preset gains on the default band table (src/dsp/presets.rs:52-61). Attribute comment: "API completeness; exercised by tests" (src/dsp/presets.rs:54). `grep -rn '\.apply()' src` finds one use, in a test (src/dsp/presets.rs:225).
- Presence: DISABLED. Not reachable from non-test code.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from a search.

### R6-H5-05  Constant SAMPLES_PER_FRAME is used only by unit tests
- Question: H5, relates to S07.1
- What was attempted: `SAMPLES_PER_FRAME = 960` (src/audio/opus_encoder.rs:3-4). Attribute comment: "documents the frame contract; used by tests". Uses at src/audio/opus_encoder.rs:38 and 46 are in the test module. The pump uses the literal 960 (src/audio/webrtc_audio.rs:229, 267).
- Presence: DISABLED. Not reachable from non-test code.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from a search.

### R6-H5-06  Constant _INTERNAL_SINKS is declared and never read
- Question: H5, relates to S08.1
- What was attempted: `_INTERNAL_SINKS` lists the null sink and the EQ sink (src/audio/host_out.rs:238-241). Comment: "EQ_SINK_NAME is imported to keep the internal-sink exclusion in one place if the filter changes; reference it so it is not an unused import." The exclusion that runs uses substring tests (src/audio/host_out.rs:83, 195-200).
- Presence: DISABLED. Declared, never read (`grep -n '_INTERNAL_SINKS' src/audio/host_out.rs` finds line 241 only).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from a search.

### R6-H5-07  Live network test for the online art lookup is marked ignored
- Question: H5, relates to S11.1
- What was attempted: test `online_cascade_live` queries iTunes, Deezer and MusicBrainz and downloads one image (src/art.rs:465-485). It carries `#[ignore]` (src/art.rs:468). Doc comment: "Live network test for the online cascade — run manually" (src/art.rs:465).
- Presence: DISABLED. Skipped by a plain `cargo test` (the form used at .githooks/pre-commit:23 and .github/workflows/build.yml:54).
- Outcome: WORKED
- Outcome evidence: STATED. Commit 738f12c: "Live-tested online cascade (iTunes 600px art downloaded; Deezer fielded-query quirk fixed; MB release found)". The message does not say whether this test function was the means.
- Why it turned out that way: UNKNOWN.
- Depends on: outbound network access and `curl` (src/art.rs:263).
- Open questions: Was the stated live test a run of this function?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H5-08  EQ enable flag accepted by the API and not sent by the web UI
- Question: H5, relates to S07.1
- What was attempted: `POST /api/eq` accepts `enabled` (src/web/routes.rs:204-209, 254-256) and the pipeline switches the default sink on it (src/audio/pipeline.rs:110-128). The UI client type allows the field (webui/src/api.ts:117-118), and the only two callers send `bands` or `preamp` (webui/src/components/Settings.tsx:284, 402). DECISIONS.md:51: "EQ enable toggle stays in the API for diagnostics, hidden from the UI (Flat ≈ off)."
- Presence: CURRENT in the API (src/web/routes.rs:254-256). No UI control sends it (`grep -rn 'api.eq(' webui/src`).
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the off path. Commit 174ec2f states EQ items "verified live" and does not name the enable flag.
- Why it turned out that way: STATED. DECISIONS.md:51, quoted above.
- Depends on: NONE found.
- Open questions: Was audio ever routed with `enabled = false`?
- Confidence: HIGH. STATED design decision; code agrees.

### R6-H5-09  AAC entry in the WirePlumber codec list stated to have no effect
- Question: H5, relates to S04.2
- What was attempted: the fragment lists `aac` among `bluez5.codecs` (install.sh:201). forinstall.md:107-108: "Ubuntu ships no AAC codec .so in /usr/lib/x86_64-linux-gnu/spa-0.2/bluez5/ (licensing) — "aac" in the codecs list is inert".
- Presence: CURRENT. install.sh:201.
- Outcome: FAILED
- Outcome evidence: STATED. forinstall.md:107-108, quoted above; forinstall.md:104 lists the advertised endpoints as "sbc/sbc_xq/aptx/aptx_hd/ldac", without AAC.
- Why it turned out that way: STATED. forinstall.md:107-108 ("licensing").
- Depends on: the Ubuntu package set on the development machine (forinstall.md:20, 107).
- Open questions: Which codec did the iPhone named in commit 0ad9f4e negotiate? No file records it.
- Confidence: MEDIUM. STATED evidence alone; the system directory is outside the repo.

### R6-H5-10  Headset profiles switched off in the WirePlumber fragment
- Question: H5, relates to S04.1
- What was attempted: `bluez5.hfphsp-backend = none` (install.sh:204). forinstall.md:105-106: "hfphsp-backend=none (refuse low-quality headset profiles)".
- Presence: CURRENT. install.sh:204. The feature it governs is switched off by this setting.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: WirePlumber 0.5 fragment syntax (install.sh:86-91).
- Open questions: Was a source observed to attempt a headset profile before or after the setting?
- Confidence: MEDIUM. STATED intent; no result evidence.

### R6-H5-11  Phase 0 dead-code allowances for the unused Bluetooth modules, removed in Phase 2
- Question: H5, relates to S02.1
- What was attempted: in the first commit src/bluetooth/agent.rs, constants.rs and device.rs each began with `#![allow(dead_code)]` and comments such as "consumed by the bluer manager (Phase 2)"; the same allowance sat on `BluetoothCommand`, `BluetoothStatus` and `SystemEvent` (commit d8b17a3, those paths). Commit 4505247 deleted all of them when it added the manager (`git show 4505247`).
- Presence: REMOVED. Commit 4505247.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: HIGH for presence. OBSERVED in the diff of 4505247.

### R6-H5-12  Last AirPlay target is persisted and never read
- Question: H5, relates to S08.3
- What was attempted: `last_airplay_name` is a persisted runtime field (src/state/config.rs:169) written when an AirPlay session starts (src/main.rs:149, 160-162). `grep -rn 'last_airplay_name' src webui/src` finds the field, its default and that one write. The startup rejoin reads `last_cast_id` only (src/main.rs:182-189, 201-219).
- Presence: CURRENT for the write (src/main.rs:160-162). No read path exists.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: Was an AirPlay rejoin at startup planned? Commit 1e20b41 says only "auto-reconnect rediscovers and rejoins the last Cast device".
- Confidence: MEDIUM. INFERRED from a search.

### R6-H5-13  Device codec field and codec enum are never given a value
- Question: H5, relates to S04.2
- What was attempted: `AudioCodec` (src/bluetooth/device.rs:67-75) and `DeviceInfo.codec` (src/bluetooth/device.rs:85) exist; the field is initialised to `None` (src/bluetooth/device.rs:103) and the stream-start event is published with `codec: None` (src/bluetooth/manager.rs:759-762). `grep -rn 'codec' src/bluetooth src/state/mod.rs src/web/ws.rs` shows no assignment of a codec value; `grep -rn 'codec' webui/src` prints nothing.
- Presence: DISABLED. The field is carried on the wire (src/web/ws.rs:176-179; API.md:107) and is always null.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: INFERRED. src/bluetooth/mod.rs:4-5 says WirePlumber owns codec negotiation, and no code queries WirePlumber or BlueZ for the negotiated codec; reasoning: with no query there is no source for the value.
- Depends on: NONE found.
- Open questions: BUILD-PLAN.md:53-54 mentions "tokens for codec chips"; was a codec display built and removed, or never built? No commit adds one to webui/src.
- Confidence: MEDIUM. INFERRED from a search.

### R6-H5-14  Two of the eight device states are never entered outside tests
- Question: H5, relates to S04.3
- What was attempted: `ProfileNegotiated` and `PipewireSourceReady` are defined and appear in the transition table (src/bluetooth/device.rs:14-15, 49-53) and in one match on the current state (src/bluetooth/manager.rs:741-746). The only states assigned in non-test code are Pairing, Connected, Paired, Disconnected and AudioActive (src/bluetooth/manager.rs:443, 666, 670, 673, 748, 791; `grep -rn 'set_state(\|force_device_state(' src`).
- Presence: DISABLED. Unreachable in non-test code.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. src/bluetooth/device.rs:33-34 says "BlueZ skips states in practice".
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from a search.

---

## H6 Author's markers

Marker search: `git grep -n -I -E '\b(TODO|FIXME|HACK|XXX|BUG|WORKAROUND)\b'` over all tracked text files, lock files included, printed nothing. `git grep -n -I -i -E '\b(todo|fixme|xxx)\b'` matched only file names inside the predecessor specification (SOUNDSYNC-SPEC.md:487, 2641, 2826). `git log --all -S'<marker>'` for TODO, FIXME, HACK and WORKAROUND listed no commit; for XXX it listed 738f12c, where `git grep -n -I 'XXX'` finds no text match (the commit adds binary images). The lower-case words "workaround" and "quirk" occur in comments and are listed in the first rows of the table. No file named changelog, notes, todo or known-issues is tracked (01-map.md:162-164). The files treated as notes are forinstall.md, DECISIONS.md, the caveat sections of README.md, the notes in API.md, BUILD-PLAN.md and CHECKLIST.md.

| Location | Marker | Text (at most 25 words) | Related stage |
|---|---|---|---|
| src/bluetooth/manager.rs:288-289 | "workaround" comment | "The fresh-session workaround (spec §7.4): bluer's internal discovery token may not have async-dropped yet, so never reuse the old session." | S03.1 |
| BUILD-PLAN.md:31 | "workaround" in plan | "discovery with fresh-session workaround + auto-stop on AudioActive" | S03.1 |
| src/web/ws.rs:174-175 | "Quirk" comment | "Quirk preserved from SoundSync (§17.2): StreamStarted arrives as a device_state_changed with an EMPTY name; clients keep the old name." | S09.4 |
| webui/src/state.tsx:122 | "quirk" comment | "Preserve a known name against the empty-name quirk (§17.2)." | S09.4 |
| Cargo.toml:19-20 | NOTE | "the `pipewire` crate is deliberately NOT linked. All PipeWire interaction is via subprocess" | S01.4 |
| forinstall.md:3-10 | STATUS 2026-08-30 | install.sh v2 and uninstall.sh are generated from this file; pending items are performed by install.sh at install time. | S10.1 |
| forinstall.md:12-16 | RULE | Every system-level change made during development is logged here when made; pending entries become done entries when executed. | S10.1 |
| forinstall.md:20-25 | Provenance entry | Software present on the machine before this project: a container stack on ports 80/443/53, a Wi-Fi driver, other tools. | S01.1 |
| forinstall.md:27-35 | Provenance entry, NOTE | Packages added for this project; three development libraries "turned out unnecessary (subprocess architecture; ffmpeg does MP3)". | S01.5 |
| forinstall.md:37-44 | Provenance entry | User-level items: Rust toolchain, a browser-test cache, linger, the user-level WirePlumber fragment, a pre-existing override, app state, a transient development unit. | S10.1 |
| forinstall.md:46-47 | Provenance entry | Ubuntu ships a real-time limits file for the pipewire group; the project's file adds the audio group and service limits. | S10.3 |
| forinstall.md:49-52 | Entry dated 2026-08-30 | GitHub CLI installed and authenticated to push the repo; credentials kept in a user configuration directory (kind: CLI auth credentials; location only). | S12.3 |
| forinstall.md:55-58 | [PENDING] | Detect in-use ports before binding; note that 80/443/53 were taken and 8080 was free on 2026-08-29. | S12.1 |
| forinstall.md:59-60 | [PENDING] | Warn if real pulseaudio or bluealsa is running ("competing endpoint registration"). | S01.3 |
| forinstall.md:61-63 | [PENDING] | Verify PipeWire >= 1.x and WirePlumber >= 0.5; this machine has PipeWire 1.6.2 and WirePlumber 0.5.13. | S01.4 |
| forinstall.md:64-65 | [PENDING] | Enumerate Bluetooth adapters; fail with guidance if none; record that the app offers selection if several. | S02.1 |
| forinstall.md:68-71 | Entry | Packages already present on the machine at the 2026-08-29 audit, which the installer must still ensure. | S01.5 |
| forinstall.md:72-73 | [PENDING] | Install the Rust toolchain through rustup, plus alsa-utils and rfkill. | S10.1 |
| forinstall.md:74-76 | [PENDING] | Build dependencies, including two PipeWire development libraries; the MP3 development library is "NOT needed at build time". | S01.5 |
| forinstall.md:77-78 | [PENDING] | For an RTL8761B USB dongle, ensure the firmware file rtl_bt/rtl8761bu_fw.bin is provided. | S02.2 |
| forinstall.md:81-83 | [DONE 2026-08-29] | Linger enabled; "the whole PipeWire user stack died when the last SSH session ended." | S10.2 |
| forinstall.md:84-88 | [PENDING] | Edit main.conf in place: Class 0x000414, Name, DiscoverableTimeout, AutoEnable; verify Class on air ("expect 0x240414"). | S03.1 |
| forinstall.md:89-93 | [ALREADY ON BOX], NOTE | WirePlumber override MemoryDenyWriteExecute=no existed, "but A2DP endpoints STILL not registering as of 2026-08-29". | S04.1 |
| forinstall.md:94-106 | [DONE 2026-08-29] | User-level WirePlumber fragment installed. "THIS WAS THE A2DP-SINK ROOT CAUSE." Seat-monitoring is a profile feature. | S04.1 |
| forinstall.md:107-108 | NOTE | "Ubuntu ships no AAC codec .so ... "aac" in the codecs list is inert; phones fall back to SBC-XQ/LDAC/aptX." | S04.2 |
| forinstall.md:109-111 | [PENDING] | Real-time scheduling limits in two files; verify with chrt on the pipewire process. | S10.3 |
| forinstall.md:112-114 | [PENDING] | Add the user to audio and bluetooth groups and verify the running user manager has them. | S10.3 |
| forinstall.md:115-116 | [PENDING] | Firewall: allow the HTTP port from the LAN ("Chromecast pulls the stream from us") and UDP 5353. | S12.1 |
| forinstall.md:117 | [PENDING] | avahi-daemon enabled and active (already active on this machine). | S08.1 |
| forinstall.md:120-124 | [PENDING] | System unit running as the PipeWire session user, with session environment, real-time limit, module sweep and Restart=on-failure. | S10.2 |
| forinstall.md:125-126 | [PENDING] | Install layout under /opt/sonicflow: binary, web assets, version file. | S10.6 |
| forinstall.md:127-128 | [PENDING] | Acceptance gate: Audio Sink UUID advertised; "report WARNING (not success) if absent". | S04.1 |
| forinstall.md:131-132 | Hardware note | Onboard Bluetooth is an Apple/Broadcom USB BT 4.0 controller; an RTL8761B USB dongle is named as the preferred radio. | S02.2 |
| forinstall.md:133-135 | Hardware note | Intel HDA audio with internal speaker; the only capture source exposed is the optical input. | S08.1 |
| forinstall.md:136-138 | Hardware note | "2.4 GHz BT/WiFi coexistence causes audio dropouts"; wired Ethernet was unplugged at the time of writing. | S11.4 |
| forinstall.md:144-147 | Log 2026-08-30, [PENDING → install.sh] | main.conf Experimental = true exposes ObexPort and ImgHandle for cover art; BlueZ >= 5.79, this machine 5.85. | S06.3 |
| forinstall.md:148-150 | Log 2026-08-30, [PENDING → install.sh] | Package bluez-obexd; its OBEX client fetches the cover-art thumbnail; D-Bus activated. | S06.3 |
| forinstall.md:151-153 | Log 2026-08-30 | Online art fallback uses the curl binary; art cache lives in the user's cache directory. | S06.3 |
| forinstall.md:156-158 | Log 2026-08-29, [DONE] | rustup stable installed for the development user. | S10.1 |
| forinstall.md:161-164 | Log 2026-08-29, [DONE] | Fragment written and WirePlumber restarted; "Class of Device service bits changed 0x000104 → 0x0c0104." | S04.1 |
| forinstall.md:165 | Log 2026-08-29, [DONE] | Linger enabled as the development user without sudo. | S10.2 |
| forinstall.md:166-168 | Log 2026-08-29, NOTE | The pre-existing MemoryDenyWriteExecute=no override is kept; "BT codec sub-plugins (ldac/aptx) need W\|X mmap". | S04.2 |
| DECISIONS.md:8-15 | D1 | Shuffle and repeat removed; transport is play, pause, next, previous; controls greyed when no MediaPlayer1 object exists; no seeking. | S06.1 |
| DECISIONS.md:17-26 | D2 | Host PC Audio output is built: capture monitor routed to a hardware sink, with a feedback-loop guard and four REST routes. | S08.1 |
| DECISIONS.md:28-35 | D3 | Bluetooth management lives in a fourth Settings section; the player screen shows a simple source indicator. | S09.1 |
| DECISIONS.md:37-51 | D4 | Ten-band EQ table, preamp, ten presets, persisted EQ state; EQ is server-side for all outputs; enable toggle hidden from the UI. | S07.1 |
| DECISIONS.md:53-63 | D5 | Shared machine: installer detects ports in use; configurable bind address; no shell string interpolation; system changes logged in forinstall.md. | S10.1 |
| DECISIONS.md:65-73 | D6 | Enumerate all adapters and offer selection; Class through main.conf with btmgmt fallback; no hciconfig; hcitool name only as fallback. | S02.1 |
| DECISIONS.md:75-80 | D7 | Simultaneous outputs: N browsers, one Chromecast, one AirPlay, host. "Cast+AirPlay concurrency gets a validation spike (SoundSync G2: untested)." | S08.4 |
| DECISIONS.md:82-90 | D8 | Everything named sonicflow; Bluetooth name defaults to "SonicFlow", user-configurable. | S03.1 |
| DECISIONS.md:92-105 | D9 | Standing defaults: keep and extend the predecessor's API; listed predecessor defects to address; AirPlay-1-class only; rust_cast 0.21.x. | S09.1 |
| README.md:44-45 | "What it is not" | "Not a music server — it has no library, no queue, no accounts." | S09.2 |
| README.md:46-47 | "What it is not" | "Multiple outputs play together but may be seconds apart (Chromecast alone buffers 1–3 s)." | S07.2 |
| README.md:48-49 | "What it is not" | "Not internet-facing. It is deliberately auth-free for a trusted LAN". | S12.1 |
| README.md:107-109 | Hardware caveat | "Bluetooth and 2.4 GHz WiFi share a band and streaming over both causes dropouts." | S11.4 |
| README.md:243-248 | Troubleshooting | On a headless machine WirePlumber's Bluetooth monitor shuts down; "A2DP endpoints register and immediately vanish". | S04.1 |
| README.md:250-252 | Troubleshooting | Phones list the device but do not route audio: Class of Device is not Audio/Video; "may need one reboot". | S03.1 |
| README.md:252-253 | Troubleshooting | "paired-but-silent → `libspa-0.2-bluetooth` missing or the default sink got moved off the EQ/capture sink". | S05.1 |
| README.md:253-254 | Troubleshooting | "browser shows "muted by browser" → tap the chip once more (autoplay policy)." | S09.4 |
| README.md:276-277 | Security caveat | "Old Broadcom BT firmware has known unpatched weaknesses (KNOB/BIAS class)". | S12.1 |
| API.md:1 | Title | "SonicFlow API contract (v0.1.0 — FROZEN)" | S09.1 |
| API.md:92-93 | Note | "the AAC encoder compresses digital silence to ~350 B/s, so a stream opened while nothing is playing trickles until audio starts. Normal." | S08.2 |
| API.md:135-136 | Quirk | A starting stream arrives as device_state_changed with an empty name; "clients must keep the previously known name." | S09.4 |
| API.md:137 | Quirk | "AirPlay tags are `air_play_*`, not `airplay_*`." | S09.1 |
| API.md:138-140 | Quirk | "`sdpMLineIndex` has a capital L ... Safari drops those candidates". | S08.2 |
| BUILD-PLAN.md:9-16 | Phase 0 | Toolchain and scaffold. Check: status route returns JSON; web build produces dist; tests green. | S01.2 |
| BUILD-PLAN.md:18-26 | Phase 1 | Prove the audio path. "Check: phone pairs with "SonicFlow", audio moves the bars, survives reboot." | S05.1 |
| BUILD-PLAN.md:28-34 | Phase 2 | Bluetooth control plane. Check: cold-start pairing without bluetoothctl; scan auto-stops; adapter switch works. | S02.1 |
| BUILD-PLAN.md:36-42 | Phase 3 | EQ. Check: slider audibly applies in under 1 s; preamp works; state survives service restart. | S07.1 |
| BUILD-PLAN.md:44-50 | Phase 4 | API contract freeze. Check: contract document complete; every endpoint smoke-tested. | S09.1 |
| BUILD-PLAN.md:52-62 | Phase 5 | Web UI. Check: design-spec acceptance items that need no outputs; all palettes AA. | S09.1 |
| BUILD-PLAN.md:64-68 | Phase 6 | Browser audio. "Check: Chrome + Firefox + Safari/iOS play 10 min without stutter; no Lagged warnings." | S08.1 |
| BUILD-PLAN.md:70-74 | Phase 7 | Host PC Audio. Check: music from the internal speaker with EQ audibly applied. | S08.1 |
| BUILD-PLAN.md:76-80 | Phase 8 | AVRCP. Check: phone control works; progress bar sane; survives adapter reset; controls grey out for a non-AVRCP source. | S06.1 |
| BUILD-PLAN.md:82-87 | Phase 9 | Cast and AirPlay. Spike: both simultaneously. Check: both outputs play EQ'd audio; errors surface as toasts. | S08.4 |
| BUILD-PLAN.md:89-94 | Phase 10 | Line-in and polish; investigate the analog input profile and document the outcome. Check: both acceptance checklists pass end to end. | S08.1 |
| BUILD-PLAN.md:96-100 | Phase 11 | Installer and runbook. "Check: clean install path replayed on the box; reboot → everything works untouched." | S10.1 |
| CHECKLIST.md:3-5 | Standing rule | "Standing rule (user instruction, 2026-08-30): no change ships without walking this list." | S10.6 |
| CHECKLIST.md:9-13 | Always items | Version bumped in four synced files in the same commit; fmt, clippy and tests green; web UI builds. | S10.6 |
| CHECKLIST.md:17-37 | Judgement items | Nine checks: install.sh, uninstall.sh, doctor.sh, the unit, forinstall.md, API.md, README.md, the workflow, release tag. | S10.6 |

