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

---

## H7 Workarounds

Scope: sleeps, retries, delays, version conditions with comments, tolerated error strings and special cases for a named device, browser, library or service. Each record gives the stated problem where one is written down. Two workarounds are recorded under H2 and not repeated here: the seat-monitoring profile feature (R6-H2-01) and the explicit rustls provider (R6-H2-11). Timing values that carry no stated problem are listed after the records.

### R6-H7-01  Hard 5 s timeout around the btmgmt Class of Device call
- Question: H7, relates to S02.1
- What was attempted: `btmgmt --index N class 4 5` is run under a 5 s timeout with kill-on-drop (src/bluetooth/manager.rs:218-246). Problem stated, src/bluetooth/manager.rs:220: "btmgmt HANGS (not errors) without CAP_NET_ADMIN — hard timeout." Commit 4505247 gives the same reason.
- Presence: CURRENT. src/bluetooth/manager.rs:221-234.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The log text at src/bluetooth/manager.rs:242-244 calls a timeout "expected without CAP_NET_ADMIN".
- Why it turned out that way: UNKNOWN.
- Depends on: the service running without CAP_NET_ADMIN (deploy/sonicflow.service:12-14 sets a non-root user); main.conf Class being set by the installer (install.sh:170).
- Open questions: Did the btmgmt call ever succeed on the development machine?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-02  New bluer session for every scan, with a wait for BlueZ to report discovery off
- Question: H7, relates to S03.1
- What was attempted: each scan creates a fresh `bluer::Session`, waits up to 20 x 100 ms for `is_discovering` to turn false, and hands session and adapter to a forwarder task (src/bluetooth/manager.rs:298-333). Problem stated, src/bluetooth/manager.rs:288-289: "bluer's internal discovery token may not have async-dropped yet, so never reuse the old session."
- Presence: CURRENT. src/bluetooth/manager.rs:288-344.
- Outcome: PARTIAL
- Outcome evidence: STATED. Commit 4505247: "scan start/stop clean". Known defect, src/bluetooth/manager.rs:293: "observed live: 5 rapid clicks → status "scanning", BlueZ idle"; addressed by R6-H7-03.
- Why it turned out that way: STATED. src/bluetooth/manager.rs:291-293: "the dropped session's async teardown sends a StopDiscovery that kills the new one".
- Depends on: `bluer` 0.17 (Cargo.toml:14).
- Open questions: NONE beyond R6-H7-03.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-03  StartScan ignored while a scan is running; status reset when the discovery stream ends
- Question: H7, relates to S03.1
- What was attempted: commit 0ad9f4e made `start_discovery` return early when a scan is active (src/bluetooth/manager.rs:294-297) and set status to Ready when the discovery stream ends (src/bluetooth/manager.rs:98-104). Stated reason, commit 0ad9f4e: "rapid clicks let bluer's async session teardown kill the new discovery".
- Presence: CURRENT. src/bluetooth/manager.rs:294-297, 98-104.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The commit states the defect and the change, not a re-test.
- Why it turned out that way: UNKNOWN.
- Depends on: the UI spinner following the Bluetooth status (src/bluetooth/manager.rs:101-102).
- Open questions: Was the five-click case repeated after the change?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-04  StopDiscovery sent as a raw D-Bus call, bypassing bluer
- Question: H7, relates to S03.1
- What was attempted: `stop_discovery` drops the discovery handle and calls `org.bluez.Adapter1.StopDiscovery` directly over zbus (src/bluetooth/manager.rs:346-360). Comment, src/bluetooth/manager.rs:346: "Stop via raw D-Bus, bypassing bluer's client-side tracking entirely."
- Presence: CURRENT. src/bluetooth/manager.rs:346-360.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 4505247: "raw D-Bus StopDiscovery, auto-stop on AudioActive" and "scan start/stop clean".
- Why it turned out that way: UNKNOWN. The problem with bluer's own stop is not described.
- Depends on: a system D-Bus connection being held (src/bluetooth/manager.rs:349).
- Open questions: What did bluer's own stop do that led to the bypass?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-05  Active name requests through hcitool, three at a time, 7 s timeout each
- Question: H7, relates to S03.3
- What was attempted: for devices whose name is empty or address-shaped, `hcitool -i <adapter> name <address>` is run under a 3-permit semaphore and a 7 s timeout (src/bluetooth/manager.rs:544-584; src/bluetooth/constants.rs:10-11). Problem stated, src/bluetooth/manager.rs:545: "D-Bus offers no "fetch now" call."
- Presence: CURRENT. src/bluetooth/manager.rs:544-609.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for this mechanism alone. Commit 4505247 says "names resolved" without saying which mechanism resolved them.
- Why it turned out that way: UNKNOWN.
- Depends on: `hcitool` being installed; DECISIONS.md:73 says "only as a fallback while Ubuntu ships it".
- Open questions: Does `hcitool name` work for the service user without extra capabilities?
- Confidence: MEDIUM. STATED problem; no result evidence for this path.

### R6-H7-06  Alias failure logged at error level because of a turntable incident
- Question: H7, relates to S03.1
- What was attempted: a failed `set_alias` is logged with `error!` (src/bluetooth/manager.rs:185-189). Comment, src/bluetooth/manager.rs:186-187: "a silent alias failure once had a turntable rejecting the adapter because it advertised the hostname."
- Presence: CURRENT. src/bluetooth/manager.rs:185-189.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. The comment does not say whether the incident occurred with this repo's code or with the predecessor.
- Depends on: NONE found.
- Open questions: Which turntable, and in which project did the incident occur?
- Confidence: LOW. One comment, no other material.

### R6-H7-07  Default sink set again each time a Bluetooth stream starts
- Question: H7, relates to S05.1
- What was attempted: on stream start the manager sets the default sink to the EQ sink or the null sink again (src/bluetooth/manager.rs:763-771). Comment, src/bluetooth/manager.rs:763-764: "Insurance: something may have stolen the default sink since startup; audio must land in the EQ/capture chain."
- Presence: CURRENT. src/bluetooth/manager.rs:763-771.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the Bluetooth node following the default sink; no code in the repo links the two (01-map.md:108).
- Open questions: Was the default sink ever observed to move during a run? Does a node that is already playing follow a default-sink change made after it started?
- Confidence: MEDIUM. INFERRED from clear code. No result evidence.

### R6-H7-08  Device state transitions outside the table are applied, not rejected
- Question: H7, relates to S04.3
- What was attempted: `force_device_state` applies any requested state (src/bluetooth/manager.rs:412-420). Comment, src/bluetooth/device.rs:33-35: "BlueZ skips states in practice, so callers force invalid transitions with a loud log rather than rejecting them".
- Presence: CURRENT. src/bluetooth/device.rs:33-56; src/bluetooth/manager.rs:412-429.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-09  Name updates reject empty and address-shaped values
- Question: H7, relates to S03.3
- What was attempted: `update_name` ignores a candidate that is empty or shaped like an address (src/bluetooth/device.rs:126-136). Comment, src/bluetooth/device.rs:127-128: "so a transient bad read never regresses a resolved name back to a MAC."
- Presence: CURRENT. src/bluetooth/device.rs:126-136; called at src/bluetooth/manager.rs:655.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 4505247 lists "name resolution guard-railed" as built.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-10  Missing-player errors do not count toward the AVRCP stale-connection threshold
- Question: H7, relates to S06.3
- What was attempted: commit 8d2e000 resets the failure counter when the error text contains "UnknownObject", "Unknown object" or "UnknownInterface" (src/bluetooth/avrcp.rs:194-203). Stated reason, commit 8d2e000: "A missing MediaPlayer1 (idle phone, turntable) was counted as D-Bus staleness and forced a reconnect every ~1.25s."
- Presence: CURRENT. src/bluetooth/avrcp.rs:194-203.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No re-test is stated.
- Why it turned out that way: UNKNOWN.
- Depends on: the exact error strings returned by zbus and BlueZ 5.85 (src/bluetooth/avrcp.rs:197-199).
- Open questions: Do the three strings cover the error returned when `find_player_path` yields no path (src/bluetooth/avrcp.rs:136-139 returns None before any call)?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-11  AVRCP D-Bus connection rebuilt after five consecutive failures
- Question: H7, relates to S02.3
- What was attempted: after `DBUS_RECONNECT_THRESHOLD` (5) consecutive poll failures the cached connection is dropped (src/bluetooth/avrcp.rs:211-220; src/bluetooth/constants.rs:22). Problem stated, src/bluetooth/avrcp.rs:211-212: "Long-lived D-Bus connections go stale after adapter resets / suspend; reconnect past the threshold."
- Presence: CURRENT. src/bluetooth/avrcp.rs:204-220.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:79 lists "survives adapter reset" as a Phase 8 check; no file reports it.
- Why it turned out that way: UNKNOWN.
- Depends on: poll intervals of 250 ms and 2000 ms (src/bluetooth/constants.rs:4-5).
- Open questions: Was an adapter reset or suspend tried with this repo's code?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-12  AVRCP position read as milliseconds and clamped to the track duration
- Question: H7, relates to S06.3
- What was attempted: `Position` is treated as milliseconds and limited to `Duration` when the duration is known (src/bluetooth/avrcp.rs:268-272). Comment, src/bluetooth/avrcp.rs:28-29: "BlueZ documents MediaPlayer1.Position in MILLISECONDS (spec defect D7: the original code commented "microseconds" — the comment was wrong)."
- Presence: CURRENT. src/bluetooth/avrcp.rs:28-31, 266-272.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 0ad9f4e: "Live-verified: track title/artist/album/position from a playing iPhone."
- Why it turned out that way: UNKNOWN.
- Depends on: one iPhone; BlueZ 5.85 (forinstall.md:147).
- Open questions: BUILD-PLAN.md:78 asks to "validate the Position ms/µs defect (SS D7) against a real phone"; does the 0ad9f4e statement include a check of the unit?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-13  500 ms wait between starting the filter-chain and switching the default sink
- Question: H7, relates to S07.1
- What was attempted: after the EQ process is spawned the code sleeps 500 ms and then sets the default sink (src/audio/pipeline.rs:67-71, 112-115). Comment, src/audio/pipeline.rs:69: "Let the filter-chain register its nodes before routing."
- Presence: CURRENT. src/audio/pipeline.rs:67-71, 112-115.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the wait itself. Commit 174ec2f states EQ changes were "verified live".
- Why it turned out that way: UNKNOWN.
- Depends on: the EQ process registering its sink within 500 ms; the process is killed and respawned on every EQ change (README.md:84-86).
- Open questions: Where does audio go during the respawn window on each EQ change?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-14  Null-sink creation retried up to ten times with backoff, and checked by listing
- Question: H7, relates to S05.1
- What was attempted: `pactl load-module module-null-sink` is tried up to 10 times with waits of 1 to 5 s, and counts only if the sink then appears in a listing (src/audio/pipeline.rs:140-169). Comments, src/audio/pipeline.rs:140 and 156: "pipewire-pulse may not be ready right after boot: retry w/ backoff." and "a successful exit code is not sufficient evidence."
- Presence: CURRENT. src/audio/pipeline.rs:140-169.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. BUILD-PLAN.md:25 lists "survives reboot" as a check; no file reports a reboot test.
- Why it turned out that way: UNKNOWN.
- Depends on: `pactl` being present (src/audio/pipeline.rs:133).
- Open questions: Was the service started at boot on the development machine?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-15  Audio-service readiness probe with a 30 s deadline
- Question: H7, relates to S10.2
- What was attempted: startup polls `pactl info` (or `pw-cli info 0`) once a second for up to 30 s (src/audio/pipeline.rs:212-241). Comment, src/audio/pipeline.rs:212: "systemd After= guarantees start ordering, not readiness. Probe explicitly."
- Presence: CURRENT. src/audio/pipeline.rs:212-241; on failure the code proceeds (src/audio/pipeline.rs:54-56).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the user-session PipeWire being reachable through the environment at deploy/sonicflow.service:16-18; the unit orders after bluetooth and avahi only (deploy/sonicflow.service:6).
- Open questions: The unit has no ordering against the user's PipeWire service; how long did readiness take at boot?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-16  Existing null sink from a previous run is adopted, not duplicated
- Question: H7, relates to S10.2
- What was attempted: if a `sonicflow-capture` null-sink module already exists it is reused (src/audio/pipeline.rs:134-139). Comment, src/audio/pipeline.rs:134: "Adopt an existing sink from a previous run instead of duplicating."
- Presence: CURRENT. src/audio/pipeline.rs:134-139.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: a leftover module being present at start.
- Open questions: Under the system unit the sweep at deploy/sonicflow.service:24 unloads such modules before start; when does the adoption branch run?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-17  Module sweep before every service start
- Question: H7, relates to S10.2
- What was attempted: `ExecStartPre` runs a script that unloads null-sink and loopback modules whose arguments mention "sonicflow" (deploy/sonicflow.service:23-24; deploy/sweep-modules.sh:5-14). Problem stated, deploy/sweep-modules.sh:2-3: "they accumulate across crashes".
- Presence: CURRENT. deploy/sonicflow.service:24; installed by install.sh:303.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: `pactl` reaching the user session from the unit's environment (deploy/sonicflow.service:16-18).
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from clear script text.

### R6-H7-18  Orphaned line-in loopback modules unloaded at startup
- Question: H7, relates to S10.2
- What was attempted: `cleanup_orphaned_loopbacks` unloads leftover loopback modules when the line-in manager initialises (src/audio/line_in.rs:24-25, 92-108). Comment, src/audio/line_in.rs:92: "Loopbacks accumulate across crashes/restarts; sweep ours at startup."
- Presence: CURRENT. src/audio/line_in.rs:92-108.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for the sweep. Commit 9d619ed states "line-in loopback load/unload verified against iec958 source", which covers the normal path.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE found.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-19  3 s wait before startup outputs are activated
- Question: H7, relates to S08.1
- What was attempted: `activate_startup_outputs` sleeps 3 s before discovery and host activation (src/main.rs:174-175). Comment: "Let the pipeline and managers settle first."
- Presence: CURRENT. src/main.rs:174-175.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 1e20b41 lists the startup-output feature as built and gives no result.
- Why it turned out that way: UNKNOWN.
- Depends on: pipeline start finishing inside 3 s; the null-sink retry alone can take longer (src/audio/pipeline.rs:141-167).
- Open questions: What happens to host activation when the pipeline is not ready after 3 s?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-20  parec capture buffer fixed at 50 ms
- Question: H7, relates to S05.4
- What was attempted: both capture paths pass `--latency-msec=50` (src/audio/capture.rs:13-15, 79; src/audio/cast_stream.rs:17-19, 59). Problem stated, src/audio/cast_stream.rs:17-18: "PipeWire's default quantum (1024 ≈ 21.3 ms @ 48 kHz) must fit inside the buffer or it underruns silently." src/audio/capture.rs:14 adds "(Fixes SoundSync D2.)".
- Presence: CURRENT. src/audio/capture.rs:15; src/audio/cast_stream.rs:19.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No underrun count or measurement is recorded.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire quantum of 2048 frames or fewer (src/audio/capture.rs:14).
- Open questions: What quantum was in use on the development machine?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-21  No pacing clock in the WebRTC path; the blocking read of capture frames sets the timing
- Question: H7, relates to S07.3
- What was attempted: frames are sent as soon as they are encoded (src/audio/webrtc_audio.rs:251-263). Problem stated, src/audio/webrtc_audio.rs:205-206: "NO pacer — parec's blocking read IS the clock; a second clock drifts and the browser hears the beat pattern as stuttering." The same rule is at src/audio/capture.rs:126-127.
- Presence: CURRENT. src/audio/capture.rs:125-128; src/audio/webrtc_audio.rs:202-206, 251.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for stutter. Commit 9d619ed states "audio element readyState 4, playing" in headless Chromium; BUILD-PLAN.md:68 sets a 10-minute no-stutter check that no file reports.
- Why it turned out that way: UNKNOWN.
- Depends on: `parec` delivering frames at the capture clock's rate.
- Open questions: Was a long listening test run in any browser?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-22  RTP sequence and timestamp advanced across frames the receiver lagged past
- Question: H7, relates to S05.4
- What was attempted: when the broadcast receiver reports `Lagged(n)`, the pump adds n to the sequence number and n x 960 to the timestamp (src/audio/webrtc_audio.rs:227-232). Problem stated, src/audio/webrtc_audio.rs:203: "advance RTP counters across dropped frames (or the browser plays fast)".
- Presence: CURRENT. src/audio/webrtc_audio.rs:227-233.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. A per-minute counter of sent and dropped frames is logged (src/audio/webrtc_audio.rs:270-276); no log is stored (01-map.md:301).
- Why it turned out that way: UNKNOWN.
- Depends on: broadcast capacity of 256 frames (src/audio/capture.rs:12).
- Open questions: Were any dropped frames logged during the stated live checks?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-23  Each HTTP stream client gets its own parec-to-ffmpeg process pair
- Question: H7, relates to S08.2
- What was attempted: every HTTP stream request spawns a dedicated `parec` and `ffmpeg` joined by a file descriptor (src/audio/cast_stream.rs:49-98). Problem stated, src/audio/cast_stream.rs:13-14: "Deliberately NOT the broadcast channel — the channel adds timing jitter that manifests as audible stuttering."
- Presence: CURRENT. src/audio/cast_stream.rs:12-19, 49-98.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for audio quality. Commit 3d4afd4 states "AAC stream headers + icy-name verified".
- Why it turned out that way: UNKNOWN.
- Depends on: `parec` and `ffmpeg` being installed (install.sh:99-100).
- Open questions: Was the stuttering observed in this repo or in the predecessor?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-24  Two spawn strategies for the EQ process, with `pipewire -c` as the one used on Ubuntu
- Question: H7, relates to S07.1
- What was attempted: the code tries `pipewire-filter-chain --config` and then `pipewire -c` (src/audio/filter_chain.rs:46-66). Comment, src/audio/filter_chain.rs:47-48: "`pipewire -c` (Debian/Ubuntu — the path on this machine; Ubuntu ships no pipewire-filter-chain binary, only the module)."
- Presence: CURRENT. src/audio/filter_chain.rs:46-66.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 174ec2f subject: "Phase 3: EQ verified live". Commit b19435c: "440Hz tone through the EQ sink lights the correct bars end-to-end".
- Why it turned out that way: UNKNOWN.
- Depends on: Ubuntu 26.04 packaging (forinstall.md:20); PipeWire 1.6.2 (forinstall.md:61).
- Open questions: Was the first strategy ever exercised?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-25  Client modules added to the generated filter-chain configuration
- Question: H7, relates to S07.1
- What was attempted: the generated config loads protocol-native, client-node, adapter and metadata modules before the filter-chain module (src/dsp/equalizer.rs:113-122). Problem stated, src/dsp/equalizer.rs:109-111: "a standalone client instance needs the client modules below or module-filter-chain fails with "can't find protocol 'PipeWire:Protocol:Native'"". Commit d8b17a3 lists it as a fix: "standalone pipewire -c needs client modules (protocol-native etc.) or the EQ never registers".
- Presence: CURRENT. src/dsp/equalizer.rs:108-122.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 174ec2f: "Preset apply, positional band gains, preamp gain stage (Mult math correct), kill-and-respawn filter-chain per change" under the subject "EQ verified live"; the same message adds "Audible check pending a Bluetooth source."
- Why it turned out that way: STATED. src/dsp/equalizer.rs:109-111, quoted above.
- Depends on: PipeWire 1.6.2 (forinstall.md:61).
- Open questions: Was the audible check with a Bluetooth source carried out later? No commit says so in those words.
- Confidence: HIGH. STATED in two commits; the config generator agrees.

### R6-H7-26  "already loaded" and "Entity exists" treated as success when loading the RAOP discovery module
- Question: H7, relates to S08.1
- What was attempted: a failed `pactl load-module module-raop-discover` is accepted when stderr contains either string (src/audio/airplay.rs:138-150). Comment, src/audio/airplay.rs:138-139: "Both "already loaded" (PulseAudio) and "Entity exists" (pipewire-pulse) mean success."
- Presence: CURRENT. src/audio/airplay.rs:138-150.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 9d619ed: Cast and AirPlay "live sessions left for a supervised test".
- Why it turned out that way: UNKNOWN.
- Depends on: the exact error text of pipewire-pulse.
- Open questions: Was an AirPlay session ever started with this repo's code?
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-27  RAOP sink polled for up to 5 s after the module loads
- Question: H7, relates to S08.3
- What was attempted: the code looks for the device's RAOP sink up to 10 times, 500 ms apart (src/audio/airplay.rs:152-160). Comment, src/audio/airplay.rs:152: "poll — sinks appear async".
- Presence: CURRENT. src/audio/airplay.rs:152-160.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire's RAOP module creating a sink within 5 s.
- Open questions: Same as R6-H7-26.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-28  Unmatched AirPlay device falls back to the only RAOP sink, and only when exactly one exists
- Question: H7, relates to S08.1
- What was attempted: when no sink matches the device, the single existing RAOP sink is used; with none or several, the connect fails with a message (src/audio/airplay.rs:161-177). Comment, src/audio/airplay.rs:161: "D3 fix: last-resort "any raop sink" only when exactly one exists."
- Presence: CURRENT. src/audio/airplay.rs:161-177.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. BUILD-PLAN.md:85 names the predecessor defect as "no wrong-device fallback".
- Depends on: the sink-name matching in `find_raop_sink` (src/audio/airplay.rs:405-417, per 01-map.md:140).
- Open questions: Same as R6-H7-26.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-29  AirPlay sink un-suspended and polled for ports before linking
- Question: H7, relates to S08.3
- What was attempted: `pactl suspend-sink <sink> 0`, then up to 10 checks 200 ms apart for playback ports (src/audio/airplay.rs:180-195). Problem stated, src/audio/airplay.rs:180: "SUSPENDED sinks expose no ports to pw-link."
- Presence: CURRENT. src/audio/airplay.rs:180-195.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire RAOP sink behaviour.
- Open questions: Same as R6-H7-26.
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-30  Host hardware sink un-suspended before linking
- Question: H7, relates to S08.3
- What was attempted: `pactl suspend-sink <sink> 0` is run before ports are listed and linked (src/audio/host_out.rs:87-93). No comment in this file gives the reason; the AirPlay path states it (src/audio/airplay.rs:180).
- Presence: CURRENT. src/audio/host_out.rs:87-90.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 9d619ed: "Host PC Audio verified — pw-link FL/FR graph links, sink RUNNING, volume 40% via API, deactivate removes all links".
- Why it turned out that way: UNKNOWN.
- Depends on: the Intel HDA sink on the development machine (forinstall.md:133).
- Open questions: Was sound heard from the speaker? BUILD-PLAN.md:74 sets that check; the commit reports graph state.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-31  pw-link errors "already linked" and "File exists" treated as success
- Question: H7, relates to S08.3
- What was attempted: a failed `pw-link` is accepted when stderr contains either string, in the AirPlay path (src/audio/airplay.rs:214-218) and the host path (src/audio/host_out.rs:107-111). No comment gives a reason.
- Presence: CURRENT. Both locations above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the exact error text of `pw-link`.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-32  IPv6 rows dropped from avahi discovery output before de-duplication
- Question: H7, relates to S08.1
- What was attempted: both discovery parsers skip rows whose address contains a colon (src/audio/airplay.rs:323-328; src/audio/chromecast.rs:329-332). Problem stated, src/audio/airplay.rs:323-324: "otherwise an IPv6 row claims the name and the IPv4 row is discarded, losing the device entirely."
- Presence: CURRENT. Both locations above.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 9d619ed: "Cast/AirPlay discovery verified on the real LAN (22 + 19 devices, avahi escape decoding + IPv6 skip + dedup all correct".
- Why it turned out that way: STATED. src/audio/airplay.rs:323-324, quoted above.
- Depends on: every target having an IPv4 address; the LAN of the development site.
- Open questions: NONE.
- Confidence: HIGH. STATED in the commit; both parsers and their tests agree (src/audio/airplay.rs:466; src/audio/chromecast.rs:443).

### R6-H7-33  Cast discovery asks avahi first and uses the in-process mDNS library only if avahi returns nothing
- Question: H7, relates to S08.1
- What was attempted: `discover_devices` runs `avahi-browse` and falls back to the `mdns-sd` crate (src/audio/chromecast.rs:295-305). Problem stated, src/audio/chromecast.rs:295-296: "avahi owns UDP 5353 on Linux; a second responder in this process is unreliable".
- Presence: CURRENT. src/audio/chromecast.rs:295-317, 395-441.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 9d619ed: "Cast/AirPlay discovery verified on the real LAN (22 + 19 devices".
- Why it turned out that way: UNKNOWN. The message does not say which of the two paths found the devices.
- Depends on: avahi-daemon running (install.sh:284); `avahi-browse` installed (install.sh:100).
- Open questions: Was the mdns-sd path ever exercised?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-34  Cast session thread wrapped against panics; disconnect always clears the active state
- Question: H7, relates to S08.3
- What was attempted: commit 2641e55 wrapped the session thread in `catch_unwind`, sending a session-ended event on a panic (src/audio/chromecast.rs:145-162), and made `disconnect` clear and announce even with no live session (src/audio/chromecast.rs:176-190). Stated reason, commit 2641e55: "Cast disconnect now always clears + publishes stopped, even with a dead session — fixes the chip you could never toggle off".
- Presence: CURRENT. src/audio/chromecast.rs:145-162, 176-190.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No re-test of a Cast session is stated in 2641e55.
- Why it turned out that way: UNKNOWN. The earlier failure is STATED at src/main.rs:35-36 (see R6-H2-11).
- Depends on: the panic being catchable (unwinding enabled); no `panic` setting appears in Cargo.toml:1-54.
- Open questions: Did a Cast session start and stop cleanly after 2641e55?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-35  Bluetooth cover-art fetch polls for the image handle and for transfer completion
- Question: H7, relates to S06.3
- What was attempted: after the OBEX session opens, the code reads `ImgHandle` up to 10 times 300 ms apart, then polls the transfer status up to 40 times 200 ms apart, all inside a 10 s timeout (src/art.rs:23, 77, 161-176, 198-205). Problem stated, src/art.rs:161-162: "ImgHandle is only valid during the lifetime of the OBEX connection — read it AFTER the session is up (BlueZ media-api)."
- Presence: CURRENT. src/art.rs:161-205.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 738f12c reports a live test of the online tier only.
- Why it turned out that way: UNKNOWN.
- Depends on: `Experimental = true`, BlueZ >= 5.79 and `bluez-obexd` (forinstall.md:144-150); a source that offers AVRCP 1.6 cover art.
- Open questions: The polls can total 11 s (3 s plus 8 s) inside a 10 s timeout; was the timeout ever reached? Did any phone return an image?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-36  BlueZ experimental interface switched on for cover art
- Question: H7, relates to S06.3
- What was attempted: the installer sets `Experimental = true` in main.conf (install.sh:173-175). forinstall.md:144-147: it "exposes the experimental D-Bus properties `ObexPort` and `ImgHandle` that native AVRCP 1.6 cover art depends on (BlueZ >= 5.79; this box: 5.85)".
- Presence: CURRENT. install.sh:175; reverted by uninstall.sh:131; checked at deploy/doctor.sh:29-33.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The forinstall.md entry is marked "[PENDING → install.sh]" (forinstall.md:144).
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ >= 5.79 (forinstall.md:146).
- Open questions: Was the key set on the development machine? Does it change any other BlueZ behaviour there?
- Confidence: MEDIUM. STATED requirement; no result evidence.

### R6-H7-37  Deezer queried with a plain string, not its fielded syntax
- Question: H7, relates to S06.3
- What was attempted: the Deezer lookup sends "artist title" as one plain query (src/art.rs:300-306). Problem stated, src/art.rs:298-299: "Deezer's fielded `artist:"x" track:"y"` syntax returns nothing through URL encoding."
- Presence: CURRENT. src/art.rs:298-313.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 738f12c: "Deezer fielded-query quirk fixed".
- Why it turned out that way: STATED. src/art.rs:298-299, quoted above.
- Depends on: Deezer's public search endpoint (src/art.rs:303); `curl --data-urlencode` (src/art.rs:266).
- Open questions: The ignored test prints the Deezer result without asserting it (src/art.rs:474-475); what did it return?
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-38  Descriptive User-Agent sent to MusicBrainz
- Question: H7, relates to S06.3
- What was attempted: the MusicBrainz request carries a project User-Agent header (src/art.rs:26, 329). Comment, src/art.rs:25: "MusicBrainz requires a descriptive User-Agent and ≤ 1 req/s."
- Presence: CURRENT. src/art.rs:25-26, 316-337.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 738f12c: "MB release found".
- Why it turned out that way: UNKNOWN.
- Depends on: MusicBrainz and Cover Art Archive endpoints (src/art.rs:323, 335).
- Open questions: Which code keeps requests at or under one per second? None was found; lookups run once per track change with a per-album disk cache (src/art.rs:14-15).
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-39  iTunes artwork URL rewritten from 100 px to 600 px
- Question: H7, relates to S06.3
- What was attempted: the returned `artworkUrl100` has "100x100" replaced by "600x600" (src/art.rs:293-295). Comment, src/art.rs:294: "The CDN serves arbitrary sizes by path substitution."
- Presence: CURRENT. src/art.rs:293-295; unit test at src/art.rs:460-463.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 738f12c: "iTunes 600px art downloaded".
- Why it turned out that way: UNKNOWN.
- Depends on: the image host's URL scheme.
- Open questions: NONE.
- Confidence: MEDIUM. STATED evidence alone.

### R6-H7-40  Stream-start message keeps the predecessor's empty-name form; the client keeps the old name
- Question: H7, relates to S09.4
- What was attempted: a stream start is sent as `device_state_changed` with an empty name (src/web/ws.rs:174-179) and the client keeps the name it already has (webui/src/state.tsx:122-123). API.md:134 labels it "Quirks preserved from SoundSync (§17.2), deliberately".
- Presence: CURRENT. src/web/ws.rs:174-179; test at src/web/ws.rs:304.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No reason for keeping the quirk is given beyond "deliberately".
- Depends on: every client applying the keep-old-name rule.
- Open questions: Why was the quirk kept when other parts of the predecessor's contract were changed (API.md:6-8)?
- Confidence: MEDIUM. STATED as deliberate; no result evidence.

### R6-H7-41  ICE candidate field name fixed as sdpMLineIndex; the client accepts two spellings
- Question: H7, relates to S08.2
- What was attempted: the server renames the field explicitly (src/web/ws.rs:87-95) and the client reads either `sdpMLineIndex` or `sdp_mline_index` and drops candidates where both identifiers are null (webui/src/webrtc.ts:177-181). Problem stated, src/web/ws.rs:87-88: "serde's camelCase would produce `sdpMlineIndex` and Safari drops those."
- Presence: CURRENT. Both locations above; API.md:138-140.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for Safari. Commits name headless Chromium and Firefox 154 only (9d619ed, 2641e55).
- Why it turned out that way: UNKNOWN.
- Depends on: Safari's handling of ICE candidates.
- Open questions: Was Safari or iOS used against this repo's code?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-42  Audio element created and played inside the click handler (Safari rule a)
- Question: H7, relates to S08.3
- What was attempted: `start` creates the audio element, sets `playsinline`, and calls `play()` before any await (webui/src/webrtc.ts:64-72). Comment, webui/src/webrtc.ts:67: "Rule (a): prime the element before ANY await."
- Presence: CURRENT. webui/src/webrtc.ts:64-72.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for Safari. Commit 9d619ed reports playback in headless Chromium.
- Why it turned out that way: UNKNOWN.
- Depends on: browser autoplay policy.
- Open questions: Same as R6-H7-41.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-43  AudioContext created, resumed and closed after priming (Safari rule b)
- Question: H7, relates to S08.3
- What was attempted: `negotiate` opens an `AudioContext` (or `webkitAudioContext`), resumes it and closes it, ignoring errors (webui/src/webrtc.ts:103-114). Comment, webui/src/webrtc.ts:103: "Rule (b): unlock an AudioContext after priming."
- Presence: CURRENT. webui/src/webrtc.ts:103-114.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: browser autoplay policy.
- Open questions: Same as R6-H7-41.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-44  Remote ICE candidates buffered until the remote description is set (Safari rule c)
- Question: H7, relates to S08.2
- What was attempted: candidates that arrive before the answer are queued and added afterwards (webui/src/webrtc.ts:165-169, 184-187). Comment, webui/src/webrtc.ts:165: "Rule (c): flush candidates buffered before the remote description."
- Presence: CURRENT. webui/src/webrtc.ts:161-189.
- Outcome: UNVERIFIED
- Outcome evidence: NONE specific to the buffering. Commit 2641e55 states "WebRTC connected" in two browsers.
- Why it turned out that way: UNKNOWN.
- Depends on: message order on the WebSocket.
- Open questions: Same as R6-H7-41.
- Confidence: MEDIUM. INFERRED from clear code.

### R6-H7-45  HTTP fallback after 5 s reuses the audio element primed in the click
- Question: H7, relates to S08.3
- What was attempted: a 5 s timer switches the same element to the HTTP stream when WebRTC has not produced playback (webui/src/webrtc.ts:77-89). Problem stated, webui/src/webrtc.ts:77-78: "the fallback swaps src on the ALREADY-PLAYING element, so no new play() happens outside the gesture chain." Labelled "D11 fix", a predecessor defect (webui/src/webrtc.ts:2-4).
- Presence: CURRENT. webui/src/webrtc.ts:77-89.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The stated browser checks report WebRTC connecting, not the fallback.
- Why it turned out that way: UNKNOWN.
- Depends on: browser autoplay policy; the HTTP stream routes (src/web/mod.rs:105-106).
- Open questions: Was the HTTP fallback exercised in any browser?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-46  Fallback to the HTTP stream when the peer connection fails or disconnects
- Question: H7, relates to S08.3
- What was attempted: commit 2641e55 added a connection-state handler that tears down the peer and starts the HTTP stream on "failed" or "disconnected" (webui/src/webrtc.ts:130-138). Stated reason, commit 2641e55: "fall back to HTTP stream on ICE failure/disconnect (was: silent dead session)".
- Presence: CURRENT. webui/src/webrtc.ts:130-138.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The 2641e55 check reports a successful connection.
- Why it turned out that way: UNKNOWN.
- Depends on: a public STUN server named at webui/src/webrtc.ts:118 and src/audio/webrtc_audio.rs:130.
- Open questions: Was an ICE failure provoked to test the handler?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-47  MP3 stream used when the browser cannot play the AAC stream (Firefox)
- Question: H7, relates to S08.2
- What was attempted: on an audio error in HTTP mode the source is switched from `.aac` to `.mp3` and play is retried (webui/src/webrtc.ts:196-202). Comment, webui/src/webrtc.ts:197: "Firefox cannot decode raw ADTS AAC — swap to the MP3 stream." Commit 2641e55: "fix the Firefox ADTS-AAC fallback to actually replay after swapping to MP3".
- Presence: CURRENT. webui/src/webrtc.ts:191-205.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The Firefox 154 check in 2641e55 reports WebRTC, not the HTTP path.
- Why it turned out that way: UNKNOWN.
- Depends on: Firefox 154; ffmpeg with libmp3lame (src/audio/cast_stream.rs:32).
- Open questions: Was the MP3 path played in Firefox after the change?
- Confidence: MEDIUM. STATED problem; no result evidence.

### R6-H7-48  MediaStream built from the track because the server library attaches none
- Question: H7, relates to S08.2
- What was attempted: `ontrack` uses `ev.streams[0]` if present, otherwise `new MediaStream([ev.track])` (webui/src/webrtc.ts:122-128). Comment, webui/src/webrtc.ts:123: "webrtc-rs add_track() associates no stream: streams[0] is undefined."
- Presence: CURRENT. webui/src/webrtc.ts:122-128.
- Outcome: WORKED
- Outcome evidence: STATED. Commit 2641e55: "WebRTC connected, unmuted, signal present in the received track" in headless Chromium and Firefox 154.
- Why it turned out that way: STATED. webui/src/webrtc.ts:123, quoted above.
- Depends on: `webrtc` crate 0.12 (Cargo.toml:31).
- Open questions: NONE.
- Confidence: HIGH. STATED in the commit; code agrees.

### R6-H7-49  Linger enabled so the user audio stack outlives SSH sessions
- Question: H7, relates to S10.2
- What was attempted: `loginctl enable-linger` for the service user (install.sh:136-141; done by hand on 2026-08-29, forinstall.md:81-83, 165). Problem stated, forinstall.md:82-83: "Was Linger=no; the whole PipeWire user stack died when the last SSH session ended."
- Presence: CURRENT. install.sh:136-141; check at deploy/doctor.sh:15-16; optional revert at uninstall.sh (flag `--disable-linger`, uninstall.sh:38-57).
- Outcome: UNVERIFIED
- Outcome evidence: NONE for survival after logout or reboot. The notes record the action as done.
- Why it turned out that way: UNKNOWN.
- Depends on: systemd-logind; a system unit that reaches the user's session directory (deploy/sonicflow.service:16-18).
- Open questions: Was a logout or reboot test made after linger was enabled?
- Confidence: MEDIUM. STATED problem and action; no result evidence.

### R6-H7-50  WirePlumber override allowing writable-executable memory
- Question: H7, relates to S04.2
- What was attempted: a user-unit drop-in sets `MemoryDenyWriteExecute=no` for WirePlumber (install.sh:220-227). Reason stated, forinstall.md:91: "BT codec sub-plugins need W|X mmap". The drop-in existed on the machine before the project (forinstall.md:42-43).
- Presence: CURRENT. install.sh:220-227; left in place by the uninstaller (commit 26a79c2: "never touches ... the pre-existing MDWE override").
- Outcome: UNVERIFIED
- Outcome evidence: NONE for its effect on codecs. forinstall.md:92-93 records that with the override present the endpoints were "STILL not registering", so it was not what restored sink registration.
- Why it turned out that way: UNKNOWN.
- Depends on: the LDAC and aptX plugins (forinstall.md:167).
- Open questions: Was WirePlumber run without the override to see which codecs load?
- Confidence: MEDIUM. STATED reason; no result evidence.

### R6-H7-51  Installer restarts WirePlumber once more when the sink UUID is missing
- Question: H7, relates to S04.1
- What was attempted: after writing the fragment the installer checks `bluetoothctl show` for the Audio Sink UUID; if absent it restarts WirePlumber, waits 5 s and checks again (install.sh:232-241). Commit 26a79c2 names it the "self-healing sink-UUID gate".
- Presence: CURRENT. install.sh:232-241.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No file says a second restart was ever needed.
- Depends on: `bluetoothctl` output format; a running user session for `systemctl --user` (install.sh:58-64).
- Open questions: Was the UUID ever missing after the first restart?
- Confidence: MEDIUM. INFERRED from clear script text.

### R6-H7-52  Fixed sleeps after service restarts in the installer
- Question: H7, relates to S10.1
- What was attempted: `sleep 2` after restarting bluetooth (install.sh:178-179), `sleep 4` after restarting WirePlumber (install.sh:229-230), `sleep 3` before the acceptance gate (install.sh:386). No comment gives a reason for the durations.
- Presence: CURRENT. Lines above.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the services becoming ready inside those times on the target machine.
- Open questions: NONE.
- Confidence: MEDIUM. INFERRED from clear script text.

### R6-H7-53  Installer stops a development instance before starting the unit
- Question: H7, relates to S10.1
- What was attempted: commit 8d2e000 added stopping the transient unit `sonicflow-dev` and killing any `sonicflow` process of the user, then `sleep 1` (now install.sh:348-351). Stated reason, commit 8d2e000: "install.sh stops a running sonicflow-dev/dev binary before claiming the port."
- Presence: CURRENT. install.sh:348-351; the same stop in uninstall.sh (01-map.md:59).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: the development unit name; it has no definition in the repo (01-map.md:59).
- Open questions: How was the development instance started? No command for it is recorded.
- Confidence: MEDIUM. STATED reason; no result evidence.

### R6-H7-54  Service start retried up to five times with a new port on a bind failure
- Question: H7, relates to S12.1
- What was attempted: the installer restarts the service, waits 4 s and requests `/api/status`; when the journal shows "address in use" or "HTTP server exited" it picks the following free port, rewrites the config and tries again, up to 5 times (install.sh:358-374). Comment, install.sh:358-359: "on a bind race, pick the next port and retry".
- Presence: CURRENT. install.sh:318-327, 358-382.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Commit 26a79c2 describes the mechanism ("port negotiation VERIFIED against the live app (/api/status) with bind-race renegotiation") and gives no run result.
- Why it turned out that way: UNKNOWN.
- Depends on: ports 80, 443 and 53 being taken by other software on the development machine (forinstall.md:57-58); `ss`, `curl` and `journalctl`.
- Open questions: Does the capitalised word in the 26a79c2 message describe a test that was run or the behaviour of the script?
- Confidence: MEDIUM. Script text is clear; the commit wording is ambiguous.

### R6-H7-55  Installer accepts WirePlumber 0.5, 0.6 and 1.x only
- Question: H7, relates to S01.4
- What was attempted: a version check passes `0.5*|0.6*|1.*` and reports anything else as too old (install.sh:86-91). Message text, install.sh:90: "0.5+ required for the conf-format fragments". forinstall.md:62 adds "0.5 SPA-JSON `.conf` config format, NOT 0.4 Lua".
- Presence: CURRENT. install.sh:86-91.
- Outcome: UNVERIFIED
- Outcome evidence: NONE beyond the development machine's version, 0.5.13 (forinstall.md:62).
- Why it turned out that way: UNKNOWN.
- Depends on: the fragment format of WirePlumber 0.5 (install.sh:187-205).
- Open questions: forinstall.md:62-63 mentions keeping "dual-format logic only if older targets matter"; no 0.4 path exists in install.sh. Was one written at any point? No commit contains one.
- Confidence: MEDIUM. INFERRED from clear script text.

### R6-H7-56  Reboot advised when the Class of Device has not taken effect
- Question: H7, relates to S03.1
- What was attempted: the acceptance step reads the adapter Class; when it is not Audio/Video the installer prints "reboot once (controller re-reads main.conf)" (install.sh:388-392). README.md:250-252 repeats "may need one reboot". The step header names a device family: "Class of Device is what makes iPhones list us as a speaker" (install.sh:152).
- Presence: CURRENT. install.sh:152, 388-392.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ >= 5.66 honouring the main.conf Class (src/bluetooth/constants.rs:16).
- Open questions: Was a reboot needed on the development machine? Was an iPhone seen to omit the device before the Class was set?
- Confidence: MEDIUM. STATED advice; no result evidence.

Timing values and version ranges with no stated problem (listed for completeness; no record):

- Bluetooth init retried every 10 s after a failure (src/bluetooth/manager.rs:70-83).
- 5 s wait after a failed AVRCP D-Bus connect (src/bluetooth/avrcp.rs:92-95).
- Poll intervals: device properties 500 ms, AVRCP 250 ms active and 2000 ms idle (src/bluetooth/constants.rs:3-5).
- AirPlay sink presence checked every 5 s (src/audio/airplay.rs:227-240).
- Cast heartbeat every 5 s, session ended after three failures (src/audio/chromecast.rs:10, 242-268).
- Discovery timeouts: `avahi-browse` 12 s for Cast (src/audio/chromecast.rs:307-314) and 10 s for AirPlay (src/audio/airplay.rs:303-310); mdns-sd browse 10 s (src/audio/chromecast.rs:9, 400-403).
- 14 s wait between Cast discovery and rejoining the last Cast device at startup (src/main.rs:201-219).
- 300 ms wait at shutdown (src/main.rs:138-139).
- 500 ms wait after spawning `pw-loopback` in the path used when `pactl` is absent (src/audio/pipeline.rs:172-188).
- Line-in loopback `latency_msec=50` (src/audio/line_in.rs:51).
- `curl` timeout 10 s for art lookups (src/art.rs:24, 264).
- WebSocket reconnect backoff doubling to a 15 s cap (webui/src/state.tsx:286-293).
- Rescan indicator cleared after 12 s (webui/src/components/Settings.tsx:429-433).
- Unit restart policy `Restart=on-failure`, `RestartSec=5` (deploy/sonicflow.service:26-27).
- AAC pipeline spawn failure falls through to MP3 on the server (src/audio/cast_stream.rs:21-28).
- Dependency ranges without comments: Cargo.toml:14-54; `typescript ~5.6.0` at webui/package.json:17; "rust_cast 0.21.x" at DECISIONS.md:105.

---

## H8 Final state

Subjects of the last ten commits, newest first (`git log --all -10 --date=short --pretty='%h %ad %s'`):

1. 3046b9e 2026-08-30 "v0.2.1: art_online config knob; installer template + README document it"
2. 738f12c 2026-08-30 "v0.2.0: album art — BIP native + online cascade + idle image (D-ART-1c/2/3b)"
3. 3624d19 2026-08-30 "v0.1.1: NoAlbumArt.png fallback replaces the gradient placeholder"
4. 07b63e5 2026-08-30 "Add MIT LICENSE + GitHub Actions build/release workflow"
5. 7448b91 2026-08-30 "forinstall: log gh CLI install (dev tooling, not an app dependency)"
6. 5eaf177 2026-08-30 "Remove placeholder readme.md (superseded by README.md)"
7. 128e29f 2026-08-30 "Merge GitHub placeholder repo history"
8. beead5a 2026-08-30 "Comprehensive README: what it is, architecture, install, API, troubleshooting"
9. b306b25 2026-08-30 "Create readme.md"
10. 26a79c2 2026-08-30 "Installer v2 + uninstaller: audited, self-healing, privilege-split, recorded reverts"

Uncommitted changes:

- None. The manifest records an empty `git status --porcelain` for R6 (00-manifest.md:27); `git status --short` printed nothing during this audit; 01-map.md:3 records the same.

Unmerged or parked work:

- None. No unmerged branch, stash or tag (R6-H4-01).

State of the last commit:

- 3046b9e changes 8 files (`git show --stat 3046b9e`): the version in four places, the `art_online` setting in src/state/config.rs and src/art.rs, the installer template and README.md.
- The four version locations agree at 0.2.1: Cargo.toml:3, webui/package.json:4, webui/src/version.ts:3, install.sh:20.

Items that the repo's own documents leave open at the last commit (facts with citations, no judgement):

- No `v*` tag exists (`git tag`), while CHECKLIST.md:36-37 and .github/workflows/build.yml:88-90 describe releases published from tags.
- API.md:1 is titled "v0.1.0 — FROZEN"; the package version is 0.2.1 (Cargo.toml:3). API.md was changed twice after the freeze commit 3d4afd4: by 9d619ed and 738f12c (`git log --all --oneline -- API.md`).
- Test counts differ between sources: commit d8b17a3 says "63 passing"; README.md:208 says "66 unit tests"; the map counts 70 test attributes (01-map.md:299). No test output is stored (01-map.md:301).
- The pre-commit hook compares three version files (.githooks/pre-commit:8-11); the CI workflow compares four, adding install.sh (.github/workflows/build.yml:40-45); CHECKLIST.md:9-10 names four; README.md:212-215 names three.
- forinstall.md carries 17 entries marked pending (forinstall.md:55, 59, 61, 64, 72, 74, 77, 84, 109, 112, 115, 117, 120, 125, 127, 144, 148). forinstall.md:9-10 says these "are performed by install.sh at install time". No file reports a run of install.sh v2 (R6-H2-13).
- Build-plan phases and commit subjects: subjects name Phases 0, 1, 2, 3, 4, 5, 6/7/10 and 10/11 (d8b17a3 to 1e20b41). No subject names Phase 8 (AVRCP) or Phase 9 (Cast and AirPlay) (BUILD-PLAN.md:76-87). Commit 9d619ed says of Cast and AirPlay: "live sessions left for a supervised test". Commit 174ec2f says: "Audible check pending a Bluetooth source." Later statements: commit 0ad9f4e names a playing iPhone; README.md:283-285 says "verified end-to-end on real hardware: real phones, a house full of real Chromecasts".
- DECISIONS.md:77 and BUILD-PLAN.md:86 call for a Cast-plus-AirPlay concurrency spike. No result was found (`git grep -n -i 'spike'` matches those two lines only; all commit messages read).
- BUILD-PLAN.md:100 sets the Phase 11 check "clean install path replayed on the box; reboot → everything works untouched". No commit message, notes entry or README passage reports a reboot test.
- webui/src/components/NowPlaying.tsx:3 still describes a gradient placeholder that commit 3624d19 removed (R6-H2-15).
- deploy/doctor.sh:49 counts Bluetooth sources with `pactl list short sources`; src/bluetooth/manager.rs:821-824 says stream-role nodes do not appear in that listing (R6-H2-08).

Whether the repo was left mid-change:

- At the version-control level, no: the working tree is clean, nothing is stashed, no branch holds unmerged work, and the last commit is a complete version step with all four version locations in agreement (citations above). INFERRED from those facts; reasoning: a change in progress leaves uncommitted files, a stash, a diverged branch or mismatched version files, and none of these is present.
- At the document level, the open items listed above remain. Each is a statement by the author or an absence found by search; none is graded here.

---

## Tally

| Question | Answered with | Records |
|---|---|---|
| H1 Timeline | findings (list) | list with citations |
| H2 Approach changes | findings | R6-H2-01 to R6-H2-19 (19 finding records) |
| H3 Reverts and repeated fixes | findings | R6-H3-02 to R6-H3-07 (6 finding records); R6-H3-01 is a NOT FOUND record for the reverted-commits part |
| H4 Unmerged work | NOT FOUND | R6-H4-01 (1 NOT FOUND record) |
| H5 Dead and disabled code | findings | R6-H5-01 to R6-H5-14 (14 finding records) |
| H6 Author's markers | findings (table) | 79 table rows; none of the six named marker words occurs |
| H7 Workarounds | findings | R6-H7-01 to R6-H7-56 (56 finding records) |
| H8 Final state | findings (list) | list with citations |

- Questions answered with findings: 7 (H1, H2, H3, H5, H6, H7, H8)
- Questions answered NOT FOUND: 1 (H4)
- Total: 8

Record counts: 95 finding records (H2 19, H3 6, H5 14, H7 56); 2 NOT FOUND records (R6-H3-01, R6-H4-01). H3 is counted once, under findings; its NOT FOUND record covers the reverted-commits half of the question only.
