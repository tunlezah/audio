# R1 stage records, Bluetooth stages S01 to S06

Repo: /home/user/a2dplaya. Read-only audit. All paths are relative to /home/user/a2dplaya. Line numbers refer to the working tree at commit 70ad071 (branch `claude/inspiring-galileo-a9dlia`, identical to `main`; `git branch -a`, `git log --all`). The repo holds 4 commits, no tags, no stash and no unmerged branch (`git tag`, `git stash list`, `git branch -a`), so no record below is BRANCH-ONLY or REMOVED.

Notes on how the fixed vocabulary is applied in this file:

- Presence DISABLED is used in its "unreachable" sense for settings, constants and methods that exist in the default branch and that no code reads or calls. Each such record says so and cites the search.
- No artefact in the repo records a run of the product: no log file, no test output, no measurement, no notes file (searched `find` names and all 28 tracked files, `git ls-files`). The only author statements about the Bluetooth part are docstrings, README.md and the message of commit 47701ce. None of them states a result. Outcomes are therefore UNVERIFIED throughout (Rule 4).
- "grep" below means a search of all tracked `*.py`, `*.sh`, `*.md`, `*.html` and `*.txt` files, case-insensitive unless a symbol name is given.

---

## S01 Platform and stack

### R1-S01-01  Linux with a package manager of the apt or dnf/yum family and systemd; no hardware named
- Question: S01.1
- What was attempted: the installer header states "Supports Debian/Ubuntu (apt) and Fedora/RHEL (dnf/yum)" (install.sh:4) and picks `apt-get`, `dnf` or `yum`, in that order, by `command -v` (install.sh:15-24). With none of them it prints a warning and sets `PKG_MGR="none"` (install.sh:21-23), after which no system package is installed (install.sh:30-48). README.md:107-108 lists "Linux with BlueZ 5.x" and "Bluetooth adapter". systemd is used when `systemctl` exists (install.sh:77-80) and a unit file is written to /etc/systemd/system (install.sh:111-129). No board, computer model, CPU architecture, distribution release or kernel version is named: grep for `raspberry|raspi|rpi|jetson|armv|aarch|x86|ubuntu|debian|fedora|rhel|bookworm|bullseye|kernel` returns only install.sh:4. setup.py:34 carries the classifier "Intended Audience :: End Users/Desktop".
- Presence: CURRENT. install.sh:1-175 and README.md:104-109 at commit 70ad071.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No artefact records an installation or a run on any machine.
- Depends on: a host with `apt-get`, `dnf` or `yum` (install.sh:15-20); `sudo` (install.sh:31, 41, 78-99, 113-132); systemd for the service part (install.sh:107-136); `/etc/bluetooth/main.conf` existing for the BlueZ edits (install.sh:88-89).
- Open questions: On which machine and distribution, if any, was install.sh run? Was the product ever started on hardware with a Bluetooth adapter?
- Confidence: MEDIUM. STATED platform support (install.sh:4, README.md:107) that the installer code agrees with; no evidence of a run.

### R1-S01-02  Python package with a bash installer and a single-page HTML/JavaScript web UI
- Question: S01.2
- What was attempted: the product is a Python package (`python_requires=">=3.8"`, setup.py:15; "Python 3.8+", README.md:106) started with `python -m a2dplaya` (a2dplaya/__main__.py:91-92) or the console script `a2dplaya` (setup.py:28-30). Concurrency is by `threading.Thread` daemon threads (for example a2dplaya/audio/pipeline.py:139-144, a2dplaya/bluetooth/manager.py:145-150). The web layer is Flask (a2dplaya/web/server.py:29; `flask>=2.0`, requirements.txt:5) serving one static page with inline CSS and JavaScript (a2dplaya/web/static/index.html:1-805). The installer is bash (install.sh:1). Tests use `unittest` (tests/test_config.py:6, tests/test_pipeline.py:6). Bytecode file names in commit 47701ce carry `cpython-311` and `pytest-9.0.2` (for example `tests/__pycache__/test_config.cpython-311-pytest-9.0.2.pyc`, commit 47701ce; deleted in commit d78a903).
- Presence: CURRENT. setup.py:5-39, a2dplaya/__main__.py:16-92 at commit 70ad071.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The bytecode names show that CPython 3.11 imported the config, audio and test modules at some time (commit 47701ce); they record no result.
- Depends on: Python 3.8 or later (setup.py:15); Flask 2.0 or later (requirements.txt:5); CPython 3.11 and pytest 9.0.2 in the environment that produced the committed bytecode (commit 47701ce, tests/__pycache__/).
- Open questions: Was any module outside `a2dplaya/config.py` and `a2dplaya/audio/` ever imported? Commit 47701ce holds bytecode for `a2dplaya/__init__`, `config`, `audio/__init__`, `fft_analyzer`, `pipeline` and `resampler` only, and none for `bluetooth`, `app`, `web`, `cast` or `airplay`.
- Confidence: MEDIUM. INFERRED from clear code and manifests.

### R1-S01-03  BlueZ 5.x controlled over the D-Bus system bus with dbus-python
- Question: S01.3
- What was attempted: the Bluetooth manager names BlueZ interfaces as constants (`org.bluez.Adapter1`, `org.bluez.Device1`, `org.bluez.Media1`, `org.bluez.MediaTransport1`, `org.bluez.Agent1`, `org.bluez.AgentManager1`, a2dplaya/bluetooth/manager.py:25-31), imports `dbus` inside `start()` and opens `dbus.SystemBus()` (a2dplaya/bluetooth/manager.py:98-99). Stated requirements: "BlueZ 5.x with D-Bus API" and "dbus-python or pydbus" (a2dplaya/bluetooth/manager.py:9-10); "Linux with BlueZ 5.x" (README.md:107). Packages installed: `bluetooth bluez bluez-tools` and `python3-dbus python3-gi` on apt (install.sh:34, 37); `bluez bluez-tools` and `python3-dbus` on dnf/yum (install.sh:43, 46). No BlueZ version is pinned or checked. `pydbus` is never imported (grep `pydbus`: only a2dplaya/bluetooth/manager.py:10). `gi` is never imported (grep `import gi|from gi`: no hits). In requirements.txt the dbus-python line is commented out: "# dbus-python>=1.2  # System package recommended: apt install python3-dbus" (requirements.txt:14). Only the `Adapter1`, `Device1`, `org.freedesktop.DBus.ObjectManager` and `org.freedesktop.DBus.Properties` interfaces are used in calls (a2dplaya/bluetooth/manager.py:159-163, 175-180, 238-242, 259-264, 311-315, 363-367, 428-432, 464-468, 500-504, 538-542).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:92-102, reached from a2dplaya/app.py:72-76.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No log or note records that the D-Bus path ran.
- Depends on: a running `org.bluez` service on the system bus (a2dplaya/bluetooth/manager.py:25, 99); the `dbus` Python module being importable by the interpreter that runs the program. The installer creates the virtual environment with `python3 -m venv "$VENV_DIR"` and no further option (install.sh:61), installs into it only the package and requirements.txt (install.sh:66-67; requirements.txt:5, 8, 11), and the systemd unit runs `$INSTALL_DIR/venv/bin/python` (install.sh:123). INFERRED: nothing in the installer places a `dbus` module inside that environment, because the only dbus-python entry is commented out (requirements.txt:14) and the apt package is installed outside the environment (install.sh:37).
- Open questions: Does `import dbus` at a2dplaya/bluetooth/manager.py:98 succeed inside the environment that install.sh builds, or does every installed run take the bluetoothctl path of R1-S01-04? Which BlueZ 5.x release was in view?
- Confidence: MEDIUM. INFERRED from clear code; STATED requirement lines agree with the imports.

### R1-S01-04  `bluetoothctl` command-line tool as the fallback Bluetooth interface
- Question: S01.3
- What was attempted: when the `dbus` import, the bus connection or the D-Bus adapter setup raises, `start()` logs "D-Bus not available (%s), using bluetoothctl fallback" and calls `_setup_adapter_cli()` (a2dplaya/bluetooth/manager.py:103-106). Every later operation branches on `_dbus_available` and runs `bluetoothctl` with `subprocess` (a2dplaya/bluetooth/manager.py:184-192, 271-297, 332-356, 400-417, 440-453, 476-489, 513-527, 550-563). Stated in the class docstring: "Falls back to command-line bluetoothctl if D-Bus is not available." (a2dplaya/bluetooth/manager.py:66) and in requirements.txt:13 "optional, falls back to bluetoothctl CLI". Results are judged by matching text in standard output: "Pairing successful" (a2dplaya/bluetooth/manager.py:447), "Connection successful" (483), "Successful disconnected" (520), "trust succeeded" (557).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:103-106.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a `bluetoothctl` binary on PATH; the exact output wording of the installed bluetoothctl release, since success is decided by substring match (a2dplaya/bluetooth/manager.py:447, 483, 520, 557); no bluetoothctl version is named in the repo.
- Open questions: Against which bluetoothctl release were the matched strings taken? Was the fallback ever exercised?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S01-05  PulseAudio or PipeWire named as the audio stack; PulseAudio Bluetooth module installed; no code talks to either
- Question: S01.4
- What was attempted: the Bluetooth module docstring lists under "Requires" the line "PulseAudio or PipeWire with bluetooth module" (a2dplaya/bluetooth/manager.py:8-11). README.md:109 lists "Optional: PulseAudio/PipeWire, ALSA". The installer installs `pulseaudio-module-bluetooth` on both package families (install.sh:35, 44). The generated systemd unit orders the service after `sound.target` (install.sh:116). No code addresses either audio server: grep for `pactl|pulse|pipewire|wireplumber|parec|pacat|pw-|gst` returns only install.sh:35, install.sh:44, README.md:109 and a2dplaya/bluetooth/manager.py:11. No version of PulseAudio or PipeWire is named.
- Presence: CURRENT. install.sh:35, 44 (package install); a2dplaya/bluetooth/manager.py:11 and README.md:109 (statements).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the distribution providing a package named `pulseaudio-module-bluetooth` (install.sh:35, 44); no PipeWire package is installed by the script (install.sh:32-47).
- Open questions: The docstring calls the audio server a requirement (a2dplaya/bluetooth/manager.py:8-11) and README.md:109 calls it optional; which was meant? Which process was expected to hold the A2DP stream once the PulseAudio module is loaded, and how was this program expected to read from it? Is a PulseAudio instance running for the unit's `User=$USER` (install.sh:121) when the service starts at boot?
- Confidence: MEDIUM. INFERRED from clear manifests and a search with few hits.

### R1-S01-06  ALSA for the line-in input, through pyalsaaudio or the `arecord` tool
- Question: S01.4
- What was attempted: the only audio-device API used anywhere in the code is ALSA capture for the line-in input: `alsaaudio.PCM(type=alsaaudio.PCM_CAPTURE, ...)` (a2dplaya/audio/line_in.py:151-161) with a fallback to an `arecord` subprocess (a2dplaya/audio/line_in.py:181-198). Packages: `alsa-utils` (install.sh:36), `alsa-utils alsa-lib-devel` (install.sh:45). pyalsaaudio is commented out in requirements.txt:17. No ALSA version is named. No ALSA playback call exists (the map's search for `aplay|PCM_PLAYBACK|paplay|sounddevice|pyaudio|pw-play|pacat` at /home/user/audio/repos/R1/01-map.md:111 reports no hits; re-run for this file with the same result).
- Presence: CURRENT. a2dplaya/audio/line_in.py:138-219, started from a2dplaya/app.py:105-111 only when `enable_line_in` is set.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `alsaaudio` importable, or `arecord` on PATH (a2dplaya/audio/line_in.py:140-147); an ALSA capture device, default name "default" (a2dplaya/audio/line_in.py:35).
- Open questions: Was line-in capture run on any sound card?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S01-07  Other external dependencies, all without exact version pins
- Question: S01.5
- What was attempted: Python packages: `flask>=2.0` (requirements.txt:5; setup.py:17, the only `install_requires` entry), `pychromecast>=13.0` (requirements.txt:8; setup.py:20, 23), `zeroconf>=0.80` (requirements.txt:11; setup.py:21, 24). requirements.txt:2 states "Most are optional; the application degrades gracefully when not available". System packages: `python3 python3-pip python3-venv` (install.sh:33), `avahi-utils` (install.sh:38), `libglib2.0-dev` (install.sh:39), `avahi-tools` (install.sh:47). Run-time tools named in code: `avahi-browse` (a2dplaya/airplay/manager.py:261) and `shairport-sync` (a2dplaya/airplay/manager.py:395), the second not installed by install.sh. No lock file exists and no constraint uses `==` (requirements.txt:1-17, setup.py:16-26). pytest 9.0.2 appears only in bytecode file names of commit 47701ce and in no manifest.
- Presence: CURRENT. requirements.txt:1-17, setup.py:16-26, install.sh:27-51.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: whatever versions the package index and distribution supply on the day of installation, since every constraint is a lower bound (requirements.txt:5, 8, 11).
- Open questions: Which concrete versions were installed in the environment that produced the bytecode in commit 47701ce? `python3-gi` and `libglib2.0-dev` are installed (install.sh:37, 39) and nothing imports `gi`; what were they for?
- Confidence: MEDIUM. INFERRED from clear manifests.

---

## S02 Adapter and controller

### R1-S02-01  Adapter found through the BlueZ object manager, selected by substring of its object path, powered and renamed over D-Bus
- Question: S02.1
- What was attempted: `_setup_adapter_dbus()` calls `GetManagedObjects()` on `/` (a2dplaya/bluetooth/manager.py:238-242), walks the objects and takes the first one that has `org.bluez.Adapter1`; when `preferred_adapter` is set, objects whose path does not contain that string are passed over (a2dplaya/bluetooth/manager.py:245-253). It stores the object path and the `Address` property (250-252), raises `RuntimeError("No Bluetooth adapter found")` when none matched (255-256), then sets `Powered` to true and `Alias` to "A2DPlaya" (263-264). `preferred_adapter` comes from the config file field (a2dplaya/config.py:66, default `None`), the environment variable `A2DPLAYA_BT_ADAPTER` (a2dplaya/config.py:214; README.md:84, default shown as "auto") or the flag `--bt-adapter` (a2dplaya/__main__.py:36-40, 63-64; README.md:70-71). `Discoverable` and `Pairable` are not touched at start (a2dplaya/bluetooth/manager.py:258-264). The lookup happens once per process start (a2dplaya/bluetooth/manager.py:101).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:234-269, called at a2dplaya/bluetooth/manager.py:101.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the `dbus` module being importable (see R1-S01-03); the order in which `GetManagedObjects()` returns adapters when more than one exists and no preference is set (a2dplaya/bluetooth/manager.py:245-253); permission of the service user to set adapter properties on the system bus (the installer adds the user to the `bluetooth` group when that group exists, install.sh:83-85).
- Open questions: Was the adapter ever powered by this code on a real controller? Does the service user have D-Bus permission for `org.bluez` on the target distribution?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S02-02  Adapter read from `bluetoothctl show`, powered and renamed with bluetoothctl commands
- Question: S02.1
- What was attempted: `_setup_adapter_cli()` runs `bluetoothctl show` (a2dplaya/bluetooth/manager.py:274-277), takes the second word of each output line that starts with "Controller" as the adapter address and sets the adapter path to the placeholder "/cli" (278-284), then runs `bluetoothctl power on` (287-290) and `bluetoothctl system-alias A2DPlaya` (291-294). `preferred_adapter` is not read anywhere in this function (271-297). The return codes and output of the power and alias commands are not examined (287-294). Any exception is logged as "Failed to setup Bluetooth adapter: %s" and not re-raised (296-297).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:271-297, called at a2dplaya/bluetooth/manager.py:106.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `bluetoothctl` on PATH and the "Controller <address>" line format of its `show` output (a2dplaya/bluetooth/manager.py:278-283); the default controller of bluetoothctl, since no adapter is named in the commands (274-294).
- Open questions: With `--bt-adapter` or `A2DPLAYA_BT_ADAPTER` set and the fallback active, which adapter do the commands act on?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S02-03  Install-time controller setup: bluetooth service enabled and started, user added to the bluetooth group, `AutoEnable=true` appended to main.conf
- Question: S02.1
- What was attempted: `setup_bluetooth()` runs `sudo systemctl enable bluetooth` and `sudo systemctl start bluetooth`, each with errors discarded by `2>/dev/null || true` (install.sh:77-80); adds `$USER` to the group `bluetooth` when it exists (install.sh:83-85); and, when `/etc/bluetooth/main.conf` exists, appends `AutoEnable=true` unless a line already starts with "AutoEnable" (install.sh:88-89, 98-100). The comment above that edit reads "# Enable auto-pairing" (install.sh:97). The line is appended at the end of the file with `tee -a` and no section header is written with it (install.sh:99). The service start at install.sh:79 precedes the file edits at install.sh:91-100, and the script holds no `restart` of the bluetooth service (grep `restart|reload` in install.sh: three hits, `Restart=on-failure` at install.sh:124 and `RestartSec=5` at install.sh:125, both lines of the a2dplaya unit file written at install.sh:113-129, and `sudo systemctl daemon-reload` at install.sh:131; none of the three names the bluetooth service).
- Presence: CURRENT. install.sh:73-104, called at install.sh:155.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: `/etc/bluetooth/main.conf` existing (install.sh:89); the section of main.conf that happens to be last in the file, since the appended lines carry no header (install.sh:92-94, 99); the bluetooth daemon re-reading the file at some later start.
- Open questions: Under which main.conf section do the appended lines land on a given distribution? The comment says "auto-pairing" and the key written is `AutoEnable`; which effect was intended? Was the daemon restarted by hand after installation?
- Confidence: MEDIUM. INFERRED from a short, clear script.

### R1-S02-04  Adapter assumptions: any one BlueZ adapter, named in examples as hci0 or hci1; no model, chipset or firmware named
- Question: S02.2
- What was attempted: the code takes one adapter, the first match, and stores a single path and address (a2dplaya/bluetooth/manager.py:78-79, 245-253). The adapter is referred to by HCI name in examples only: `# e.g., "hci0"` (a2dplaya/config.py:66), "Bluetooth adapter to use (e.g., hci0)" (a2dplaya/__main__.py:38), `python -m a2dplaya --bt-adapter hci1` (README.md:71). The preference is matched as a substring of the D-Bus object path (a2dplaya/bluetooth/manager.py:247-249). README.md:108 lists "Bluetooth adapter" with no qualifier. No adapter model, chipset, firmware file, USB identifier or HCI tool appears: grep for `rfkill|hciconfig|hcitool|btmgmt|firmware|chipset|usb|dongle|broadcom|realtek|intel|csr` returns no hits.
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:245-253; a2dplaya/config.py:66.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ object paths containing the HCI name the user passes (a2dplaya/bluetooth/manager.py:247-249); no adapter is named in the repo.
- Open questions: Which adapter, if any, was the code written or run against? With two adapters and a preference such as "hci1", does a path such as one ending "hci10" also match the substring test?
- Confidence: MEDIUM. INFERRED from clear code and a search with no hits.

### R1-S02-05  Missing adapter at start: D-Bus error falls through to bluetoothctl, start-up continues, availability reported in the status API
- Question: S02.3
- What was attempted: when no adapter object matches, the D-Bus setup raises (a2dplaya/bluetooth/manager.py:255-256); `start()` catches every exception, logs a warning and runs the bluetoothctl setup (103-106). That function also swallows every exception (296-297) and logs "Bluetooth adapter configured via CLI: %s" whether or not a "Controller" line was found (278-295). `start()` therefore returns normally; the controller logs "Bluetooth manager initialized" (a2dplaya/app.py:76-77), keeps the manager object, and the handler at a2dplaya/app.py:78-80 that sets `self.bluetooth = None` is reached only if the constructor or `start()` raises. `is_adapter_available()` is true when either the path or the address is set (a2dplaya/bluetooth/manager.py:218-220) and is exposed as `bluetooth.adapter.available` in `GET /api/status` (a2dplaya/bluetooth/manager.py:222-230; a2dplaya/web/server.py:138-139). The web page does not read that field: its Bluetooth indicator uses `connected_device` only (a2dplaya/web/static/index.html:517-521). Blocked adapter: no handling located (grep `rfkill|blocked`: no hits). Adapter reset or removal while running: no handling located; the adapter is looked up once (a2dplaya/bluetooth/manager.py:101) and no D-Bus signal is subscribed (grep `PropertiesChanged|InterfacesAdded|InterfacesRemoved|add_signal_receiver|connect_to_signal|GLib|MainLoop`: one hit, the package name `libglib2.0-dev` at install.sh:39, and none in code). Later D-Bus calls are wrapped in `try/except` that log and return (for example a2dplaya/bluetooth/manager.py:182-183, 327-328, 436-438). The systemd unit has `Restart=on-failure` and `RestartSec=5` (install.sh:124-125).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:92-106, 218-230, 255-256, 296-297.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the process staying up without an adapter (a2dplaya/app.py:70-80); `Restart=on-failure` acting only when the process exits with failure (install.sh:124), which an absent adapter does not cause on this code path (INFERRED: both setup functions catch their errors, a2dplaya/bluetooth/manager.py:103, 296).
- Open questions: What does the user see when no adapter is present, given that the page ignores `adapter.available`? What happens to a session when the adapter is unplugged and re-plugged during a run?
- Confidence: MEDIUM. INFERRED from clear control flow.

### R1-S02-06  `--no-bluetooth` flag implemented by setting the preferred adapter to the string "__disabled__"
- Question: S02.3
- What was attempted: the flag is declared with the help text "Disable Bluetooth (line-in only mode)" (a2dplaya/__main__.py:41-45). When given, main() sets `config.bluetooth.preferred_adapter = "__disabled__"` under the comment "Disable Bluetooth by preventing its initialization" (a2dplaya/__main__.py:79-81). The controller still constructs and starts the Bluetooth manager without consulting the flag (a2dplaya/app.py:70-77). INFERRED from the control flow: on the D-Bus path no object path contains "__disabled__", so the setup raises (a2dplaya/bluetooth/manager.py:247-256), the exception is caught and the bluetoothctl setup runs (103-106); that setup does not read `preferred_adapter` and runs `show`, `power on` and `system-alias A2DPlaya` (271-294). The manager object remains set, so the `/api/bluetooth/*` routes stay active (a2dplaya/web/server.py:176-234). README.md:55-72 does not list the flag.
- Presence: CURRENT. a2dplaya/__main__.py:41-45, 79-81.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No run of the flag is recorded.
- Depends on: the fallback in `start()` treating every D-Bus setup error the same way (a2dplaya/bluetooth/manager.py:103-106); `bluetoothctl` being present, in which case its commands run with the flag set (INFERRED, reasoning above).
- Open questions: Was the flag ever run on a machine with a Bluetooth adapter, and was the adapter left untouched?
- Confidence: MEDIUM. INFERRED from clear code; the stated intent (a2dplaya/__main__.py:80) and the traced flow differ.

---

## S03 Discoverability, pairing and trust

### R1-S03-01  Discoverable and pairable switched on request from the web UI or API, over D-Bus
- Question: S03.1
- What was attempted: `set_discoverable()` sets the adapter property `Discoverable` to the requested value and sets `Pairable` to true on every call, whatever the argument (a2dplaya/bluetooth/manager.py:168-183). Its only caller is the route `POST /api/bluetooth/discoverable`, which reads `enabled` from the JSON body with default true (a2dplaya/web/server.py:191-198). The web page has a "Make Discoverable" button (a2dplaya/web/static/index.html:405) whose handler always sends `{enabled: true}` (597-605); the page has no control that sends false. Nothing calls `set_discoverable` at start-up (grep `set_discoverable`: a2dplaya/bluetooth/manager.py:168 and a2dplaya/web/server.py:197 only). Duration: no timeout is set or read (grep `DiscoverableTimeout|PairableTimeout|timeout` in a2dplaya/bluetooth/manager.py shows only subprocess, join and scan timeouts). The internal flag `_discoverable` is set before the D-Bus call and is not reverted when the call fails (a2dplaya/bluetooth/manager.py:170, 182-183); it is what `GET /api/status` reports (227). The route is absent from the README API table (README.md:89-102).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:168-183; a2dplaya/web/server.py:191-198.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a person or program calling the route after each start (a2dplaya/web/server.py:191-198); the discoverable timeout configured in BlueZ outside this repo; the D-Bus path being active (R1-S01-03).
- Open questions: For how long does the device stay discoverable after the button is pressed? Is the device discoverable or pairable after a reboot before anyone opens the page? Was the device ever seen by a phone?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S03-02  Discoverable switched with `bluetoothctl` in fallback mode
- Question: S03.1
- What was attempted: when D-Bus is not in use, `set_discoverable()` builds the string "discoverable on" or "discoverable off" and passes it to `subprocess.run` as one list element: `["bluetoothctl", cmd]` (a2dplaya/bluetooth/manager.py:184-190). No `pairable` command is issued on this path (184-192). The command result is not examined (187-190).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:184-192.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: how the installed bluetoothctl treats a single argument that contains a space (a2dplaya/bluetooth/manager.py:186-188); the other bluetoothctl calls in the file pass each word as its own element, for example `["bluetoothctl", "power", "on"]` (288).
- Open questions: Does bluetoothctl accept "discoverable on" as one argument? Is the adapter pairable in fallback mode?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S03-03  Device name fixed as the adapter alias "A2DPlaya" at every start
- Question: S03.1
- What was attempted: the name is set as the adapter `Alias` on the D-Bus path (a2dplaya/bluetooth/manager.py:264) and with `bluetoothctl system-alias A2DPlaya` on the fallback path (291-294). The string is a literal in both places; `BluetoothConfig` has no name field (a2dplaya/config.py:60-68) and no flag or environment variable sets it (a2dplaya/__main__.py:20-50; a2dplaya/config.py:206-216).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:264, 291-294.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the adapter setup of R1-S02-01 or R1-S02-02 completing; on the fallback path the command result is not checked (a2dplaya/bluetooth/manager.py:291-294).
- Open questions: Was the name ever seen on a source device?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S03-04  Device class `0x200414` appended to /etc/bluetooth/main.conf by the installer
- Question: S03.1
- What was attempted: when `/etc/bluetooth/main.conf` exists and no line starts with "Class", the installer appends a blank line, the comment "# A2DPlaya: Set device class to Audio Sink" and the line `Class = 0x200414` (install.sh:88-95). The surrounding comment reads "Configure BlueZ for A2DP sink (if config file exists)" (install.sh:87). The lines go to the end of the file without a section header (install.sh:92-94). The running program does not set or read the class (case-sensitive grep `\bClass\b` in `*.py`: one unrelated hit, a comment at a2dplaya/cast/manager.py:52).
- Presence: CURRENT. install.sh:88-95, called at install.sh:155.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: main.conf existing and holding no active `Class` line (install.sh:89, 91); the section that is last in the file; a restart of the bluetooth daemon after the edit, which the script does not perform (see R1-S02-03).
- Open questions: Did the class value take effect on any machine? How did source devices display the device?
- Confidence: MEDIUM. INFERRED from a short, clear script.

### R1-S03-05  Pairing started from this device toward a scanned device, with `Device1.Pair()` over D-Bus
- Question: S03.2
- What was attempted: the pairing flow in the code runs outward from this device. A scan is started from the page or API (`POST /api/bluetooth/scan`, a2dplaya/web/server.py:183-189; "Scan" button, a2dplaya/web/static/index.html:404, 588-595), which runs `StartDiscovery()` for up to `discovery_timeout` seconds, default 15 (a2dplaya/bluetooth/manager.py:306-330; a2dplaya/config.py:54, 63). Each listed unpaired device gets a "Pair" button (a2dplaya/web/static/index.html:628) that posts the address to `POST /api/bluetooth/pair` (636-643; a2dplaya/web/server.py:200-210). `_pair_device_dbus()` looks the address up in the in-memory list, returns false when it is unknown or has no object path (a2dplaya/bluetooth/manager.py:423-425), calls `Pair()` on the device object and marks it paired (428-435). No pairing agent is registered by the program (see R1-S03-07), no confirmation, PIN or passkey step exists in the code or page (grep `passkey|\bpin\b|RequestConfirmation|RequestPinCode|AuthorizeService|RequestAuthorization`: no hits), and the device is not trusted after pairing (no caller of `trust_device`, see R1-S03-09).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:194-198, 419-438.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the remote device being in a discoverable state during the scan window (a2dplaya/bluetooth/manager.py:315-321); a scan having been run since process start, because the lookup at a2dplaya/bluetooth/manager.py:423 reads a list that only the scan loop fills (320, 346); whatever agent the system provides for the pairing exchange, since the program registers none.
- Open questions: How was a phone expected to pair when the pairing is started from this side? What handles a pairing request that arrives from the source device? Was any device ever paired?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S03-06  Pairing started from this device with `bluetoothctl pair` in fallback mode
- Question: S03.2
- What was attempted: `_pair_device_cli()` runs `bluetoothctl pair <address>` with a 30-second timeout and treats the presence of "Pairing successful" in standard output as success (a2dplaya/bluetooth/manager.py:440-453). On success the in-memory entry is marked paired when it exists (448-449). No agent command is issued (grep `agent` in a2dplaya/bluetooth/manager.py: only the constants at lines 30-31).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:440-453.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the output wording of the installed bluetoothctl (a2dplaya/bluetooth/manager.py:447); a non-interactive bluetoothctl call completing a pairing exchange within 30 seconds (444-445).
- Open questions: What happens when the remote device asks for a confirmation during a non-interactive `bluetoothctl pair`?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S03-07  Pairing agent with capability "NoInputNoOutput" and automatic acceptance: settings and interface names declared, no agent code
- Question: S03.2
- What was attempted: configuration declares `BT_AGENT_CAPABILITY = "NoInputNoOutput"  # Auto-accept pairing` (a2dplaya/config.py:57) and the fields `auto_accept_pairing: bool = True` and `agent_capability` (a2dplaya/config.py:67-68). The Bluetooth module declares `BLUEZ_AGENT_IFACE = "org.bluez.Agent1"` and `BLUEZ_AGENT_MANAGER_IFACE = "org.bluez.AgentManager1"` (a2dplaya/bluetooth/manager.py:30-31). A test asserts the default of `auto_accept_pairing` (tests/test_config.py:34). Nothing reads any of these: grep for `auto_accept_pairing`, `agent_capability`, `BT_AGENT`, `BLUEZ_AGENT` returns only the lines cited here, and grep for `RegisterAgent|RequestDefaultAgent` returns no hits. No class exporting the agent interface exists in the package (a2dplaya/bluetooth/manager.py:37-563 holds two classes, `BluetoothDevice` and `BluetoothManager`).
- Presence: DISABLED. Declared and unreachable: a2dplaya/config.py:57, 67-68 and a2dplaya/bluetooth/manager.py:30-31 have no reader or caller.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No commit, comment or note says why the agent was left out; the code arrived in a single commit (47701ce) and was not changed after it (`git log -- a2dplaya/bluetooth/manager.py`).
- Depends on: an agent provided by something outside this program for any pairing that needs one.
- Open questions: Which agent, if any, answers an incoming pairing request on an installed system? Was an in-process agent planned?
- Confidence: MEDIUM. INFERRED from clear code and searches with no further hits.

### R1-S03-08  Device list held in process memory, filled only during a scan, shown through the API and page; no storage or removal by the project
- Question: S03.3
- What was attempted: known devices live in the dictionary `_devices`, keyed by address (a2dplaya/bluetooth/manager.py:75). It is filled by `_refresh_devices_dbus()` and `_refresh_devices_cli()`, which are called only inside the two scan loops (320, 346). On the D-Bus path every `org.bluez.Device1` object that BlueZ holds is read with its `Name`, `Paired`, `Connected`, `Trusted` and `UUIDs` properties (358-398); on the fallback path `bluetoothctl devices` supplies address and name only (400-417). The list is returned by `get_devices()` (126-129) through `GET /api/bluetooth/devices` (a2dplaya/web/server.py:176-181) and rendered with the address and one of "Connected", "Paired" or "Available" (a2dplaya/web/static/index.html:607-634). The page loads the list once at start (802) and after scan, pair, connect and disconnect actions (591-593, 642, 651, 660). The project writes nothing about devices to disk: `AppConfig.save()` has no caller outside tests (grep `.save(`: tests/test_config.py:62 only) and the config holds no device list (a2dplaya/config.py:60-68). Entries are never deleted from the dictionary (all occurrences of `_devices` are at a2dplaya/bluetooth/manager.py:75-559 and none removes an entry). Removal of a pairing: no code located (grep `RemoveDevice|unpair|forget|remove`: eight hits, none about Bluetooth devices: a2dplaya/audio/pipeline.py:171, a2dplaya/cast/manager.py:382, a2dplaya/airplay/manager.py:232, and `classList.remove` at a2dplaya/web/static/index.html:520, 526, 532, 538, 572).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:75, 126-129, 358-417.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: BlueZ's own store of paired devices, outside this repo, for anything that survives a restart; a scan being run after each process start before the list or the pair, connect and disconnect lookups hold anything (INFERRED: the dictionary starts empty at a2dplaya/bluetooth/manager.py:75 and has no other writer).
- Open questions: How was a paired device to be removed? After a restart, how does a previously paired device appear in the page before a scan is run?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S03-09  Trusting a device: methods present for D-Bus and bluetoothctl, no caller
- Question: S03.3
- What was attempted: `trust_device()` carries the docstring "Mark a device as trusted for auto-reconnection." (a2dplaya/bluetooth/manager.py:212-213). The D-Bus form sets the device property `Trusted` to true (529-548); the fallback form runs `bluetoothctl trust <address>` and matches "trust succeeded" in lower-cased output (550-563). No code calls `trust_device`: grep returns only its definition (212) and nothing in a2dplaya/web/server.py (routes at 176-234 cover devices, scan, discoverable, pair, connect, disconnect). The page has no trust control and does not show the `trusted` field that `to_dict()` returns (a2dplaya/bluetooth/manager.py:55; a2dplaya/web/static/index.html:621-633).
- Presence: DISABLED. Unreachable: defined at a2dplaya/bluetooth/manager.py:212-216, 529-563, with no call site in the repo.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: nothing at run time, since no path reaches it.
- Open questions: Was a trust step after pairing planned in the route at a2dplaya/web/server.py:200-210 or in the page?
- Confidence: MEDIUM. INFERRED from clear code and a search with no call sites.

### R1-S03-10  NOT FOUND
- Question: S03.4
- Searched: grep for `second device|multiple|simultaneous|priority|max_devices|already connected|single|limit` (one unrelated hit, tests/test_resampler.py:76); grep for `RegisterAgent|RequestDefaultAgent|AuthorizeService|RequestConfirmation|RequestAuthorization` (no hits); grep for `PropertiesChanged|InterfacesAdded|InterfacesRemoved|add_signal_receiver|connect_to_signal` (no hits); read of a2dplaya/bluetooth/manager.py:1-563, a2dplaya/web/server.py:174-234, a2dplaya/web/static/index.html:587-661, README.md:1-113, install.sh:73-104, a2dplaya/config.py:53-68; messages of all four commits (`git log --all`).
- Nearest related material: `pair_device()` and `connect_device()` hold no check for an existing connection (a2dplaya/bluetooth/manager.py:194-204, 419-489); `get_connected_device()` returns the first entry whose `audio_connected` flag is set (a2dplaya/bluetooth/manager.py:131-137). No code receives an incoming pairing or connection attempt (see R1-S03-07, R1-S04-06).

### R1-S03-11  NOT FOUND
- Question: S03.5
- Searched: grep for `allow|whitelist|allowlist|blocklist|blacklist|Blocked|auth|passkey|\bpin\b|login|token` (hits: setup.py:9 and a2dplaya/__init__.py:20 "author", tests/test_resampler.py:66 "Allow +-1", none related); grep for `auto_accept_pairing|agent_capability|AuthorizeService`; read of a2dplaya/bluetooth/manager.py:1-563, a2dplaya/config.py:53-68, a2dplaya/web/server.py:174-234, install.sh:73-104, README.md:1-113.
- Nearest related material: `auto_accept_pairing: bool = True` (a2dplaya/config.py:67), read by nothing (R1-S03-07); discoverability is switched on only by a call to `POST /api/bluetooth/discoverable` (a2dplaya/web/server.py:191-198; R1-S03-01); the pair route takes any address from the request body with no check of the caller (a2dplaya/web/server.py:200-210).

---

## S04 Connection and A2DP profile

### R1-S04-01  In-process A2DP sink registration: named in docstrings and constants, no registration call
- Question: S04.1
- What was attempted: the docstrings state that the module is for "acting as a Bluetooth A2DP audio sink" (a2dplaya/bluetooth/manager.py:4), that `start()` will "Initialize Bluetooth subsystem and register as A2DP sink." (93), and that the subpackage "Handles BlueZ D-Bus interactions, A2DP sink registration," (a2dplaya/bluetooth/__init__.py:4). Commit 47701ce describes "Bluetooth A2DP sink manager with BlueZ D-Bus API and bluetoothctl CLI fallback for device pairing/connection". The constants `BLUEZ_MEDIA_IFACE = "org.bluez.Media1"` and `BLUEZ_MEDIA_TRANSPORT_IFACE = "org.bluez.MediaTransport1"` exist (a2dplaya/bluetooth/manager.py:28-29) and have no other occurrence (grep `BLUEZ_MEDIA`). The body of `start()` performs adapter setup only (94-106). No endpoint or profile registration exists: grep for `RegisterEndpoint|MediaEndpoint|RegisterProfile|Profile1|RegisterApplication|SelectConfiguration|SetConfiguration` returns no hits. The UUID constants `A2DP_SINK_UUID` and `A2DP_SOURCE_UUID` (33-34) are used once, to test a remote device's `UUIDs` property (391-395).
- Presence: DISABLED. Declared and unreachable: a2dplaya/bluetooth/manager.py:28-29 are unreferenced, and the behaviour named at a2dplaya/bluetooth/manager.py:93 has no code under it (94-106).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The file was added whole in commit 47701ce and not edited afterwards; no comment or message explains the gap between the docstring and the body.
- Depends on: an A2DP sink endpoint being provided by something outside this program (see R1-S04-02).
- Open questions: Was an in-process media endpoint planned? Does any component on an installed system advertise the A2DP sink service to a source?
- Confidence: MEDIUM. INFERRED from clear code and searches with no hits.

### R1-S04-02  A2DP sink role left to system components: `pulseaudio-module-bluetooth` installed and the audio device class set
- Question: S04.1
- What was attempted: the installer's banner lists "Configure Bluetooth for A2DP sink mode" (install.sh:142). The steps that follow are the package install that includes `pulseaudio-module-bluetooth` (install.sh:35, 44), the bluetooth service enable and start (install.sh:78-79), the `Class = 0x200414` line (install.sh:94) and `AutoEnable=true` (install.sh:99). The module docstring lists "PulseAudio or PipeWire with bluetooth module" under "Requires" (a2dplaya/bluetooth/manager.py:8-11). No PulseAudio or PipeWire configuration is written and no module-load or profile command appears (grep `pactl|load-module|default.pa|pulse|pipewire|wireplumber`: only install.sh:35, install.sh:44, README.md:109, a2dplaya/bluetooth/manager.py:11). INFERRED: with no registration in the program (R1-S04-01), the installed PulseAudio Bluetooth module is the only component named in the repo that could hold an A2DP sink role.
- Presence: CURRENT. install.sh:35, 44, 87-100, 142.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a PulseAudio daemon with its Bluetooth module loaded for some user at run time, which the repo does not set up (install.sh:27-136); the main.conf edits taking effect (R1-S02-03, R1-S03-04).
- Open questions: Did any source device ever see or connect to an audio sink on a machine set up by this installer? On a host that runs PipeWire, which component holds the sink role, given that only the PulseAudio module package is installed?
- Confidence: LOW. INFERRED from a package name and a banner line; no configuration or result to check it against.

### R1-S04-03  NOT FOUND
- Question: S04.2
- Searched: grep for `sbc|aac|aptx|ldac|lc3|codec|capabilit|SelectConfiguration|SetConfiguration|ClearConfiguration|MediaEndpoint|RegisterEndpoint` (four hits, all in a2dplaya/config.py: lines 24, 33, 57, 68; described under nearest related material); read of a2dplaya/bluetooth/manager.py:1-563, a2dplaya/config.py:1-109, install.sh:1-175, README.md:1-113, requirements.txt:1-17.
- Nearest related material: the unreferenced constant `BLUEZ_MEDIA_IFACE` (a2dplaya/bluetooth/manager.py:28). The only codec names in the repo are in comments about the outputs: "Chromecast supports: MP3 (up to 320kbps), AAC-LC, FLAC, WAV, Opus, Vorbis" (a2dplaya/config.py:24) and "AirPlay supports: ALAC (Apple Lossless), AAC, PCM" (a2dplaya/config.py:33). The agent capability string at a2dplaya/config.py:57 concerns pairing, not codecs.

### R1-S04-04  Connect and disconnect on request from the web UI or API, with `Device1.Connect()` and `Device1.Disconnect()`
- Question: S04.3
- What was attempted: connections are opened from this device toward the source. The page shows "Connect" for a paired, unconnected device and "Disconnect" for a connected one (a2dplaya/web/static/index.html:629-630), posting to `POST /api/bluetooth/connect` and `POST /api/bluetooth/disconnect` (645-661; a2dplaya/web/server.py:212-234). `_connect_device_dbus()` returns false for an address that is not in the in-memory list or has no path (a2dplaya/bluetooth/manager.py:459-461), calls the general `Connect()` method of the device object (464-468) and sets `connected` true (469); it does not set `audio_connected`. `_disconnect_device_dbus()` calls `Disconnect()` and clears both flags (491-511). No profile-specific connect is used (grep `ConnectProfile`: no hits). A connection opened by the source device is not received by any code (no signal subscription, see R1-S04-06). The disconnect route is absent from the README API table (README.md:89-102).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:200-210, 455-474, 491-511.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a scan having filled the device list since process start (a2dplaya/bluetooth/manager.py:459, 320); the source device accepting a connection opened from the sink side; an A2DP sink endpoint existing on the system (R1-S04-01, R1-S04-02).
- Open questions: Was a connection ever made? What did `Connect()` connect, given that the program registers no audio endpoint?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S04-05  Connect and disconnect with `bluetoothctl connect` and `bluetoothctl disconnect` in fallback mode
- Question: S04.3
- What was attempted: `_connect_device_cli()` runs `bluetoothctl connect <address>` with a 30-second timeout and looks for "Connection successful" (a2dplaya/bluetooth/manager.py:476-489). `_disconnect_device_cli()` runs `bluetoothctl disconnect <address>` with a 10-second timeout and looks for "Successful disconnected" (513-527). On success the in-memory flags are updated when the address is in the list (484-485, 521-523). `audio_connected` is never set true on this path: the only assignment that can make it true is in the D-Bus refresh (392-395), and the fallback refresh sets name only (407-415).
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:476-489, 513-527.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the output wording of the installed bluetoothctl (a2dplaya/bluetooth/manager.py:483, 520). INFERRED: in fallback mode `get_connected_device()` (131-137) returns nothing, because it tests `audio_connected` and no fallback code sets it; the page's Bluetooth indicator reads that value (a2dplaya/web/server.py:140; a2dplaya/web/static/index.html:517-521).
- Open questions: Does the installed bluetoothctl print exactly "Successful disconnected"?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S04-06  Connection state read by polling BlueZ once a second during a scan window; no event handling
- Question: S04.3
- What was attempted: device state (`Paired`, `Connected`, `Trusted`) is copied from BlueZ in `_refresh_devices_dbus()` (a2dplaya/bluetooth/manager.py:383-388), which runs once a second inside the scan loop for at most `discovery_timeout` seconds, default 15 (317-321; a2dplaya/config.py:54, 63), and nowhere else (grep `_refresh_devices_dbus`: 320 and 358). `audio_connected` is computed there as `connected` and the presence of the A2DP sink or source UUID in the device's `UUIDs` property (390-395); it is not derived from a media transport object. The value feeds `connected_device` in `GET /api/status` (131-137; a2dplaya/web/server.py:140) and the page's Bluetooth dot, polled every 500 ms (a2dplaya/web/static/index.html:494-497, 517-521). No D-Bus signal is subscribed and no main loop is started (grep `PropertiesChanged|InterfacesAdded|InterfacesRemoved|add_signal_receiver|connect_to_signal|DBusGMainLoop|GLib|MainLoop`: one hit, the package name `libglib2.0-dev` at install.sh:39, and none in code). INFERRED: a connect, disconnect or loss of range that happens outside a scan window does not reach the in-memory state until the next scan is started, because no other code updates it. No code distinguishes "out of range" from "disconnected".
- Presence: CURRENT. a2dplaya/bluetooth/manager.py:306-330, 358-398.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a person pressing "Scan" (a2dplaya/web/static/index.html:404) or a program calling `POST /api/bluetooth/scan` (a2dplaya/web/server.py:183-189) for any state refresh; the D-Bus path being active.
- Open questions: What does the status show when a phone connects by itself after pairing? What does it show after the phone walks out of range?
- Confidence: MEDIUM. INFERRED from clear code and searches with no hits.

### R1-S04-07  Automatic reconnect: delay and retry settings declared, no reconnect code
- Question: S04.3
- What was attempted: configuration declares `BT_RECONNECT_DELAY = 3  # seconds between reconnection attempts` and `BT_RECONNECT_MAX_RETRIES = 10` (a2dplaya/config.py:55-56) and the fields `reconnect_delay` and `reconnect_max_retries` (a2dplaya/config.py:64-65). Nothing reads them: grep `reconnect` returns those four lines and the docstring "Mark a device as trusted for auto-reconnection." (a2dplaya/bluetooth/manager.py:213), which belongs to the uncalled method of R1-S03-09. No loop, timer or retry exists in the Bluetooth manager (grep `sleep|retry|retries` in a2dplaya/bluetooth/manager.py: only the scan-loop sleeps at 321 and 345). Reconnect after reboot: the installer enables the bluetooth service and the product's unit at boot (install.sh:78, 132) and appends `AutoEnable=true` (install.sh:99); at start the program holds an empty device list (a2dplaya/bluetooth/manager.py:75) and requests no connection (92-106).
- Presence: DISABLED. Declared and unreachable: a2dplaya/config.py:55-56, 64-65 have no reader.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: the source device or BlueZ re-opening the link without help from this program.
- Open questions: Was a reconnect loop planned around these two settings? Does a paired phone reconnect after the host reboots?
- Confidence: MEDIUM. INFERRED from clear code and a search with no readers.

### R1-S04-08  NOT FOUND
- Question: S04.4
- Searched: grep for `multiple|simultaneous|priority|switch|second device|max_devices|already connected|exclusive|takeover|preempt` (no hits); read of a2dplaya/bluetooth/manager.py:126-137, 194-210, 358-398, 455-527; a2dplaya/app.py:70-80, 176-186; a2dplaya/audio/pipeline.py:76-80, 117-123, 182-218; a2dplaya/web/server.py:131-157, 212-234; a2dplaya/web/static/index.html:393-413, 569-579, 615-634; README.md:1-113.
- Nearest related material: `get_connected_device()` returns the first device in dictionary order with `audio_connected` set and stops there (a2dplaya/bluetooth/manager.py:131-137); `connect_device()` holds no check for another connected device (200-204, 455-474). The pipeline records one source name, set to "bluetooth" or "line-in" by whichever callback delivered the latest chunk, and both callbacks append to the same buffer (a2dplaya/app.py:176-186; a2dplaya/audio/pipeline.py:117-123, 213-215). The page's source tabs only show or hide a section and send no request (a2dplaya/web/static/index.html:569-579).

---

## S05 Audio capture

### R1-S05-01  Bluetooth capture path: callback, transport file descriptor and audio thread declared; no code obtains or delivers Bluetooth audio
- Question: S05.1
- What was attempted: the stated design is that the Bluetooth manager reads the A2DP stream and hands PCM to the pipeline through a callback: "Feeds received audio data into the AudioPipeline." (a2dplaya/bluetooth/manager.py:6); "captures the audio and feeds it to the AudioPipeline via a callback" (64-65); "audio transport management" (a2dplaya/bluetooth/__init__.py:5); README.md:5 "captures audio from connected Bluetooth devices". The pieces present are: the constructor argument `audio_callback`, stored at a2dplaya/bluetooth/manager.py:71; the attributes `_transport_fd` and `_audio_thread`, each initialised to `None` under the comment "# Audio transport" (88-90); clean-up of both in `stop()` (113-122); the unreferenced constant `BLUEZ_MEDIA_TRANSPORT_IFACE` (29); and the receiving function `_bt_audio_callback(pcm_data, sample_rate, channels, sample_width)`, which sets the source name to "bluetooth" and calls `pipeline.feed_audio()` (a2dplaya/app.py:176-180), passed in at a2dplaya/app.py:72-75. The pieces absent are: any call of `self._audio_callback` in the Bluetooth manager (grep `_audio_callback`: a2dplaya/bluetooth/manager.py:71 is the only hit in that file); any assignment to `_transport_fd` or `_audio_thread` other than `None` (grep: lines 89-90, 113-122 only); any transport acquisition (grep `Acquire|TryAcquire|MediaTransport1|os.read|fdopen`: no hits beyond the constant); any read from an audio server or Bluetooth audio device (grep `pactl|parec|pacat|pulse|pipewire|pw-|bluealsa|bluez-alsa|gst`: only the package and docstring lines cited in R1-S01-05). INFERRED: on the default branch no path exists by which audio received over Bluetooth reaches `feed_audio()`, because the only function that would deliver it is never called.
- Presence: DISABLED. Declared and unreachable: a2dplaya/bluetooth/manager.py:71, 88-90 are never used to produce audio; a2dplaya/app.py:176-180 is wired at a2dplaya/app.py:74 and has no caller.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The Bluetooth manager was added whole in commit 47701ce, whose message opens "Full implementation of the audio bridge application including:", and was not edited afterwards (`git log -- a2dplaya/bluetooth/manager.py`). No comment, marker or note mentions the missing hop (grep `TODO|FIXME|HACK|XXX|WORKAROUND|not yet|placeholder|stub` in a2dplaya/bluetooth/: no hits).
- Depends on: nothing at run time, since no path reaches the callback. The design as declared names a BlueZ media transport descriptor (a2dplaya/bluetooth/manager.py:29, 89) while the installer puts `pulseaudio-module-bluetooth` on the system (install.sh:35, 44); the repo holds no code for either route.
- Open questions: Which capture point was intended: a BlueZ `MediaTransport1` descriptor read in-process, or a PulseAudio or PipeWire source read from outside? Was the product ever run with a source device playing, and what did the outputs carry? Commit 47701ce holds no bytecode for `a2dplaya/bluetooth/`; was the module ever imported?
- Confidence: MEDIUM. INFERRED from clear code and searches with no hits; the STATED descriptions (a2dplaya/bluetooth/manager.py:6, 64-65) are not matched by the code.

### R1-S05-02  Line-in capture from ALSA: the one input whose code reaches the pipeline (not Bluetooth)
- Question: S05.1
- What was attempted: a second input enters at the same pipeline function without Bluetooth. `LineInCapture` opens an ALSA capture device with pyalsaaudio (`alsaaudio.PCM(type=alsaaudio.PCM_CAPTURE, mode=alsaaudio.PCM_NORMAL, device=...)`, a2dplaya/audio/line_in.py:151-157; S16_LE, period size `chunk_size`, 158-161) and passes each read to its callback (165-174); when the import or the open fails it starts `arecord -D <device> -f S16_LE -r <rate> -c <channels> -t raw --buffer-size <chunk_size*4>` and reads fixed-size blocks from its standard output (138-147, 181-211). The callback is `_linein_audio_callback`, which sets the source name to "line-in" and calls `feed_audio()` (a2dplaya/app.py:182-186). The capture format is the pipeline constants 44100 Hz, 2 channels, 2 bytes per sample (a2dplaya/audio/line_in.py:37-40; a2dplaya/config.py:40-42). It starts at boot only when `enable_line_in` is set (a2dplaya/app.py:109-111), which `--line-in <device>` does (a2dplaya/__main__.py:46-50, 65-67); `start()` returns early while that setting is false (a2dplaya/audio/line_in.py:68-70). README.md:12 lists "Line-In Input" as a feature.
- Presence: CURRENT. a2dplaya/audio/line_in.py:63-79, 138-219; a2dplaya/app.py:103-115.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: an ALSA capture device (default name "default", a2dplaya/audio/line_in.py:35); `alsaaudio` or `arecord` being available (140-147); `enable_line_in` being true (68-70).
- Open questions: Was line-in ever used to exercise the outputs in place of Bluetooth audio?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S05-03  Format at the capture boundary: PCM bytes with rate, channel count and sample width passed on each call; no Bluetooth-side values exist
- Question: S05.2
- What was attempted: the boundary function takes `(pcm_data: bytes, sample_rate: int, channels: int, sample_width: int)` (a2dplaya/app.py:176) and forwards the four values to `feed_audio()`, whose docstring specifies "Raw PCM audio bytes (signed, little-endian)" and a sample width of 1 to 4 bytes (a2dplaya/audio/pipeline.py:196-200). When the three numbers differ from the pipeline format the chunk is passed through the resampler (206-211). The pipeline format is 44100 Hz, 16-bit signed little-endian, stereo (a2dplaya/audio/pipeline.py:13-16; a2dplaya/config.py:39-42). The contract is decoded PCM: the package holds no decoder for a Bluetooth codec (grep `sbc|aptx|ldac`: no hits in the repo; grep `decode` in a2dplaya/bluetooth/ and a2dplaya/audio/: hits only in a2dplaya/audio/resampler.py:73-74, 82, 127, 152-153, where `_decode_samples` unpacks PCM bytes to floats). Because no Bluetooth code calls the callback (R1-S05-01), no sample rate, bit depth or channel count for Bluetooth audio is written anywhere in the repo.
- Presence: CURRENT. a2dplaya/app.py:176-180; a2dplaya/audio/pipeline.py:182-218. The Bluetooth caller that would supply the values is absent (R1-S05-01).
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a caller supplying already-decoded PCM and true format values; the resampler (a2dplaya/audio/resampler.py:29) for any format other than 44100 Hz, 2 channels, 16-bit.
- Open questions: What format would data from the declared transport descriptor (a2dplaya/bluetooth/manager.py:89) have had, and where was it to be decoded to PCM?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S05-04  Stream start marked by the source name set on each chunk; no stop detection
- Question: S05.3
- What was attempted: the only stream-activity markers are in the pipeline. Each delivered chunk sets `current_source` to "bluetooth" or "line-in" (a2dplaya/app.py:179, 185). The value starts as `None` (a2dplaya/audio/pipeline.py:80) and no code sets it back to `None`. Grep `current_source = ` returns four lines: the two callback assignments (a2dplaya/app.py:179, 185); the body of the property setter, `self._current_source = name` (a2dplaya/audio/pipeline.py:123, setter declared at 121-122), which stores whatever its caller passes; and a test that assigns "bluetooth" (tests/test_pipeline.py:117, in `test_current_source`, 115-118). None of the four assigns `None`, the setter has no caller other than those three lines (grep `current_source`: the remaining hits are the initialisation at a2dplaya/audio/pipeline.py:80, the getter at 118-119, the read at a2dplaya/web/server.py:145 and the test reads at tests/test_pipeline.py:116, 118), and `stop()` does not touch the value (a2dplaya/audio/pipeline.py:150-156). Each processed chunk also updates `last_chunk_time` (a2dplaya/audio/pipeline.py:270). `GET /api/status` returns the source name as `audio.source` and the statistics dictionary under the key `pipeline_running` (a2dplaya/web/server.py:142-147). The page lights its "Audio" dot while `audio.source` is non-empty (a2dplaya/web/static/index.html:535-539) and does not read `last_chunk_time`. The Bluetooth manager has no transport-state or playback-state watch (grep `PropertiesChanged|State|"active"|"pending"|"idle"` in a2dplaya/bluetooth/manager.py: only the comment "# State" at line 77). INFERRED: once any chunk has arrived the audio indicator stays lit for the life of the process, because nothing clears the source name.
- Presence: CURRENT. a2dplaya/app.py:176-186; a2dplaya/audio/pipeline.py:80, 117-123, 266-270.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN.
- Depends on: a capture source calling the callback; for Bluetooth none does (R1-S05-01).
- Open questions: How was the end of a Bluetooth stream to be told apart from a pause or a dropped link?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S05-05  Capture-side buffer: a 100-chunk deque between the input callback and the processing thread; overrun and underrun counters declared
- Question: S05.4
- What was attempted: `feed_audio()` appends each (resampled) chunk to `deque(maxlen=100)`, described as "Audio buffer for smoothing out input irregularities" and "Max ~100 chunks buffered" (a2dplaya/audio/pipeline.py:76-77, 213-215). The append sits in a `try` whose `except` increments `overruns` (214-218). The processing thread pops from the left and, when the deque is empty, sleeps 1 ms and looks again (228-240). Statistics include `underruns` and `overruns`, both starting at 0 (83-89). `underruns` is never incremented (grep `underruns`: a2dplaya/audio/pipeline.py:86 and tests/test_pipeline.py:113 only). INFERRED: a full deque does not raise on append, since a Python deque with `maxlen` drops the oldest item to make room, so the `overruns` counter at line 218 is not reached by overflow and the oldest chunk is dropped without a count or log. `chunk_duration` is computed at line 227 and not used afterwards (grep). No silence is inserted when input stops and no gap is measured (a2dplaya/audio/pipeline.py:230-240). Chunks are whatever size the caller delivers; they are not re-cut to `chunk_size` (a2dplaya/audio/pipeline.py:202-215). Tests cover start, stop, consumer delivery of one chunk and the presence of the statistics keys (tests/test_pipeline.py:22-26, 48-72, 108-113); none fills the buffer.
- Presence: CURRENT. a2dplaya/audio/pipeline.py:76-89, 182-240.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. No test result, log or measurement is recorded; commit 47701ce mentions a "Comprehensive test suite (44 tests)" and gives no result.
- Depends on: the caller's chunk size, which sets how much audio 100 entries hold; consumers returning quickly, since they run on the processing thread (a2dplaya/audio/pipeline.py:253-260).
- Open questions: How many seconds of audio do 100 chunks represent for a Bluetooth source? What is heard at the outputs when the deque overflows or runs empty?
- Confidence: MEDIUM. INFERRED from clear code; the overflow behaviour rests on the standard behaviour of `deque(maxlen=...)`.

---

## S06 Remote control and metadata

### R1-S06-01  NOT FOUND
- Question: S06.1
- Searched: grep for `avrcp|MediaPlayer1|MediaControl|\.Play\(|\.Pause\(|\.Next\(|\.Previous\(|pause|skip|next|previous|transport control` across `*.py`, `*.html`, `*.sh`, `*.md`, `*.txt` (three hits, all the word "next" in comments or docstrings about audio chunks: a2dplaya/cast/manager.py:285, 294; a2dplaya/audio/pipeline.py:236); read of a2dplaya/bluetooth/manager.py:1-563, a2dplaya/web/server.py:114-399, a2dplaya/web/static/index.html:345-458 and 587-661, README.md:87-102.
- Nearest related material: NONE for the Bluetooth source. The routes whose names contain "play" start an output stream to a Chromecast or AirPlay target and send nothing to the source device (a2dplaya/web/server.py:253-263, 290-300; README.md:98, 101).

### R1-S06-02  Volume applied as software scaling in the pipeline, set from the web UI or API; not linked to the source device's volume
- Question: S06.2
- What was attempted: one volume value, 0.0 to 1.0, is held by the pipeline (a2dplaya/audio/pipeline.py:56-57, 94-103; clamped at 102). The processing thread scales every chunk before analysis and distribution (244): values of 0.999 and above pass the data through, 0.001 and below produce zeros, and anything between unpacks the bytes as 16-bit little-endian samples, multiplies and clips (275-305). The value is set by `POST /api/volume` (a2dplaya/web/server.py:159-172) from the page's slider (a2dplaya/web/static/index.html:389-390, 559-567) and read back through `GET /api/status` (a2dplaya/web/server.py:144; a2dplaya/web/static/index.html:547-555). The start value comes from the config field `audio.volume`, default 1.0 (a2dplaya/config.py:80), or `A2DPLAYA_AUDIO_VOLUME` (a2dplaya/config.py:213; README.md:82). A change made at run time is not written to the config file (a2dplaya/web/server.py:165-170 sets the property only; `AppConfig.save()` has no caller outside tests). No code reads or sets a volume on the Bluetooth side: case-sensitive grep for `avrcp|AbsoluteVolume|MediaTransport1|"Volume"` returns only the unreferenced constant at a2dplaya/bluetooth/manager.py:29, and the word "volume" does not occur in a2dplaya/bluetooth/. Tests exist for the property, the clamp and the scaling at 1.0, 0.0 and 0.5 (tests/test_pipeline.py:28-36, 80-101).
- Presence: CURRENT. a2dplaya/audio/pipeline.py:94-103, 244, 275-305; a2dplaya/web/server.py:159-172.
- Outcome: UNVERIFIED
- Outcome evidence: NONE
- Why it turned out that way: UNKNOWN. The tests are present; no run output is stored. Bytecode names in commit 47701ce show that pytest 9.0.2 loaded tests/test_pipeline.py and record no pass or fail.
- Depends on: 16-bit samples in the pipeline (a2dplaya/audio/pipeline.py:294-296); the volume the source device applies before sending, which this program neither sees nor changes.
- Open questions: Did the 44 tests named in commit 47701ce pass? Do the volume buttons on a phone change anything on this device?
- Confidence: MEDIUM. INFERRED from clear code.

### R1-S06-03  NOT FOUND
- Question: S06.3
- Searched: grep for `track|title|artist|album|metadata|now.?playing|MediaPlayer1|avrcp|playback` across `*.py`, `*.html`, `*.md`, `*.sh`, `*.txt` (twelve hits, none about a Bluetooth source: the page `<title>` and "subtitle" at a2dplaya/web/static/index.html:6, 56, 348, and the word "playback" for browser or Cast output at README.md:5, 14, a2dplaya/__init__.py:8, a2dplaya/cast/__init__.py:5, a2dplaya/web/__init__.py:5, a2dplaya/web/server.py:8, 100, 362, 366); read of a2dplaya/bluetooth/manager.py:37-57 and 358-398, a2dplaya/web/server.py:131-157, a2dplaya/web/static/index.html:345-458 and 510-556, README.md:87-102.
- Nearest related material: `GET /api/status` returns the connected device's name and address flags and the pipeline's source name, with no track or playback fields (a2dplaya/web/server.py:137-147; a2dplaya/bluetooth/manager.py:49-57). The page shows four status dots and a device list (a2dplaya/web/static/index.html:351-368, 510-539, 615-634).

---

## Tally

- Finding records: 34
- NOT FOUND records: 6 (R1-S03-10, R1-S03-11, R1-S04-03, R1-S04-08, R1-S06-01, R1-S06-03)
- Questions answered with findings: 18 (S01.1, S01.2, S01.3, S01.4, S01.5, S02.1, S02.2, S02.3, S03.1, S03.2, S03.3, S04.1, S04.3, S05.1, S05.2, S05.3, S05.4, S06.2)
- Questions answered NOT FOUND: 6 (S03.4, S03.5, S04.2, S04.4, S06.1, S06.3)
- Total: 24
