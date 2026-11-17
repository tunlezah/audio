# R2 stage audit: Bluetooth stages S01 to S06

Repo: /home/user/AudioBurgerSonnet (R2). All paths are relative to that root.
Read at commit 1d878c2 (`main`; the checked-out branch points at the same commit). Older file versions are cited as `HASH:PATH:LINE` and were read with `git show`.
Role: Bluetooth stage auditor. 24 questions, S01.1 to S06.3.

## Conventions used in this file

- **Document-only material.** A design statement or a code listing that exists only as text in a `.md` file, with no counterpart in `audioburger/`, `install.sh`, `config.toml` or a unit file, is given Presence DISABLED with the words "document only". It is present on the default branch and not reachable from any run path. The Rule 5 vocabulary has no separate term for it.
- **Note N1, general statements in commit messages.** Commit eb02d59 message: "All core audio routing components are now functional." and, in its list, "Capture audio from line-in or Bluetooth A2DP sources". Commit add5474 message: "All audio routing is now fully functional end-to-end". Neither message names a run, a test, a device, a log or a measurement. The two commits are dated 2026-02-02 03:03:01 and 03:04:50 +0000 (`git log --date=iso`). No test file exists beyond the zero-line `audioburger/tests/__init__.py`, and no log, CI result or test output is tracked (`git ls-files`; `.gitignore:46` ignores `*.log`). In this file these statements are not counted as evidence of a result for any single function. Each record they touch says so and carries the matter as an open question.
- **Note N2, document dates.** `README.md` (last changed deb899b, 2026-02-01), `PROJECT-COMPLETION-ROADMAP.md` (1491214, 2026-02-02 02:42) and `AI-AGENT-IMPLEMENTATION-GUIDE.md` (cec17a7, 2026-02-02 02:51) all predate eb02d59 (`git log --date=iso -- <file>`). Their statements that the Bluetooth and audio modules are stubs describe the code as it stood at 70fc363 to b61e1ec, which eb02d59 replaced.
- **Note N3, text addressed to an AI agent.** `AI-AGENT-IMPLEMENTATION-GUIDE.md:1-22` is written as instructions to an AI coding agent. Under Rule 10 it was read as material only. Its code listings are recorded where they differ from the code on main.
- **Owner notes.** `/home/user/audio/00-manifest.md:12` gives "(none given)" for R2.

---

## S01 Platform and stack

### R2-S01-01  Target platforms named in the documents
- Question: S01.1
- What was attempted: The requirements document names Linux, "systemd-based assumed unless otherwise specified" (project-context.md:17), ARM64 "Raspberry Pi 4 / 5 class devices" (project-context.md:21) and x86_64 "Intel NUC, Mac Mini running Linux" (project-context.md:22). README names "Linux (tested on Ubuntu 22.04+, Raspberry Pi OS)" (README.md:23). setup.py carries the classifiers 'Development Status :: 2 - Pre-Alpha' and 'Operating System :: POSIX :: Linux' (setup.py:45, :51). INSTALLER.md lists x86_64, ARM64 and ARMv7 (INSTALLER.md:10-12) and a "Tested Versions" column: Ubuntu 20.04+, Debian 11+, Fedora 35+, RHEL 8+, Rocky Linux 8+, Arch rolling, openSUSE Leap 15.3+ and Tumbleweed (INSTALLER.md:18-23). The architecture document names Raspberry Pi 4/5 as target hardware (architecture-and-build-plan.md:705) and Ubuntu 22.04+ and Fedora 34+ as distributions to document (architecture-and-build-plan.md:1662). The Bluetooth research report leaves the distribution choice open, "Needs: Human review decision (Phase 3)" (bluetooth-a2dp-sink-research.md:495-506). No OS version is pinned in any manifest.
- Presence: CURRENT. All cited files are on main at 1d878c2.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. README.md:23 and INSTALLER.md:18 use the words "tested" and "Tested Versions" but name no run, result or log. The same README is headed "NOT a working application" (README.md:7). No test output or log is tracked (`git ls-files`).
- Why it turned out that way: UNKNOWN. No file says what was run on any named platform.
- Depends on: The documents disagree on the lowest Ubuntu release (22.04+ at README.md:23, 20.04+ at INSTALLER.md:20) and on ARMv7 (listed at INSTALLER.md:12, absent from project-context.md:19-22). The architecture go/no-go table lists "Dev environment ready" as pending with the note "Need to verify Raspberry Pi and Chromecast availability" (architecture-and-build-plan.md:2441).
- Open questions: What was run on Ubuntu 22.04+ and Raspberry Pi OS, and with what result? Was a Raspberry Pi, NUC or any other machine used at any point? What answer was given to the distribution question at bluetooth-a2dp-sink-research.md:495-506?
- Confidence: MEDIUM. Statements read directly from documents; no artefact of a run.

### R2-S01-02  Platform detection in the installer
- Question: S01.1
- What was attempted: `install.sh` maps `uname -m` to x86_64, arm64 or armv7 and continues with a warning on any other value (install.sh:222-240). It reads the distribution from `/etc/os-release` or release files (install.sh:242-256), picks apt, dnf, yum, pacman or zypper (install.sh:258-272) and tests for systemd (install.sh:274-280). The header names "ARM64 (Raspberry Pi), x86_64 (Intel/AMD)" and "Debian/Ubuntu, Fedora/RHEL, Arch, and derivatives" (install.sh:6-7). No branch of the detection code refers to Bluetooth hardware or to a Bluetooth package (case-insensitive search of install.sh for `bluez`, `bluetooth`, `hci`: no hit).
- Presence: CURRENT. install.sh on main, added in 70b5a5a.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No install log or transcript is tracked (`git ls-files`; `.gitignore:46`).
- Why it turned out that way: UNKNOWN.
- Depends on: bash, `uname`, `/etc/os-release`, one of five package managers, root (install.sh:10).
- Open questions: Was install.sh run on any machine? On which architecture and distribution?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S01-03  Languages, runtimes and frameworks
- Question: S01.2
- What was attempted: Python 3.11 or later (setup.py:36; requirements.txt:2; install.sh:26) with asyncio as the run loop (audioburger/main.py:16, :438). aiohttp for HTTP and WebSocket (requirements.txt:5; audioburger/web_ui/api_server.py:92-107). dbus-python for BlueZ calls (requirements.txt:16; audioburger/bluetooth/a2dp_sink.py:16-22). numpy, scipy and sounddevice in the audio module (requirements.txt:11-13; audioburger/audio_plane/audio_pipeline.py:13-15, :21-25). Frontend in Svelte 4 built with Vite 5 (audioburger/web_ui/frontend/package.json:11-18), Node.js 18 or later (install.sh:27). Installer in Bash (install.sh:1). Configuration in TOML (config.toml:1-35). Service definition for systemd (audioburger.service:1-32; install.sh:965-993).
- Presence: CURRENT. All cited files are on main.
- Outcome: PARTIAL
- Outcome evidence: STATED, for the code as it stood at deb899b (Note N2). README.md:9: "The logging, config, and app-lifecycle layers are functional. All audio, Bluetooth, and Chromecast modules are stubs that log events but do nothing." README.md:212: "aiohttp HTTP server starts and binds successfully". Defects listed by the author at that commit: Bluetooth, audio and Chromecast modules were stubs (README.md:226-235); the frontend could not be served (README.md:224); the status endpoint returned fixed data (README.md:221). For the code after eb02d59 and add5474 only the general statements of Note N1 exist.
- Why it turned out that way: STATED. The first code commit was a skeleton by intent: "This is a **foundation skeleton**. Do not implement full features without explicit instruction." (README.md:351).
- Depends on: Python 3.11+; the listed Python packages at or above their lower bounds; Node.js for the frontend build.
- Open questions: Was the application started after eb02d59 and add5474? Did `import dbus` and `import sounddevice` succeed on any machine (both are guarded, a2dp_sink.py:16-22, audio_pipeline.py:21-25)?
- Confidence: MEDIUM. STATED evidence alone, and it describes an earlier commit.

### R2-S01-04  BlueZ reached over the system D-Bus with dbus-python
- Question: S01.3
- What was attempted: The Bluetooth module opens `dbus.SystemBus()` (audioburger/bluetooth/a2dp_sink.py:92), addresses the service name `org.bluez` and the interfaces `org.bluez.Adapter1`, `org.freedesktop.DBus.Properties`, `org.freedesktop.DBus.ObjectManager` and `org.bluez.Device1` (a2dp_sink.py:97-99, :219-222, :302-305). The binding is `dbus-python>=1.3.2` (requirements.txt:16), imported under a guard that sets `DBUS_AVAILABLE` (a2dp_sink.py:16-22). No BlueZ version appears in any manifest. Documents give "BlueZ 5.x" (bluetooth-a2dp-sink-research.md:11) and "BlueZ 5.50+ with PipeWire Bluetooth backend" (architecture-and-build-plan.md:199); the research report lists distribution versions "Debian Bookworm: BlueZ 5.66; Ubuntu 24.04: BlueZ 5.7x; Raspberry Pi OS: varies" (bluetooth-a2dp-sink-research.md:500). Two documents name `pybluez` as a dependency to add (README.md:274; architecture-and-build-plan.md:1909); it is not in requirements.txt (requirements.txt:1-32).
- Presence: CURRENT. Added in eb02d59 (audioburger/bluetooth/a2dp_sink.py).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Note N1 applies: commit eb02d59 lists "Implement BlueZ D-Bus integration for Bluetooth control" and names no run.
- Why it turned out that way: UNKNOWN.
- Depends on: A BlueZ daemon owning `org.bluez` on the system bus. `install.sh` installs no Bluetooth package and no D-Bus or GLib development package (case-insensitive search of install.sh for `bluez`, `bluetooth`, `dbus`, `glib`: no hit; system packages at install.sh:392-404, :449-458); dbus-python arrives through `pip install -r requirements.txt` (install.sh:740). The repo unit file orders after and wants `bluetooth.target` (audioburger.service:3-4); the unit the installer writes names `network.target sound.target` only (install.sh:968-969). The service user is `audioburger` with group `audio` (audioburger.service:10-11; install.sh:941-943, :973-974). No D-Bus policy file is tracked (`git ls-files`).
- Open questions: Which BlueZ version, if any, was the code run against? Does the `audioburger` user have permission to set `org.bluez.Adapter1` properties on the system bus? Did the pip build of dbus-python complete on any target?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S01-05  PipeWire and WirePlumber as the Bluetooth audio backend (design)
- Question: S01.3
- What was attempted: The Bluetooth research report names three options and states "PipeWire + WirePlumber is the recommended approach" (bluetooth-a2dp-sink-research.md:13, :122-146). The architecture document adopts "PipeWire (primary) with BlueZ integration" (architecture-and-build-plan.md:15) and states "PipeWire 0.3.50+ includes native Bluetooth support with automatic profile switching" (architecture-and-build-plan.md:420). Under this design the A2DP sink role, codecs and audio transport belong to PipeWire, and the Python code talks to BlueZ for device control only (architecture-and-build-plan.md:393-416). In code, PipeWire appears in comments and docstrings only (audioburger/bluetooth/a2dp_sink.py:8, :40; audioburger/audio_plane/audio_pipeline.py:46, :226-230, :362, :426). `install.sh` installs and configures neither PipeWire nor WirePlumber (case-insensitive search for `pipewire`, `wireplumber`: no hit).
- Presence: DISABLED. Document only; no call into PipeWire or WirePlumber in `audioburger/` and no configuration file for either is tracked (`git ls-files`).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN for the absence from code. The roadmap listed it as work still to do: "Bluetooth audio comes via PipeWire - capture from virtual source" / "This requires PipeWire setup" (PROJECT-COMPLETION-ROADMAP.md:357-358).
- Depends on: PipeWire 0.3.50+ (architecture-and-build-plan.md:420, :1661); the audio framework report gives "v0.3.65+" (audio-framework-research-report.md:15, :185). The research report notes that on Raspberry Pi the services need enabling with `systemctl --user enable --now pipewire pipewire-pulse wireplumber` (bluetooth-a2dp-sink-research.md:254); the application is defined as a system service under its own user (audioburger.service:10; install.sh:973).
- Open questions: Was PipeWire or WirePlumber present on any machine the code ran on? How was a system service under the `audioburger` user meant to reach a per-user PipeWire session?
- Confidence: MEDIUM. STATED in two documents; code agrees that nothing implements it.

### R2-S01-06  PulseAudio with BlueZ (evaluated on paper, rejected)
- Question: S01.3
- What was attempted: The research report describes PulseAudio with `module-bluetooth-discover` (bluetooth-a2dp-sink-research.md:90-118) and rejects it: "Deprecated technology with poor A2DP profile management." (bluetooth-a2dp-sink-research.md:154), with the stated deciding factor "Building a new long-running appliance on deprecated infrastructure is high-risk for future maintenance." (bluetooth-a2dp-sink-research.md:162). No PulseAudio call or package appears in code or installer (search of `audioburger/` and install.sh for `pulse`, `pactl`, `parec`: no hit).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The report cites web sources (bluetooth-a2dp-sink-research.md:579-582); nothing in the repo shows PulseAudio being run.
- Why it turned out that way: STATED. Rejection reasons at bluetooth-a2dp-sink-research.md:152-162.
- Depends on: The report's reading of distribution trends in 2025-2026 (bluetooth-a2dp-sink-research.md:11, :97).
- Open questions: NONE
- Confidence: MEDIUM. STATED in one document.

### R2-S01-07  BlueALSA with BlueZ (evaluated on paper, not chosen)
- Question: S01.3
- What was attempted: The research report describes bluez-alsa with `bluealsad` and `bluealsa-aplay` (bluetooth-a2dp-sink-research.md:54-86) and gives its status as "Viable but not recommended." (bluetooth-a2dp-sink-research.md:168). Reasons listed: "No automatic pairing/authorization. Must write/maintain Python D-Bus agent scripts for production use." (bluetooth-a2dp-sink-research.md:171), ALSA-only access, one application per stream, smaller user base (bluetooth-a2dp-sink-research.md:173-183). The architecture document keeps it as a swap option: "Design audio plane to allow swapping PipeWire for BlueZ-ALSA or PulseAudio if needed" (architecture-and-build-plan.md:1663), and its stack diagram reads "A2DP sink profile (bluez-alsa or native)" (architecture-and-build-plan.md:406). No BlueALSA call or package appears in code or installer (search for `bluealsa`, `bluez-alsa`: document hits only).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: STATED. Reasons at bluetooth-a2dp-sink-research.md:170-185.
- Depends on: The project scope listed at bluetooth-a2dp-sink-research.md:173-179 (visualisation, input switching, web playback, Chromecast).
- Open questions: Does any code in the audio plane provide the swap point described at architecture-and-build-plan.md:1663? (None was found: `AudioSource` has two capture branches, audioburger/audio_plane/audio_pipeline.py:195-201.)
- Confidence: MEDIUM. STATED in documents.

### R2-S01-08  Custom A2DP sink written against the BlueZ D-Bus API (evaluated on paper, rejected)
- Question: S01.3
- What was attempted: The research report rejects "Writing a custom A2DP sink implementation directly against BlueZ D-Bus API." (bluetooth-a2dp-sink-research.md:191), giving "Reinventing PipeWire/bluez-alsa" and "No benefit over using established solutions" among the reasons (bluetooth-a2dp-sink-research.md:194-197). The code on main uses the BlueZ D-Bus API for adapter and device control (record R2-S01-04) and holds no media endpoint, profile or transport code (search of all tracked files for `RegisterEndpoint`, `RegisterProfile`, `RegisterApplication`, `MediaEndpoint`, `MediaTransport`, `Media1`: no hit).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: STATED. bluetooth-a2dp-sink-research.md:193-197.
- Depends on: The availability of PipeWire or bluez-alsa to supply the sink (bluetooth-a2dp-sink-research.md:194).
- Open questions: NONE
- Confidence: MEDIUM. STATED in one document; code agrees that no media-side D-Bus code exists.

### R2-S01-09  Audio stack in code: numpy, scipy and sounddevice
- Question: S01.4
- What was attempted: The audio module imports numpy, `scipy.fft.fft`, `scipy.signal.resample_poly` and, under a guard, `sounddevice` (audioburger/audio_plane/audio_pipeline.py:13-15, :21-25). Versions are lower bounds: numpy>=1.24.0, scipy>=1.10.0, sounddevice>=0.4.6 (requirements.txt:11-13). `sounddevice` is used for line-in input only, through `sd.rec` on the default input device (audio_pipeline.py:210-216). No sound server is addressed by name in code. The system sound library that `sounddevice` binds to is not named in the repo and `install.sh` installs no audio system package (case-insensitive search for `portaudio`, `alsa`, `pipewire`, `pulse`: no hit). The installer-written unit orders after `sound.target` (install.sh:968).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Note N1 applies.
- Why it turned out that way: UNKNOWN.
- Depends on: A system audio library usable by `sounddevice`; a default input device; the `audio` group for the service user (install.sh:941-943).
- Open questions: Did `import sounddevice` succeed on any target? Which sound server was running underneath?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S01-10  PipeWire as the audio stack (design)
- Question: S01.4
- What was attempted: The architecture document names PipeWire as the audio routing framework (architecture-and-build-plan.md:15, :141, :147) with "PipeWire 0.3.50+" (architecture-and-build-plan.md:420, :1661). It also names FFmpeg or libsamplerate for resampling (architecture-and-build-plan.md:149, :287). The audio framework report gives "PipeWire (v0.3.65+, 2026)" (audio-framework-research-report.md:15). Code holds PipeWire in comments and docstrings only, for example "Future implementation will: - Configure PipeWire graph for selected source" (audioburger/audio_plane/audio_pipeline.py:361-362). README lists "PipeWire connection" as Sprint 2 work, "Requires the `pipewire` Python bindings or a subprocess wrapper around `pw-connect`" (README.md:261); no such binding is in requirements.txt (requirements.txt:1-32).
- Presence: DISABLED. Document only; comments in code, no calls (case-insensitive search of `audioburger/` for `pipewire`, `pw-`, `subprocess`: eight hits, all comments or docstrings, at a2dp_sink.py:8, :40 and audio_pipeline.py:46, :226, :229, :230, :362, :426).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire at or above the versions the documents name; "PipeWire availability confirmed on target distributions" is listed as a prerequisite (architecture-and-build-plan.md:2411).
- Open questions: Was the prerequisite at architecture-and-build-plan.md:2411 checked on any machine?
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

### R2-S01-11  GStreamer for processing (research report only)
- Question: S01.4
- What was attempted: The audio framework report states "PipeWire + GStreamer combination is recommended." (audio-framework-research-report.md:7) and sketches a pipeline `pipewiresrc → audioconvert → equalizer-10bands → spectrum → tee` (audio-framework-research-report.md:223-226). GStreamer does not appear in the architecture document, README or roadmap (case-insensitive search for `gstreamer` in those three files: no hit) and not in code or requirements (search of `audioburger/` for `gst`: no hit).
- Presence: DISABLED. Document only, in one research report.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No document says why the architecture document left GStreamer out.
- Depends on: NONE recorded beyond the report itself.
- Open questions: Was GStreamer dropped by decision, and where is that recorded?
- Confidence: MEDIUM. STATED in one document; absence elsewhere checked by search.

### R2-S01-12  Other external dependencies and their versions
- Question: S01.5
- What was attempted: Python packages, all as lower bounds: aiohttp>=3.9.0, tomli>=2.0.1, pychromecast>=14.0.0, sdnotify>=0.3.2, pytest>=7.2.0, pytest-asyncio>=0.21.0, pytest-aiohttp>=1.0.0 (requirements.txt:5, :8, :19, :22, :25-27). `setup.py` reads the same file into `install_requires` (setup.py:13-19, :38). npm packages: svelte ^4.2.0, vite ^5.0.0, @sveltejs/vite-plugin-svelte ^3.0.0, @picocss/pico ^2.0.0 (audioburger/web_ui/frontend/package.json:11-18), with exact resolutions in the lockfile (audioburger/web_ui/frontend/package-lock.json, lockfileVersion 3 at :4). System packages installed by the installer: Python and its venv, dev and pip packages (install.sh:382-407), Node.js, from the NodeSource `setup_20.x` script on apt systems (install.sh:409-440), compiler tools and pkg-config (install.sh:442-461). No exact Python pin exists; the only exact versions in the repo are the npm lockfile resolutions.
- Presence: CURRENT. requirements.txt reached its present content in eb02d59 ("Uncomment and add required dependencies in requirements.txt", commit eb02d59 message).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No install log is tracked. For the earlier state README.md:240 states that the audio, Bluetooth, Chromecast and test dependencies were commented out and "Only `aiohttp` and `tomli` are installed" (Note N2).
- Why it turned out that way: UNKNOWN.
- Depends on: Network access to PyPI, npm and NodeSource at install time (install.sh:421, :740, :817).
- Open questions: Was `pip install -r requirements.txt` run to completion after eb02d59 on any machine?
- Confidence: MEDIUM. INFERRED from clear manifests; no evidence of an install.

---

## S02 Adapter and controller

### R2-S02-01  Adapter chosen by configured name, powered on and given an alias
- Question: S02.1
- What was attempted: The adapter is not searched for. Its name comes from configuration: `adapter = "hci0"` (config.toml:6), default `"hci0"` (audioburger/config/config_manager.py:27), accepted only if it matches `^hci\d+$` (config_manager.py:290-292). `A2DPSink.start()` builds the object path `/org/bluez/<adapter>` (audioburger/bluetooth/a2dp_sink.py:95), takes `Adapter1` and `Properties` interfaces on it (a2dp_sink.py:97-99), sets `Powered` to true (a2dp_sink.py:106) and sets `Alias` to the literal `"AudioBurger"` (a2dp_sink.py:109). It then registers receivers for `InterfacesAdded` and `InterfacesRemoved` (a2dp_sink.py:112-122) and sets `_running` (a2dp_sink.py:124). `start()` is called once at start-up (audioburger/main.py:224). No other adapter property is written at start (no `Discoverable`, `Pairable` or class setting between a2dp_sink.py:105 and :124). The roadmap carries an earlier listing of the same approach without the alias line (PROJECT-COMPLETION-ROADMAP.md:387-416).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Note N1 applies: commit eb02d59 lists "Add adapter initialization and configuration" and names no run.
- Why it turned out that way: UNKNOWN.
- Depends on: `bluetooth.enabled = true` (config.toml:5; early return at a2dp_sink.py:76-78); a successful `import dbus` (a2dp_sink.py:80-82); a BlueZ daemon exposing the named adapter; permission for the service user to set adapter properties (record R2-S01-04).
- Open questions: Was `start()` ever run against a real adapter? Does the power-on call succeed for the `audioburger` service user?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S02-02  Stub `start()` that set a flag and logged
- Question: S02.1
- What was attempted: The first version of the module set `_running = True` and logged with `"status": "stub"`; its docstring listed BlueZ initialisation as future work (70fc363:audioburger/bluetooth/a2dp_sink.py:53-79). The module docstring read "CURRENT STATUS: STUB - No functionality implemented yet." (70fc363:audioburger/bluetooth/a2dp_sink.py:10).
- Presence: REMOVED. Replaced in commit eb02d59 (audioburger/bluetooth/a2dp_sink.py).
- Outcome: FAILED
- Outcome evidence: STATED. README.md:229: "All methods are stubs. `start()` sets `_running = True` and logs. No BlueZ, no D-Bus, no pairing, no audio reception." The code at 70fc363 agrees.
- Why it turned out that way: STATED. A placeholder by intent: commit 70fc363 subject "Implement Phase 4 Step 1: Foundation and Skeleton"; README.md:351.
- Depends on: NONE. The stub touched no adapter.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S02-03  One adapter, named `hciN`, assumed in code
- Question: S02.2
- What was attempted: The code holds one adapter path and one pair of adapter interfaces (audioburger/bluetooth/a2dp_sink.py:59-60, :95-99). The configuration accepts one name of the form `hci` plus digits (audioburger/config/config_manager.py:290-292), default `hci0` (config.toml:6). No code reads the adapter's model, chipset, address, firmware or Bluetooth version (search of `audioburger/` for `Modalias`, `Manufacturer`, `Version`, `chipset`, `firmware`: no hit in the Bluetooth module). The paired-device listing reads every `org.bluez.Device1` object on the bus and does not filter by adapter path (a2dp_sink.py:262-273).
- Presence: CURRENT. Added in eb02d59; the name pattern dates from b61e1ec (patch1.md:198).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: The adapter of interest holding the index named in configuration. No adapter model appears anywhere in code, configuration or scripts.
- Open questions: Which adapter, if any, was the code run with? On a host with two adapters, which one holds index 0?
- Confidence: MEDIUM. INFERRED from clear code.

### R2-S02-04  Adapter hardware statements in the documents
- Question: S02.2
- What was attempted: The requirement is "Support both onboard and USB Bluetooth adapters" (project-context.md:64). The research report states, from web sources, that Raspberry Pi onboard Bluetooth is unsuitable for A2DP and heads its mitigation "USB Bluetooth Dongle (Mandatory for Raspberry Pi)" (bluetooth-a2dp-sink-research.md:233-248, sources at :586-588); it names chipset families "Cambridge Silicon Radio, Realtek, Intel on USB" (bluetooth-a2dp-sink-research.md:245) and treats Intel onboard Bluetooth on x86 as suitable (bluetooth-a2dp-sink-research.md:260-261). It lists one adapter with a reported BlueZ regression, the TP-Link UB500 Plus (bluetooth-a2dp-sink-research.md:311). It leaves adapter selection with two adapters present as an open question (bluetooth-a2dp-sink-research.md:479-491). The architecture document plans tests "with multiple Bluetooth adapters (onboard, USB)" and a list of tested adapters (architecture-and-build-plan.md:1605-1606). A log example in the logging report shows `chipset="Intel AX200"` (logging-observability-report.md:120); it is a format example.
- Presence: DISABLED. Document only. No adapter list, no dongle handling and no `dtoverlay` or blacklist step exists in code or installer (search of install.sh and `audioburger/` for `dtoverlay`, `disable-bt`, `blacklist`, `dongle`: no hit).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The statements are quoted from external sources; no adapter is recorded as tried in this repo.
- Why it turned out that way: UNKNOWN.
- Depends on: The external sources listed at bluetooth-a2dp-sink-research.md:584-588.
- Open questions: Was any adapter, onboard or USB, used with this code? Was the list of tested adapters at architecture-and-build-plan.md:1606 ever started?
- Confidence: MEDIUM. STATED in documents; no artefact.

### R2-S02-05  Missing adapter, missing D-Bus binding and failures at start
- Question: S02.3
- What was attempted: Three branches at start. (1) If `dbus` did not import, the module logs a warning and returns (audioburger/bluetooth/a2dp_sink.py:80-82). (2) If taking the adapter object raises `DBusException`, it logs an error and "Bluetooth functionality will be limited. Check if BlueZ is running and adapter exists." and returns without raising (a2dp_sink.py:96-103); `_running` stays false and the bus handle stays set (a2dp_sink.py:92). (3) Any other exception, including one from the `Powered` or `Alias` writes, is logged and raised again (a2dp_sink.py:129-131), which propagates through `_start_components` (audioburger/main.py:242-244) to `run()`, which logs a critical error, stops components and returns exit code 1 (main.py:399-405). After start there is no handling: no `PropertiesChanged` subscription, no check of `Powered`, no retry and no re-initialisation (search of `audioburger/` for `PropertiesChanged`, `rfkill`, `Powered`: the only hit is the write at a2dp_sink.py:106). The `InterfacesRemoved` handler logs device removals only (a2dp_sink.py:168-171). With no adapter interface, `enable_discoverable` logs a warning and returns (a2dp_sink.py:180-182), and the HTTP handler still answers `{"success": True, "timeout": 120}` (audioburger/web_ui/api_server.py:508-510). The status endpoint reports Bluetooth "stopped" in that state (api_server.py:240-241). The watchdog health check does not look at Bluetooth (main.py:321-326).
- Presence: CURRENT. Added in eb02d59 and add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: The point at which dbus-python reports a missing object path. systemd restart policy on exit code 1: `Restart=on-failure`, `RestartSec=10s` (audioburger.service:17-18) or `Restart=always`, `RestartSec=5` (install.sh:976-977).
- Open questions: For an adapter path that does not exist, does the exception arise at a2dp_sink.py:97 (handled, start continues) or at the property write at :106 (raised, process exits with code 1)? What happens when the adapter is unplugged or blocked after start? No test of either is recorded.
- Confidence: MEDIUM. INFERRED from clear code; the branch taken for a missing adapter is not evidenced.

### R2-S02-06  Issue-list entries on adapter loss and Bluetooth health
- Question: S02.3
- What was attempted: The issue list written against the skeleton records H-2, "No Error Recovery for Component Failures", with the scenario "Bluetooth adapter temporarily unavailable (USB unplug/replug)" ending in exit code 1 and a full restart by systemd (fixes.md:570-581). It records M-1: "`bluetooth.is_running()` returns `True` even if BlueZ D-Bus connection is dead" (fixes.md:1094-1096). It proposes an error text for an adapter that is not found (fixes.md:1314) and a reliability check `sudo hciconfig hci0 down` with the expectation "Application should continue without Bluetooth" (fixes.md:1485-1490). The patch notes list H-2 and "M-1 through M-8" among issues not included (patch1.md:554-563). The release checklist line "Application survives 100 Bluetooth connect/disconnect cycles" is unticked (fixes.md:1544).
- Presence: CURRENT. The entries are open items in fixes.md on main; patch1.md:558 and :563 record them as deferred.
- Outcome: UNVERIFIED
- Outcome evidence: NONE for behaviour on a real adapter. The entries are an assessment of code at 70fc363 (fixes.md:5: "Phase 4, Step 1 Complete (Foundation & Skeleton)"), when the Bluetooth module was a stub; the checks at fixes.md:1485-1490 are written as steps to run, with no result.
- Why it turned out that way: STATED. Deferred because it "requires retry logic design" (patch1.md:558).
- Depends on: The code state at 70fc363. The code after eb02d59 handles one adapter failure differently (record R2-S02-05).
- Open questions: Was the `hciconfig hci0 down` check ever run? Does M-1 still describe the module after eb02d59, where `_running` is set once at start (audioburger/bluetooth/a2dp_sink.py:124) and cleared only in `stop()` (a2dp_sink.py:149)?
- Confidence: MEDIUM. STATED in documents; no run recorded.

### R2-S02-07  Design statements on a missing adapter in the research and logging documents
- Question: S02.3
- What was attempted: The research report leaves fallback on total Bluetooth failure as an open question with two options: show "Bluetooth unavailable" and carry on with line-in, or have systemd restart BlueZ (bluetooth-a2dp-sink-research.md:510-520). It also states the degrade rule "If BlueZ D-Bus unavailable, Web UI shows "Bluetooth unavailable" rather than crashing." (bluetooth-a2dp-sink-research.md:389). The logging report shows a different expected sequence for a missing adapter, ending "Fatal error during startup, exiting code=1" (logging-observability-report.md:497-502), and defines the code `BT_ADAPTER_NOT_FOUND` (logging-observability-report.md:746). Neither the code `BT_ADAPTER_NOT_FOUND` nor a "Bluetooth unavailable" state appears in `audioburger/` (search: no hit); the inputs endpoint reports Bluetooth with `"available": True` as a fixed value (audioburger/web_ui/api_server.py:428-430).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: Which of the two documented behaviours (continue without Bluetooth, or exit with code 1) was the intended one?
- Confidence: MEDIUM. STATED in documents; the two documents differ.

### R2-S02-08  Guide listing that marks the module as running when the adapter is absent
- Question: S02.3
- What was attempted: The code listing in the implementation guide sets `_running = True` when Bluetooth is disabled, when D-Bus is unavailable, when the adapter is not found and when start fails, with the comments "# Mark as running but inactive" and "# Still mark as running for graceful degradation" (AI-AGENT-IMPLEMENTATION-GUIDE.md:610-618, :634-637, :649-651). It wraps the power and alias writes in their own handler that logs a warning (AI-AGENT-IMPLEMENTATION-GUIDE.md:640-644). The module on main does none of this: it leaves `_running` false on those paths and raises on write errors (audioburger/bluetooth/a2dp_sink.py:76-82, :100-103, :129-131).
- Presence: DISABLED. Document only (Note N3). The guide was committed in cec17a7, twelve minutes before eb02d59; the code committed in eb02d59 differs from the listing.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No commit or comment says why the committed module departs from the listing.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. Read directly from the document and compared with code.

---

## S03 Discoverability, pairing and trust

### R2-S03-01  Discoverable and pairable mode switched on by an HTTP request
- Question: S03.1
- What was attempted: `enable_discoverable(timeout=120)` sets the adapter properties `Discoverable` true, `DiscoverableTimeout` to the timeout and `Pairable` true (audioburger/bluetooth/a2dp_sink.py:173-191). Its only caller is the handler for `POST /api/v1/bluetooth/pair`, which passes no argument and answers with the fixed value 120 (audioburger/web_ui/api_server.py:191, :506-510). Nothing calls it at start-up (a2dp_sink.py:70-131). The configured `discoverable_timeout = 120` (config.toml:7) is validated to 0-3600 (audioburger/config/config_manager.py:217-218, :293-295) and read by no other code (search of `audioburger/` for `discoverable_timeout`: hits in config_manager.py only). `Pairable` is never set back to false and no `PairableTimeout` is written (search of `audioburger/` for `Pairable`: one hit, a2dp_sink.py:187). `disable_discoverable()` exists (a2dp_sink.py:193-203) and has no caller (search for `disable_discoverable`: definition only). Name: the adapter `Alias` is the literal "AudioBurger" (a2dp_sink.py:109). Device class: not set or read anywhere (search of all tracked files for `Class of`, `DeviceClass`, `device class`: no hit). The tracked frontend has no control for pairing; its only request is `fetch('/api/v1/status')` (audioburger/web_ui/frontend/src/App.svelte:19).
- Presence: CURRENT. Function added in eb02d59, route added in add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Note N1 applies: commit eb02d59 lists "Add enable/disable discoverable mode" and names no run.
- Why it turned out that way: UNKNOWN.
- Depends on: An initialised adapter interface (a2dp_sink.py:180-182); a client able to reach the HTTP API, which has no authentication (fixes.md:31, deferred at patch1.md:556) and binds to `0.0.0.0` by default (config.toml:28).
- Open questions: Was the device ever seen as discoverable by a phone or other source? What device class and icon does a source see, given that the code sets neither? Does the handler's fixed `"success": True` hide a failed property write (a2dp_sink.py:190-191 swallows the exception)?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S03-02  Stub `enable_discoverable` and `disable_discoverable`
- Question: S03.1
- What was attempted: Both functions logged with `"status": "stub"` and did nothing more; the docstring listed "Set adapter to discoverable mode", "Configure pairing agent" and "Monitor for pairing requests" as future work (70fc363:audioburger/bluetooth/a2dp_sink.py:105-130).
- Presence: REMOVED. Replaced in commit eb02d59 (audioburger/bluetooth/a2dp_sink.py).
- Outcome: FAILED
- Outcome evidence: STATED. PROJECT-COMPLETION-ROADMAP.md:99: "`enable_discoverable()` - Does nothing". The code at 70fc363 agrees.
- Why it turned out that way: STATED. A placeholder by intent (commit 70fc363 subject; README.md:351).
- Depends on: NONE.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S03-03  Guide listing: timeout from configuration, pairable timeout, switch-off at stop
- Question: S03.1
- What was attempted: The listing in the implementation guide takes the timeout from `config.discoverable_timeout` when none is passed (AI-AGENT-IMPLEMENTATION-GUIDE.md:679-681), writes `PairableTimeout` as well as `DiscoverableTimeout` (AI-AGENT-IMPLEMENTATION-GUIDE.md:690-693), sets both `Discoverable` and `Pairable` false in `disable_discoverable` (AI-AGENT-IMPLEMENTATION-GUIDE.md:709-712) and calls that function from `stop()` (AI-AGENT-IMPLEMENTATION-GUIDE.md:673-674). It returns true, as a simulation, when D-Bus is unavailable (AI-AGENT-IMPLEMENTATION-GUIDE.md:685-687). None of these four points is in the module on main (record R2-S03-01).
- Presence: DISABLED. Document only (Note N3).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. Read directly from the document and compared with code.

### R2-S03-04  Pairing workflow in the architecture document
- Question: S03.1
- What was attempted: The architecture document draws the flow: the user presses "Pair New Device" in the Web UI, the control plane enables discoverable mode "(bluetoothctl: discoverable on)", the external device starts pairing, BlueZ receives the request, the device is trusted and the Web UI shows it (architecture-and-build-plan.md:435-469). Its API table gives `POST /bluetooth/pair` with response `{success, timeout}` (architecture-and-build-plan.md:1168). Its issue table has "Pairing Timeout | Extend timeout to 30s, display progress in UI" (architecture-and-build-plan.md:533). The code on main matches the route and the response shape (audioburger/web_ui/api_server.py:191, :510), uses D-Bus property writes and not `bluetoothctl` (audioburger/bluetooth/a2dp_sink.py:185-187), uses 120 seconds for discoverability (a2dp_sink.py:173) and has no pairing timeout, progress display or "Pair New Device" control (App.svelte:1-93).
- Presence: DISABLED. Document only for the parts not in code: the UI control, the 30-second pairing timeout and the trust step.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: A pairing agent, which the flow names at architecture-and-build-plan.md:455 (record R2-S03-06).
- Open questions: Which timeout does "30s" refer to, and how does it relate to the 120-second discoverable timeout in config.toml:7?
- Confidence: MEDIUM. STATED in one document and compared with code.

### R2-S03-05  No pairing agent registered by this project
- Question: S03.2
- What was attempted: The code sets `Pairable` true (audioburger/bluetooth/a2dp_sink.py:187) and registers no agent. Search of every tracked file for `RegisterAgent`, `Agent1`, `AgentManager`, `RequestDefaultAgent`, `RequestConfirmation`, `PinCode`, `Passkey`: no hit. No `.Pair(` call exists (search of `audioburger/`: no hit), so pairing can only be started from the remote device. Pairing method, confirmation and PIN behaviour are therefore not set by this project. The stub listed "Configure pairing agent" and "Monitor for pairing requests" as future work (70fc363:audioburger/bluetooth/a2dp_sink.py:112-115); those lines were removed in eb02d59 and nothing replaced them. README lists the agent as pending: "Implement `enable_discoverable()`, pairing agent confirmation, and `get_paired_devices()` (currently returns `[]`)." (README.md:271).
- Presence: CURRENT. The absence holds on main at 1d878c2.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No record of a pairing attempt exists in the repo.
- Why it turned out that way: UNKNOWN. No commit, comment or note says why eb02d59 left the agent out.
- Depends on: Whatever agent, if any, is registered with BlueZ on the host by other software. No such software is installed or configured by `install.sh` (search for `bluez`, `bt-agent`, `bluetoothctl`: no hit).
- Open questions: With no agent from this project, does an incoming pairing request succeed, fail or wait on the host's own agent? Was pairing from any source device ever tried?
- Confidence: MEDIUM. INFERRED from clear code and an exhaustive search; behaviour on a host is not evidenced.

### R2-S03-06  Agent that confirms pairing automatically (architecture workflow)
- Question: S03.2
- What was attempted: The pairing workflow diagram reads "BlueZ: Pairing Request Received" / "(Agent confirms pairing automatically)" and then "Control Plane: Device Trusted" (architecture-and-build-plan.md:453-462). The logging report's example log for a pairing attempt shows `method=JUST_WORKS` (logging-observability-report.md:412). No agent code exists (record R2-S03-05).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: The same document's security section states the opposite rule (record R2-S03-07).
- Open questions: Which of the two statements in architecture-and-build-plan.md (automatic confirmation at :455, user confirmation at :1435) was the approved design?
- Confidence: MEDIUM. STATED in one document; contradicted within the same document.

### R2-S03-07  Pairing confirmed by the user in the Web UI (security design)
- Question: S03.2
- What was attempted: The research report describes a backend-run BlueZ agent that forwards each pairing request to the Web UI over WebSocket and answers BlueZ after the user confirms (bluetooth-a2dp-sink-research.md:461-471), noting that example implementations "use Python scripts with auto-accept" (bluetooth-a2dp-sink-research.md:465) and leaving the timeout open: "Timeout handling if user doesn't respond? Auto-reject after timeout?" (bluetooth-a2dp-sink-research.md:473). The architecture security section states "Untrusted devices cannot auto-pair without user confirmation in Web UI" (architecture-and-build-plan.md:1435) and gives a BlueZ `main.conf` fragment with `TrustOnFirstUse=false` (architecture-and-build-plan.md:1438-1447). The WebSocket handler on main parses no commands: text messages are logged and dropped (audioburger/web_ui/api_server.py:402-405). No `main.conf` is tracked or written by the installer (search of install.sh, `*.py`, `*.toml`, `*.service` for `main.conf`: no hit).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. The research report marks it "Needs: Web UI and Backend design phase." (bluetooth-a2dp-sink-research.md:475).
- Depends on: A registered agent, a WebSocket command channel and a UI dialog, none of which exists in code.
- Open questions: Is `TrustOnFirstUse` an option of the BlueZ version in use? The repo gives no source for it.
- Confidence: MEDIUM. STATED in two documents; code agrees that nothing implements it.

### R2-S03-08  Paired devices listed from BlueZ; no storage, trust or removal in this project
- Question: S03.3
- What was attempted: Listing: `get_paired_devices()` calls `GetManagedObjects` on the BlueZ object manager and returns, for every `org.bluez.Device1` object with `Paired` true, its address, name, `Connected` flag and object path (audioburger/bluetooth/a2dp_sink.py:244-278). The class defines the name twice, first as `async def` (a2dp_sink.py:205-242) and then as a plain `def` (a2dp_sink.py:244); all three call sites call it without `await` (a2dp_sink.py:298, :340; audioburger/web_ui/api_server.py:501). `GET /api/v1/bluetooth/devices` returns the list (api_server.py:190, :498-504). Storage: the project writes no device record; the list `_paired_devices` is created empty and never written (a2dp_sink.py:53; search for `_paired_devices`: one hit). Trust: the `Trusted` property is neither read nor set (search of `audioburger/` for `Trusted`: no hit), and the `BluetoothDevice` class with a `trusted` field is never instantiated (a2dp_sink.py:25-32; search for `BluetoothDevice(`: no hit). Removal: no `RemoveDevice` call and no route for it (search of `audioburger/`: no hit; routes at api_server.py:190-192). The device name is returned as received (a2dp_sink.py:270).
- Presence: CURRENT. Function added in eb02d59, route in add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Note N1 applies: commit eb02d59 lists "Implement get_paired_devices with D-Bus queries" and names no run.
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ keeping the pairing records; the bus handle, which is set before the adapter is looked up (a2dp_sink.py:92) so the listing can run with no adapter interface.
- Open questions: Was the endpoint ever called with a paired device present? How is a device unpaired on this product, given no removal path in code?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S03-09  Stub `get_paired_devices` that returned an empty list
- Question: S03.3
- What was attempted: The first version logged and returned `[]`; its docstring listed "Query BlueZ for paired devices" as future work (70fc363:audioburger/bluetooth/a2dp_sink.py:132-146).
- Presence: REMOVED. Replaced in commit eb02d59 (audioburger/bluetooth/a2dp_sink.py).
- Outcome: FAILED
- Outcome evidence: STATED. PROJECT-COMPLETION-ROADMAP.md:100: "`get_paired_devices()` - Returns `[]`". The code at 70fc363 agrees.
- Why it turned out that way: STATED. A placeholder by intent (commit 70fc363 subject; README.md:351).
- Depends on: NONE.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S03-10  Guide listing: trust flag read, trust set on connect, device removal
- Question: S03.3
- What was attempted: The listing in the implementation guide reads `Trusted` into each listed device (AI-AGENT-IMPLEMENTATION-GUIDE.md:743-751), sets `Trusted` true on the device just before `Connect()` inside a bare `try/except: pass` (AI-AGENT-IMPLEMENTATION-GUIDE.md:791-795) and adds `remove_device()`, which calls `Adapter1.RemoveDevice` on a path built from the address (AI-AGENT-IMPLEMENTATION-GUIDE.md:843-861). Its route list adds `POST /api/v1/bluetooth/disconnect` and no removal route (AI-AGENT-IMPLEMENTATION-GUIDE.md:1341-1345). None of this is in the module on main (record R2-S03-08).
- Presence: DISABLED. Document only (Note N3).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. Read directly from the document and compared with code.

### R2-S03-11  Trust and device-limit statements in the architecture document and issue list
- Question: S03.3
- What was attempted: The architecture document gives the line `device.trust()` with the comment that it is called only after the user clicks "Trust Device" in the Web UI (architecture-and-build-plan.md:1449-1453) and lists "Trust/untrust devices" as a Sprint 3 task (architecture-and-build-plan.md:1916). The issue list notes "No limit on Bluetooth paired devices (future implementation)" and proposes `max_paired_bluetooth_devices = 20` (fixes.md:527, :540); that item, H-1, is listed as not included in the patch (patch1.md:557). No `[limits]` section and no trust control exist in configuration or code (config.toml:1-35; search of `audioburger/` for `max_paired`, `trust(`: no hit).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: STATED for the limit: deferred because it "requires monitoring implementation" (patch1.md:557). UNKNOWN for the trust control.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

### R2-S03-12  Second device: no rule in code
- Question: S03.4
- What was attempted: The code holds no rule for a second device. Incoming pairing or connection from another device is not observed: the `InterfacesAdded` handler logs the new device's path, name and address and does nothing more (audioburger/bluetooth/a2dp_sink.py:157-166). For an outgoing connect, `connect_device` calls `Connect()` on the chosen device and overwrites `_connected_device` without disconnecting the earlier one (a2dp_sink.py:300-311). `Pairable` stays true once set (record R2-S03-01), so a second device meets the same conditions as the first. No count of connected devices is kept (search of `audioburger/bluetooth/` for `len(`, `count`: one hit, the log line at a2dp_sink.py:237).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ and any audio backend on the host deciding what happens when a second source connects.
- Open questions: What happens on a host when a second source connects while the first is streaming? No test with two devices is recorded.
- Confidence: MEDIUM. INFERRED from clear code; behaviour on a host is not evidenced.

### R2-S03-13  Single active device policy (design)
- Question: S03.4
- What was attempted: The architecture document's issue table has "Multiple Devices Connecting Simultaneously" with the answer "Enforce single active device policy, auto-disconnect others" (architecture-and-build-plan.md:534), and its activation steps disconnect the current active device before connecting the selected one (architecture-and-build-plan.md:481-485). The research report leaves the behaviour open: "If Device A connected and Device B attempts connection, behavior depends on configuration (auto-disconnect A, or reject B)." (bluetooth-a2dp-sink-research.md:533) and marks it "Needs: UX design decision (Phase 3)." (bluetooth-a2dp-sink-research.md:542). The reliability plan lists "Multiple Bluetooth devices connecting simultaneously" as a stress test (architecture-and-build-plan.md:2155).
- Presence: DISABLED. Document only; the disconnect-before-connect step is not in `connect_device` (audioburger/bluetooth/a2dp_sink.py:296-311).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The stress test is a plan entry with no result.
- Why it turned out that way: UNKNOWN.
- Depends on: The UX decision named at bluetooth-a2dp-sink-research.md:542, for which no record was found.
- Open questions: Was the Phase 3 decision on device switching made, and where is it recorded?
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

### R2-S03-14  Who may pair: gated only by the pairing-mode HTTP request
- Question: S03.5
- What was attempted: The one control in code is the act of switching on pairable mode through `POST /api/v1/bluetooth/pair` (audioburger/web_ui/api_server.py:191, :506-510). The API has no authentication (fixes.md:31, "C-1: No Authentication on Web API"; listed as not included at patch1.md:556) and binds to `0.0.0.0` by default (config.toml:28). There is no allow list, no block list, no PIN, no confirmation step and no agent (record R2-S03-05; search of `audioburger/` for `allow`, `whitelist`, `blocked`, `Blocked`: no hit in the Bluetooth module). Once set, `Pairable` is not cleared by this code (record R2-S03-01).
- Presence: CURRENT. Added in eb02d59 and add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: Network reachability of port 8080 (config.toml:27); the host's own BlueZ agent and policy.
- Open questions: After one pairing request, for how long can further devices pair? Does BlueZ's own pairable timeout on the host bound it?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S03-15  Explicit trust model and `bluetooth_auto_pair` setting (design)
- Question: S03.5
- What was attempted: The requirement reads "Explicit trust model for Bluetooth devices" (project-context.md:164). The architecture document answers with: only paired devices may connect, pairing needs a user action, and a `[security]` configuration sample holding `bluetooth_auto_pair = false  # Require explicit user action` and, under logging, `log_bluetooth_pairing_attempts = true` (architecture-and-build-plan.md:1431-1436, :1524-1537). Its exposure table gives "Explicit pairing, trusted devices only" for Bluetooth (architecture-and-build-plan.md:1518). The research report adds a WirePlumber setting limiting roles to A2DP sink as a way to reduce exposure (bluetooth-a2dp-sink-research.md:402). A planned security test reads "Verify untrusted devices cannot connect" (architecture-and-build-plan.md:2136). Neither configuration key exists in config.toml or in the configuration classes (config.toml:1-35; audioburger/config/config_manager.py:23-78; search of `audioburger/` and config.toml for `auto_pair`: no hit; `security` appears only in version comments such as audioburger/main.py:11, and config.toml has no `[security]` section).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The security test is a plan entry with no result.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

---

## S04 Connection and A2DP profile

### R2-S04-01  No A2DP sink registration in the module named "A2DP Sink"
- Question: S04.1
- What was attempted: The class `A2DPSink` connects to BlueZ, powers the adapter and sets its alias (audioburger/bluetooth/a2dp_sink.py:87-109). It registers no media endpoint, profile or application and adds no service UUID. Search of every tracked file for `RegisterEndpoint`, `RegisterProfile`, `RegisterApplication`, `MediaEndpoint`, `MediaTransport`, `Media1`, `0000110b`: no hit. `UUID` appears once in code, in a Chromecast docstring (audioburger/chromecast/chromecast_output.py:288). Nothing in the installer enables a sink role either (case-insensitive search of install.sh for `bluez`, `pipewire`, `wireplumber`, `pulse`, `alsa`: no hit). The stub's docstring listed "Configure PipeWire Bluetooth backend" and "Advertise device as A2DP sink" as future work (70fc363:audioburger/bluetooth/a2dp_sink.py:57-61); eb02d59 replaced that docstring with "Initializes BlueZ D-Bus connection and configures adapter." (a2dp_sink.py:74). The six items under "PHASE 3: Bluetooth A2DP Sink Implementation" in commit eb02d59 name D-Bus integration, adapter initialisation, signal handlers, discoverable mode, paired-device listing and connect/disconnect; none names the sink role. The inputs endpoint reports "Bluetooth A2DP" as available with a fixed value (audioburger/web_ui/api_server.py:429).
- Presence: CURRENT. The absence holds on main at 1d878c2.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Nothing records whether a sink role was present on any host. Note N1 applies to the commit's general statement.
- Why it turned out that way: UNKNOWN. The documents assign the sink role to PipeWire (record R2-S04-02); no file says how that was to be set up for this product.
- Depends on: Some other software on the host (PipeWire with WirePlumber, per the design) registering the A2DP sink endpoint with BlueZ. The pre-implementation roadmap listed "A2DP profile activation" and "Audio stream reception" as missing (PROJECT-COMPLETION-ROADMAP.md:107-108, Note N2).
- Open questions: On any machine where this code ran, did a source device see an audio sink? Which software provided it?
- Confidence: MEDIUM. INFERRED from clear code and an exhaustive search; host behaviour is not evidenced.

### R2-S04-02  Sink role through a PipeWire Bluetooth configuration file (architecture document)
- Question: S04.1
- What was attempted: The architecture document lists "Automatic A2DP Sink: Advertises device as Bluetooth audio receiver" as a PipeWire feature and gives a configuration fragment for `/etc/pipewire/bluetooth.conf`: `bluez5.roles = [ a2dp_sink ]`, `bluez5.codecs = [ sbc aac ldac aptx aptx_hd ]`, `bluez5.autoswitch = true` (architecture-and-build-plan.md:422-433). Sprint 3 lists "Configure PipeWire Bluetooth backend" and "Advertise device as A2DP sink" as tasks (architecture-and-build-plan.md:1910-1911). No such file is tracked and the installer writes none (`git ls-files`; search of install.sh for `pipewire`: no hit).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: PipeWire 0.3.50+ (architecture-and-build-plan.md:420). The file path and key syntax differ from the research report's fragment (record R2-S04-03).
- Open questions: Was the fragment ever placed on a machine? Which of the two documented paths and syntaxes matches the PipeWire and WirePlumber versions in use?
- Confidence: MEDIUM. STATED in one document.

### R2-S04-03  Sink role through a WirePlumber Lua configuration (research report)
- Question: S04.1
- What was attempted: The research report states that WirePlumber activates the A2DP sink profile automatically and gives the path `/etc/wireplumber/bluetooth.lua.d/50-bluez-config.lua` with `["bluez5.roles"] = "[ a2dp_sink a2dp_source ]"` (bluetooth-a2dp-sink-research.md:38-41). Elsewhere it states the configuration "restricts to A2DP sink only (no HFP/HSP, no A2DP source)" (bluetooth-a2dp-sink-research.md:402); the fragment at :40 includes `a2dp_source`. It cites a Raspberry Pi report of "a2dp-sink profile connect failed: Protocol not available" when the PipeWire services were inactive (bluetooth-a2dp-sink-research.md:253-254). No such file is tracked and the installer writes none.
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The cited failure is from an external forum (bluetooth-a2dp-sink-research.md:587), not from this repo.
- Why it turned out that way: UNKNOWN.
- Depends on: WirePlumber with Lua configuration; the report's own reference is to "WirePlumber 0.5.13 documentation" (bluetooth-a2dp-sink-research.md:561).
- Open questions: Which role list was intended, the one at bluetooth-a2dp-sink-research.md:40 or the one described at :402?
- Confidence: MEDIUM. STATED in one document; the document is not consistent with itself on the role list.

### R2-S04-04  Codecs left to PipeWire, SBC as the baseline (design)
- Question: S04.2
- What was attempted: No code offers, selects or reports a codec (search of `audioburger/` for `codec`, `sbc`, `aptx`, `ldac`, `SelectConfiguration`, `SetConfiguration`: no hit; `aac` and `flac` appear only as Chromecast output formats, audioburger/config/config_manager.py:49, :246). The documents place negotiation in PipeWire: "Accept all codecs PipeWire offers" with SBC as the mandatory baseline and "No custom codec work needed" (bluetooth-a2dp-sink-research.md:223-225); the architecture fragment lists `sbc aac ldac aptx aptx_hd` (architecture-and-build-plan.md:431); its issue table has "Codec Negotiation Failures" answered by "Default to SBC (universal), log codec selection for diagnostics" (architecture-and-build-plan.md:532); Sprint 3 lists "Handle codec negotiation (SBC, AAC)" (architecture-and-build-plan.md:1921). The logging report asks for codec results at DEBUG and defines `BT_CODEC_NEGOTIATION_FAILED` (logging-observability-report.md:113, :750). The research report notes codec support depends on adapter hardware and on the source device (bluetooth-a2dp-sink-research.md:217-219).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: A PipeWire Bluetooth backend on the host (record R2-S01-05); the adapter and source in use.
- Open questions: Which codec, if any, was ever negotiated with a source device? Nothing in the repo records one.
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

### R2-S04-05  Connection handling: outgoing connect on request, disconnect at shutdown
- Question: S04.3
- What was attempted: Connect: `connect_device(address)` finds the address among paired devices and calls `Device1.Connect()`, then stores the address in `_connected_device` and returns true (audioburger/bluetooth/a2dp_sink.py:280-311); it does not read `Connected` or any profile state afterwards. It is reached from `PUT /api/v1/bluetooth/connect` (audioburger/web_ui/api_server.py:192, :513-527). Disconnect: `disconnect_device` calls `Device1.Disconnect()` (a2dp_sink.py:323-354); its only caller is `stop()`, for the stored address (a2dp_sink.py:146-147); no route exposes it (api_server.py:190-192). Incoming connect, disconnect and out of range: not observed. The only subscriptions are `InterfacesAdded` and `InterfacesRemoved` (a2dp_sink.py:112-122) and their handlers log only (a2dp_sink.py:157-171); there is no `PropertiesChanged` subscription (search: no hit). `DBusGMainLoop(set_as_default=True)` is called (a2dp_sink.py:89) and no GLib main loop is created or run anywhere (search of `audioburger/` for `GLib`, `MainLoop`, `gi.repository`: only the import and call at a2dp_sink.py:18-19, :89), while the process runs on `asyncio.run` (audioburger/main.py:438). Reconnect: `reconnect_attempts = 5` and `reconnect_backoff_base = 2.0` are in configuration (config.toml:8-9) and validated (audioburger/config/config_manager.py:214-221, :296-301) but read by no other code (search of `audioburger/`: hits in config_manager.py only). Reconnect after reboot: no connect call at start (a2dp_sink.py:70-131). The Bluetooth module does not notify the audio pipeline of any connection event (callbacks wired at main.py:196-198 are audio outputs and EQ only).
- Presence: CURRENT. Added in eb02d59 and add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Note N1 applies: commit eb02d59 lists "Add connect/disconnect device functionality" and "Implement device discovery with signal handlers" and names no run.
- Why it turned out that way: UNKNOWN.
- Depends on: A paired device; BlueZ's own reconnect policy on the host; delivery of D-Bus signals to the handlers.
- Open questions: Are the two signal handlers ever invoked, given that no GLib main loop is run? Does `Connect()` from the sink side bring up an A2DP stream with any tested source? What does the product do when a source goes out of range and returns? No run covers any of these.
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S04-06  Stub `connect_device` and `disconnect_device`
- Question: S04.3
- What was attempted: The first version logged and returned `False` for connect and `True` for disconnect; the connect docstring listed "Wait for A2DP profile activation" and "Configure audio routing to PipeWire" as future work (70fc363:audioburger/bluetooth/a2dp_sink.py:148-183).
- Presence: REMOVED. Replaced in commit eb02d59 (audioburger/bluetooth/a2dp_sink.py).
- Outcome: FAILED
- Outcome evidence: STATED. PROJECT-COMPLETION-ROADMAP.md:101: "`connect_device()` - Returns `False`". The code at 70fc363 agrees.
- Why it turned out that way: STATED. A placeholder by intent (commit 70fc363 subject; README.md:351).
- Depends on: NONE.
- Open questions: NONE
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S04-07  Automatic reconnection with exponential backoff (design and unread settings)
- Question: S04.3
- What was attempted: The architecture document specifies reconnection after a disconnect signal: wait 2 s, retry, then 4, 8, 16, 32 s, "max 5 attempts", then notify the user (architecture-and-build-plan.md:488-526, :1601). Its acceptance criterion is "Successful reconnection in 90% of cases within 30 seconds" (architecture-and-build-plan.md:1610). A BlueZ `main.conf` fragment in the same document carries `ReconnectAttempts=3` and `ReconnectIntervals=1,2,4` (architecture-and-build-plan.md:1440-1443). The module docstring on main still lists "Device reconnection with exponential backoff" (audioburger/bluetooth/a2dp_sink.py:7). The matching settings exist and are validated (config.toml:8-9; audioburger/config/config_manager.py:29-30, :214-221) and nothing reads them; README records this: "Use `reconnect_attempts` and `reconnect_backoff_base` from config (defined but unused)." (README.md:273).
- Presence: DISABLED. The settings are on main and unreachable by any reconnect code; the logic is document only (search of `audioburger/bluetooth/` for `reconnect`, `backoff`, `sleep`: one hit, the docstring at a2dp_sink.py:7).
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The 90% figure is an acceptance criterion with no measurement behind it.
- Why it turned out that way: UNKNOWN. Commit eb02d59 does not list reconnection among its Phase 3 items.
- Depends on: A disconnect signal reaching the code (record R2-S04-05).
- Open questions: NONE
- Confidence: HIGH. STATED at README.md:273 and the code agrees that the settings are unread.

### R2-S04-08  BlueZ reconnection problems listed from external reports
- Question: S04.3
- What was attempted: The research report lists five BlueZ problems said to affect every backend: a profile missing after reboot or reconnection (kernel 6.9.3, 6.8.12; BlueZ 5.59+), automatic connection with no audio, automatic reconnect that blocks device switching, failures after suspend and resume, and a regression in BlueZ 5.83 and 5.84 on automatic connection at start-up (bluetooth-a2dp-sink-research.md:274-312, sources at :571-577). It lists two WirePlumber problems, duplicate instances and cache corruption (bluetooth-a2dp-sink-research.md:314-326), and five operating measures including monitoring of BlueZ signals and a "Reconnect" button (bluetooth-a2dp-sink-research.md:330-340). The architecture document rates "Bluetooth A2DP Reconnection Reliability" as severity high, probability high (architecture-and-build-plan.md:1586-1588). None of the five measures is in code: no signal-driven reconnect (record R2-S04-05) and no reconnect control in the frontend (audioburger/web_ui/frontend/src/App.svelte:1-93).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Every listed problem is taken from an external issue tracker or forum; none is recorded as seen with this code.
- Why it turned out that way: UNKNOWN.
- Depends on: The kernel and BlueZ versions named at bluetooth-a2dp-sink-research.md:281, :310.
- Open questions: Did any of the listed problems occur on a machine running this product?
- Confidence: MEDIUM. STATED in documents from external sources.

### R2-S04-09  Guide listing: reconnect task declared and never created
- Question: S04.3
- What was attempted: The listing in the implementation guide declares `_reconnect_task` (AI-AGENT-IMPLEMENTATION-GUIDE.md:600) and cancels it in `stop()` (AI-AGENT-IMPLEMENTATION-GUIDE.md:660-667). No line in the listing assigns a task to it and the listing holds no reconnect logic (AI-AGENT-IMPLEMENTATION-GUIDE.md:583-882). The listing also returns true from `connect_device` as a simulation when D-Bus is unavailable (AI-AGENT-IMPLEMENTATION-GUIDE.md:764-766). The module on main has no such attribute (audioburger/bluetooth/a2dp_sink.py:43-68).
- Presence: DISABLED. Document only (Note N3).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. Read directly from the document and compared with code.

### R2-S04-10  Multiple sources: one stored address, no switching or priority logic
- Question: S04.4
- What was attempted: The module stores one connected address as a string (audioburger/bluetooth/a2dp_sink.py:61). A second `connect_device` call connects the new device and overwrites the stored address; the earlier device is not disconnected and is no longer tracked, so `stop()` disconnects only the last one (a2dp_sink.py:146-147, :308-309). The attribute `_active_device` is created and never used (a2dp_sink.py:54; search of `audioburger/bluetooth/`: one hit). No code ranks devices, selects among connected devices or limits their number. The audio pipeline has a single Bluetooth source value with no device identity (audioburger/audio_plane/audio_pipeline.py:28-32).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ and the host audio backend for what happens with two connected sources.
- Open questions: With two sources connected, which one is heard? No run with two sources is recorded.
- Confidence: MEDIUM. INFERRED from clear code.

### R2-S04-11  Active device selection among paired devices (design)
- Question: S04.4
- What was attempted: The requirement is "Allow selection of which paired Bluetooth device is currently active" (project-context.md:63). The architecture document states that several devices may be paired but "only one can stream audio at a time", with selection in the Web UI: list, select, "Activate", disconnect the current device, connect the selected one, wait for the A2DP profile, route to PipeWire (architecture-and-build-plan.md:473-486). The research report states "Only one A2DP connection at a time (limitation of A2DP profile, not BlueZ)." (bluetooth-a2dp-sink-research.md:532) and leaves active switching versus passive display open (bluetooth-a2dp-sink-research.md:540). The audio framework report asks whether to connect to the last device at start-up and whether a higher-priority device takes over (audio-framework-research-report.md:574-577). The research report records an external issue in which BlueZ automatic reconnect prevents moving a source between devices (bluetooth-a2dp-sink-research.md:292-296). On main the list and connect routes exist (audioburger/web_ui/api_server.py:190, :192); the UI, the disconnect-first step and the wait for profile activation do not (record R2-S04-10; App.svelte:1-93).
- Presence: DISABLED. Document only for selection, priority and switching.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. Both documents leave the matter as open questions (bluetooth-a2dp-sink-research.md:540-542; audio-framework-research-report.md:569-579) and no decision record was found.
- Depends on: NONE recorded.
- Open questions: Were the questions at audio-framework-research-report.md:574-577 answered anywhere?
- Confidence: MEDIUM. STATED in documents.

---

## S05 Audio capture

### R2-S05-01  Bluetooth capture function that returns silence
- Question: S05.1
- What was attempted: The processing loop calls `_capture_audio()` (audioburger/audio_plane/audio_pipeline.py:165), which calls `_capture_bluetooth()` when the active source is Bluetooth (audio_pipeline.py:199-200). That function returns `np.zeros((buffer_size, channels), dtype=np.float32)` (audio_pipeline.py:232). No audio leaves the Bluetooth stack into this project at any point: there is no read from BlueZ, PipeWire, PulseAudio, ALSA, a socket or a file descriptor for the Bluetooth source (search of `audioburger/` for `MediaTransport`, `Acquire`, `bluez_input`, `bluez_source`, `pw-record`, `pw-cat`, `parec`, `bluealsa`, `subprocess`: one hit, the comment at audio_pipeline.py:230. The word `socket` appears only in WebSocket code and in a local-IP lookup, audioburger/chromecast/chromecast_output.py:13, :414). The comment names the intended point: a PipeWire node named `bluez_input.` followed by the device address (audio_pipeline.py:230). The Bluetooth module passes no audio and no event to the pipeline (audioburger/main.py:196-198). The same approach, zeros for Bluetooth, is in the roadmap listing that preceded the commit (PROJECT-COMPLETION-ROADMAP.md:356-359). The stub before it had no capture function (70fc363:audioburger/audio_plane/audio_pipeline.py:1-140).
- Presence: CURRENT. Added in eb02d59; reached only after the source is set to Bluetooth through `PUT /api/v1/input` (audioburger/web_ui/api_server.py:457-458).
- Outcome: FAILED
- Outcome evidence: STATED. The function's docstring: "For now, this is a placeholder that returns silence." (audio_pipeline.py:227) and the comment "TODO: Implement Bluetooth audio capture via PipeWire" (audio_pipeline.py:229). The code agrees (audio_pipeline.py:232). The opposite statement in commit eb02d59, "Capture audio from line-in or Bluetooth A2DP sources" (Note N1), is contradicted by the code committed in that same commit.
- Why it turned out that way: STATED. Not implemented: the TODO at audio_pipeline.py:229, and "This requires PipeWire setup" in the listing it follows (PROJECT-COMPLETION-ROADMAP.md:358).
- Depends on: A PipeWire Bluetooth source node existing on the host, which nothing in the repo sets up (record R2-S04-01).
- Open questions: Was any method of reading the PipeWire node chosen? No document names one beyond the node name pattern.
- Confidence: HIGH. STATED evidence that the code agrees with.

### R2-S05-02  Guide listing: capture through a sound device whose name mentions Bluetooth
- Question: S05.1
- What was attempted: The listing in the implementation guide adds `_start_bluetooth_capture()`: it lists devices with `sd.query_devices()`, takes the first device with input channels whose lower-cased name contains `bluetooth`, `bluez` or `a2dp`, and opens `sd.InputStream` on it with a callback that copies each block into a queue (AI-AGENT-IMPLEMENTATION-GUIDE.md:309-351). When no device matches, `device_id` is `None` and the stream opens on the default input (AI-AGENT-IMPLEMENTATION-GUIDE.md:337-339). Its comment reads "Bluetooth audio is routed through PipeWire/PulseAudio" / "We capture from the virtual Bluetooth audio device" (AI-AGENT-IMPLEMENTATION-GUIDE.md:311-312). The stream is requested at `config.sample_rate`, `config.channels`, `float32`, block size `config.buffer_size` (AI-AGENT-IMPLEMENTATION-GUIDE.md:338-345). The module on main does not contain this function (audioburger/audio_plane/audio_pipeline.py:222-232).
- Presence: DISABLED. Document only (Note N3). Committed in cec17a7; the code committed twelve minutes later in eb02d59 uses the zeros placeholder.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. No commit or comment says why eb02d59 did not use the listing.
- Depends on: The host sound layer exposing the Bluetooth source as an input device with one of the three words in its name.
- Open questions: Does any host expose a Bluetooth source to `sounddevice` under such a name? Nothing in the repo shows a device list.
- Confidence: MEDIUM. Read directly from the document and compared with code.

### R2-S05-03  Capture from a PipeWire source node (design)
- Question: S05.1
- What was attempted: The architecture document states "Incoming Bluetooth audio captured via BlueZ, routed to PipeWire as virtual audio source" (architecture-and-build-plan.md:280) and draws BlueZ feeding a "Bluetooth audio source node" in PipeWire, which "Routes to audio processing pipeline" (architecture-and-build-plan.md:411-415). Sprint 2 lists "Research PipeWire Python bindings (PyPipeWire or D-Bus)" (architecture-and-build-plan.md:1872) and Sprint 3 lists "Receive A2DP audio stream from BlueZ/PipeWire" (architecture-and-build-plan.md:1919). The audio framework report places the entry point at a GStreamer `pipewiresrc` element (audio-framework-research-report.md:221-224). README lists "A2DP stream reception" as pending: "Receive SBC/AAC audio from paired devices and route into PipeWire." (README.md:272).
- Presence: DISABLED. Document only (record R2-S05-01 for the code).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN. The binding to use is left as a research task (architecture-and-build-plan.md:1872) and no result of that task was found.
- Depends on: PipeWire with its Bluetooth backend on the host.
- Open questions: Was the Sprint 2 research on PipeWire bindings done, and what did it find?
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

### R2-S05-04  Format at the capture point in code
- Question: S05.2
- What was attempted: The placeholder buffer is PCM `float32`, shape `(buffer_size, channels)` (audioburger/audio_plane/audio_pipeline.py:232), which with the shipped settings is 1024 frames by 2 channels (config.toml:13-14). The nominal rate is `audio.sample_rate`, 48000 as shipped (config.toml:12), limited to 44100, 48000 or 96000 (audioburger/config/config_manager.py:224-225). The pipeline takes the source rate from that setting, not from the stream: `_source_sample_rate = config.sample_rate` (audio_pipeline.py:72), and resamples only when it differs from 48000 (audio_pipeline.py:169-170). No encoded audio is handled; there is no decoder in the module (case-insensitive search of `audioburger/` for `sbc`, `codec`: no hit; `decode` appears only in the exception names `TOMLDecodeError` and `JSONDecodeError`, audioburger/config/config_manager.py:279, audioburger/web_ui/api_server.py:295). Later conversion to 16-bit integers happens at the output step (audio_pipeline.py:321, :337).
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The format of real Bluetooth audio at this point is not evidenced, because the function generates its own buffer (audio_pipeline.py:227, :232).
- Why it turned out that way: UNKNOWN.
- Depends on: `audio.sample_rate`, `audio.buffer_size`, `audio.channels` (config.toml:12-14).
- Open questions: How was the rate of an incoming A2DP stream (the documents expect 44.1 kHz, record R2-S05-05) meant to reach `_source_sample_rate`, which is fixed from configuration?
- Confidence: MEDIUM. INFERRED from clear code.

### R2-S05-05  Format expected at the capture point (documents)
- Question: S05.2
- What was attempted: The architecture document gives "Variable (44.1 kHz, 48 kHz, 96 kHz depending on source)" for capture (architecture-and-build-plan.md:283), "Variable sample rate from Bluetooth (44.1 kHz typical)" as resampler input and "48 kHz, 16-bit PCM" as its output (architecture-and-build-plan.md:602-604). The logging report's example line for stream start shows `codec=SBC bitpool=53 freq=44100` (logging-observability-report.md:122); it is a format example. Under the design, decoding from the Bluetooth codec to PCM happens inside PipeWire before the project's code (architecture-and-build-plan.md:403-415).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. No recorded stream format exists in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: The source device and negotiated codec (bluetooth-a2dp-sink-research.md:217-219).
- Open questions: NONE
- Confidence: MEDIUM. STATED in documents.

### R2-S05-06  Stream start and stop set by HTTP requests, not detected from Bluetooth
- Question: S05.3
- What was attempted: Nothing detects the start or stop of a Bluetooth stream. The loop processes audio whenever `_active_source` is not `NONE` (audioburger/audio_plane/audio_pipeline.py:163); that field starts as `NONE` (audio_pipeline.py:60) and is written only by `start_stream`, `stop_stream` and `change_source` (audio_pipeline.py:374, :408, :436), which are reached from `POST /api/v1/stream/start`, `POST /api/v1/stream/stop` and `PUT /api/v1/input` (audioburger/web_ui/api_server.py:186, :199-200). The start handler substitutes line-in when no source is set (api_server.py:565-566). No code watches a transport state or a property change (search of `audioburger/` for `MediaTransport`, `PropertiesChanged`, `status_update`: no hit), and the loop holds no signal-level test (audio_pipeline.py:157-193). The device `Connected` flag is read only when the paired list is built (audioburger/bluetooth/a2dp_sink.py:271). The tracked frontend sends none of these requests (audioburger/web_ui/frontend/src/App.svelte:19 is its only request). `stop_stream` carries the comment "STUB: Future implementation will stop audio graph here" (audio_pipeline.py:407).
- Presence: CURRENT. Loop and functions from eb02d59; routes from add5474.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: A client issuing the HTTP requests by hand or by script; no shipped UI does.
- Open questions: How was a user meant to start a stream, given no control in the tracked frontend?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S05-07  Stream start and stop events (design)
- Question: S05.3
- What was attempted: The logging report requires "A2DP stream start/stop (INFO)" among Bluetooth log events (logging-observability-report.md:114) and shows a trace in which "Stream started" follows input selection (logging-observability-report.md:376-378). The architecture activation steps include "Waits for A2DP profile to activate" before routing audio (architecture-and-build-plan.md:484). The research report states the monitoring rule "If audio stream expected but not active, trigger programmatic reconnection." (bluetooth-a2dp-sink-research.md:332). The architecture's WebSocket protocol defines a `status_update` message with `"state": "streaming"` (architecture-and-build-plan.md:1197-1205). None of these events or messages is produced by code (search of `audioburger/` for `status_update`, `Stream started`, `A2DP stream`: no hit).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: A source of stream state, which the design places in BlueZ or PipeWire.
- Open questions: NONE
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

### R2-S05-08  Buffering at capture in code
- Question: S05.4
- What was attempted: Each loop pass obtains one block of `buffer_size` frames (audioburger/audio_plane/audio_pipeline.py:165, :232), processes it and sleeps 10 ms (audio_pipeline.py:185, comment "~100Hz processing rate"). There is no queue or ring buffer between capture and processing: `_audio_buffer` is allocated at construction and never read or written afterwards (audio_pipeline.py:66; search of the file for `_audio_buffer`: one hit). No underrun, overrun or gap handling exists (search of `audioburger/` for `underrun`, `overrun`, `xrun`, `dropout`: one hit, the module docstring line "Buffer management and underrun recovery", audio_pipeline.py:9). On any exception the loop logs and sleeps 100 ms (audio_pipeline.py:189-191). Commit eb02d59 lists "Add audio buffer management and processing task lifecycle".
- Presence: CURRENT. Added in eb02d59.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. Note N1 applies; no dropout or underrun count is recorded anywhere in the repo.
- Why it turned out that way: UNKNOWN.
- Depends on: `audio.buffer_size = 1024` (config.toml:13), allowed range 128 to 8192 (audioburger/config/config_manager.py:227-228); the fixed 10 ms sleep.
- Open questions: With the placeholder returning 1024 frames per pass and a 10 ms sleep between passes, at what rate do frames leave the loop compared with the nominal 48,000 per second? What paces the loop once a real Bluetooth source replaces the placeholder?
- Confidence: MEDIUM. INFERRED from clear code; no evidence of a run.

### R2-S05-09  Guide listing: bounded queue with frame dropping and a test-tone fallback
- Question: S05.4
- What was attempted: The listing in the implementation guide puts captured blocks into `asyncio.Queue(maxsize=100)` from the sound-device callback and drops the block when the queue is full (AI-AGENT-IMPLEMENTATION-GUIDE.md:159, :329-335). The loop waits up to 0.1 s for a block (AI-AGENT-IMPLEMENTATION-GUIDE.md:407-413); on timeout with no stream it generates tones at 100, 440, 1000 and 4000 Hz "for testing" (AI-AGENT-IMPLEMENTATION-GUIDE.md:414-426). Stream status flags from the callback are logged as warnings (AI-AGENT-IMPLEMENTATION-GUIDE.md:330-331). The queue is emptied when the input stream stops (AI-AGENT-IMPLEMENTATION-GUIDE.md:363-368). The loop sleeps `1.0 / eq_update_rate` per pass (AI-AGENT-IMPLEMENTATION-GUIDE.md:395). None of this is in the module on main (record R2-S05-08).
- Presence: DISABLED. Document only (Note N3).
- Outcome: UNVERIFIED
- Outcome evidence: NONE.
- Why it turned out that way: UNKNOWN.
- Depends on: `sounddevice` callback delivery; `audio.eq_update_rate = 30` (config.toml:16).
- Open questions: NONE
- Confidence: MEDIUM. Read directly from the document and compared with code.

### R2-S05-10  Buffer sizes and underrun reporting (design)
- Question: S05.4
- What was attempted: The architecture document assigns "Manage buffer health and underrun recovery" to the audio plane (architecture-and-build-plan.md:157), defines a `BUFFER_HEALTH(underruns, latency)` status message (architecture-and-build-plan.md:380) and gives "Buffer Size: 2048 samples (42.67 ms at 48 kHz)" for pass-through (architecture-and-build-plan.md:695). The logging report gives an example warning "Buffer underrun detected input=bluetooth underruns=3 duration_ms=15" (logging-observability-report.md:151) and the codes `AUDIO_BUFFER_UNDERRUN` and `AUDIO_BUFFER_OVERRUN` (logging-observability-report.md:755-756). The shipped setting is 1024 frames (config.toml:13), not 2048. No status message or code of these names is produced (search of `audioburger/` for `BUFFER_HEALTH`, `AUDIO_BUFFER`: no hit).
- Presence: DISABLED. Document only.
- Outcome: UNVERIFIED
- Outcome evidence: NONE. The numbers in the log examples are illustrations, not measurements.
- Why it turned out that way: UNKNOWN.
- Depends on: NONE recorded.
- Open questions: NONE
- Confidence: MEDIUM. STATED in documents; code agrees that nothing implements it.

---

## S06 Remote control and metadata

### R2-S06-01  NOT FOUND
- Question: S06.1
- Searched: All tracked files except the npm lockfile, case-insensitive, for `AVRCP`, `MediaPlayer1`, `MediaControl`, `Player1`, `pause`, `skip`, `next track`, `previous`, `transport control`. `audioburger/` for `.Play(`, `.Pause(`, `.Next(`, `.Previous(`, `uinput`. Read audioburger/bluetooth/a2dp_sink.py in full, the route table at audioburger/web_ui/api_server.py:168-220, and audioburger/web_ui/frontend/src/App.svelte:1-93. No hit that concerns control of the source device.
- Nearest related material: The Web UI mock-up shows "[▶ Start Streaming]  [⏸ Pause]  [⏹ Stop]" labelled "Playback Controls" (architecture-and-build-plan.md:1027) and a UI task "Playback controls (start, pause, stop)" (architecture-and-build-plan.md:2057). The routes `POST /api/v1/stream/start` and `/stop` act on the local pipeline only (audioburger/web_ui/api_server.py:199-200, :553-589); nothing is sent to the Bluetooth source.

### R2-S06-02  NOT FOUND
- Question: S06.2
- Searched: All tracked files except the npm lockfile, case-insensitive, for `volume`, `mute`, `gain`. `audioburger/` for `Volume`, `MediaTransport`, `absolute`. Read audioburger/audio_plane/audio_pipeline.py in full. No volume control, volume property or link between source volume and device volume in code, configuration or scripts.
- Nearest related material: The only scaling in code is the fixed float-to-integer factor 32767 at the output step (audioburger/audio_plane/audio_pipeline.py:321, :337). Documents: an example log line "Output volume is muted" with `volume=0.0` (logging-observability-report.md:524-525; repeated in the table at architecture-and-build-plan.md:1360); equaliser band gain of ±12 dB as a design item (architecture-and-build-plan.md:639); a reference link titled "A2DP sink volume depends on method of connecting to bluez (#1349)" in the PulseAudio section of the research references (bluetooth-a2dp-sink-research.md:582); a proposal to "Implement fade-out/mute before shutdown" (fixes.md:1344).

### R2-S06-03  NOT FOUND
- Question: S06.3
- Searched: All tracked files except the npm lockfile, case-insensitive, for `metadata`, `track`, `artist`, `album`, `title`, `now playing`, `playback status`, `MediaPlayer`, `Player1`. Read audioburger/bluetooth/a2dp_sink.py in full and the status handler at audioburger/web_ui/api_server.py:222-263. Hits for `metadata` are the document lines listed below and one unrelated line (patch1.md:465); hits for `track` and `title` are the verb "track", URL paths and the HTML page title (audioburger/web_ui/frontend/index.html:6); `artist`, `album`, `now playing`, `playback status`, `MediaPlayer`, `Player1` have no hit. No track metadata or source playback status is fetched, stored or shown.
- Nearest related material: "Bluetooth metadata" in the documents means device name and codec, not track data: "Safe handling of external inputs (Bluetooth metadata, audio streams)" (project-context.md:165); "Validate Bluetooth metadata (device name, codec)" and a name-sanitising listing (architecture-and-build-plan.md:1392, :1457-1465); a note on script injection through device names (fixes.md:869-870). In code the device name is read from BlueZ and returned as received (audioburger/bluetooth/a2dp_sink.py:270; audioburger/web_ui/api_server.py:501-504). The status endpoint reports this product's own component flags and the pipeline's active source and destination (api_server.py:235-261; audioburger/audio_plane/audio_pipeline.py:476-484), not the source device's playback state.

---

## Tally

| Stage | Questions | Answered with findings | Answered NOT FOUND | Finding records | NOT FOUND records |
|---|---|---|---|---|---|
| S01 | 5 | 5 | 0 | 12 | 0 |
| S02 | 3 | 3 | 0 | 8 | 0 |
| S03 | 5 | 5 | 0 | 15 | 0 |
| S04 | 4 | 4 | 0 | 11 | 0 |
| S05 | 4 | 4 | 0 | 10 | 0 |
| S06 | 3 | 0 | 3 | 0 | 3 |
| Total | 24 | 21 | 3 | 56 | 3 |

- Questions answered with findings: 21
- Questions answered NOT FOUND: 3
- Total: 24 (equals the 24 questions in the brief)
