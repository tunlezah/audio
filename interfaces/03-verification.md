# Verification of 01-products.md and 02-patterns.md

Verifier run, 2026-11-17. Checks as briefed: A (two cited URLs opened; do they carry the statements that cite them), B (a cited page confirms the product accepts Bluetooth audio), C (no field ranks, scores, judges or proposes), D (each P number under a pattern has a matching statement in that product's record), E (no pattern line judges or proposes). Nothing in the two files was changed.

How the checks were run:

- S keys are the source keys in each record of `/home/user/audio/interfaces/01-products.md`; the URLs are listed there. Every page named below was opened on 2026-11-17 and its text read in full or searched for each statement that cites it.
- Check A is a sample: statements citing sources that were not opened are outside it. A PASS on A means the statements citing the opened sources were found on those pages.
- Check D was run against the record text only, not against the web pages. A pattern row fails when, for at least one listed P number, part of the pattern text is absent from that record or is at odds with it.
- github.com HTML pages returned HTTP 403 to a direct download in this session. P06 S2 was read through the fetch tool, which returns a model-written extract. P07 S2 was read from the wiki's raw source, https://raw.githubusercontent.com/wiki/pschatzmann/ESP32-A2DP/A2DP-Sink.md (2026-11-17). No other source was affected.

## Product records (checks A, B, C)

- P01 moOde audio player: PASS. A: opened S3, S6 (also S1, S2). B: S6 lists `bluetooth` as a `source` value; S3 has the Bluetooth renderer section. C: none found.
- P02 IoTSound: PASS. A: opened S3, S6 (also S4). B: S3: "If using Bluetooth: search for your device on your phone or laptop and pair". C: none found.
- P03 HiFiBerryOS (legacy): PASS. A: opened S4, S6 (also S1, S5). B: S1: "Bluetooth A2DP sink"; S4: "act as a Bluetooth speaker". C: none found.
- P04 rpi-audio-receiver: PASS. A: opened S1, S2. B: S1: "audio receiver with Bluetooth (A2DP)". C: none found.
- P05 BlueALSA bluealsa-aplay: PASS. A: opened S1, S2. B: S2: "Capture audio streams from Bluetooth devices" and play them to an ALSA device. C: none found.
- P06 squeezelite-esp32: PASS. A: opened S1, S2 (S2 through the fetch tool). B: S1: "Stream from a **Bluetooth** device (iPhone, Android)". C: none found.
- P07 ESP32-A2DP: PASS. A: opened S1, S2 (S2 through the raw wiki source). B: S1: A2DP sink that receives sound data "e.g. from your Mobile Phone". C: none found.
- P08 Audioengine B1: PASS. A: opened S1, S2 (also S3). B: S2 quick setup: select "Audioengine B1" on the phone, then play music. C: none found.
- P09 iFi ZEN Blue 3: PASS. A: opened S1, S2 (also S3). B: S2: RX mode "for receiving Bluetooth signals from your smartphone". C: none found.
- P10 FiiO BR13: PASS. A: opened S2, S3 (also S5). B: S3: "How to use the BR13 as a Bluetooth receiver?". C: none found.
- P11 Pro-Ject BT Box S2 HD: PASS. A: opened S1, S2. B: S2: "play music from your Android device over Bluetooth to BT_Box_S2 HD". C: none found.
- P12 WiiM Pro: PASS. A: opened S1, S2 (also S3). B: S1 section 7.1 "Audio Input via Bluetooth". C: none found.
- P13 Bluesound NODE: PASS. A: opened S1, S3 (also S5). B: S1: Bluetooth connectivity "Two-Way (transmit & receive)"; S3 input item `inputType="bluetooth"`. C: none found.
- P14 Denon Home 150: PASS. A: opened S1, S2. B: S1: after pairing, "The sound will now play from the speaker". C: none found.
- P15 Arylic S10: PASS. A: opened S1, S2. B: S1 section 8.3.5 "To Play Bluetooth Mode". C: none found.
- P16 Sonos Era 100: PASS. A: opened S1, S2. B: S1: pair a device with Era 100 via Bluetooth; S2 lists Era 100 as sharing Bluetooth audio. C: none found.
- P17 Amazon Echo: PASS. A: opened S1, S4 (also S3). B: S4: stream audio from the phone to the Echo "as if it were a standard portable Bluetooth speaker". C: none found.
- P18 Google Nest and Home: PASS. A: opened S1 (the record cites one URL; one opened). B: S1: connect a mobile device with Bluetooth "to listen to music, audiobooks, podcasts". C: none found.
- P19 Sonos Roam and Move 2: PASS. A: opened S1, S2. B: S1 and S2: pair a device with the speaker via Bluetooth. C: none found.
- P20 Windows AudioPlaybackConnection: PASS. A: opened S1, S2. B: S2: "configuring a PC to behave like a Bluetooth speaker". C: none found.
- P21 JBL Authentics 200 and 500: PASS. A: opened S1 (the record cites one URL; one opened). B: S1 section 7.2 "Play from the Bluetooth source". C: none found.

## Patterns (checks D, E)

Row labels are the surface number and the row's position in that surface's tables, with the line number in `/home/user/audio/interfaces/02-patterns.md`.


### 1. Setup and onboarding

- 1.1 (line 19) "Write an OS image to boot media, then finish configuration in a browser on the local network" [P01 P03]: FAIL D. P03's record marks the installation steps NOT DETERMINED and places the browser first-run wizard in the successor system, not in the legacy release examined.
- 1.2 (line 20) "Create a fleet in a cloud device-management account, flash the image it issues, and let the application download to the device" [P02]: PASS.
- 1.3 (line 21) "Run an interactive install script in a shell on an existing OS, answering a prompt per component" [P04]: PASS.
- 1.4 (line 22) "Build and install from source and start the daemons by hand" [P05]: FAIL D. P05's record says the daemon "must be running"; starting it by hand is not stated there.
- 1.5 (line 23) "Compile the function into firmware by writing code against a library" [P07]: PASS.
- 1.6 (line 24) "Device starts its own Wi-Fi access point; the user joins it and configures in a web page at a fixed address" [P06]: PASS.
- 1.7 (line 25) "Power, an audio cable and the phone's Bluetooth settings only; no app, account or network" [P08 P09 P10 P11]: FAIL D. "no app, account or network" is stated only in P08's record; P09 and P10 each name an app (GAIA APP, FiiO Control) and P11 says only "No app is mentioned".
- 1.8 (line 26) "Home-network connection together with the vendor mobile app comes first" [P12 P14 P15 P16 P17 P19 P21]: PASS.
- 1.9 (line 27) "Maker states Bluetooth cannot be used until the Wi-Fi/app setup has been done" [P16 P19]: PASS.
- 1.10 (line 28) "Bluetooth pairing has to be switched on in the vendor app before first use, and the phone is stated to need the same Wi-Fi network" [P18]: PASS.
- 1.11 (line 29) "App uses Bluetooth LE to find the device during Wi-Fi setup" [P12 P15]: FAIL D. P15's record says only that the phone has Bluetooth switched on during setup; discovery over Bluetooth LE is not stated there.
- 1.12 (line 30) "An account with the maker's online service is part of setup" [P02 P17]: PASS.
- 1.13 (line 31) "A host application has to enable the sink; the operating system does not do it by itself" [P20]: PASS.
- 1.14 (line 32) "First start completes without user action after automatic reboots" [P01]: PASS.
- 1.15 (line 33) "Fallback path offered when the first network setup method fails (own hotspot or own Wi-Fi network)" [P01 P15]: PASS.
- 1.16 (line 34) "NOT DETERMINED (whole field)" [P13]: PASS.

### 2. Pairing and connection

- 2.1 (line 42) "Receiver is discoverable and accepts pairing with no action on the receiver and no code" [P02 P04]: PASS.
- 2.2 (line 43) "Pairing window opened by pressing or holding a button on the unit" [P08 P09 P10 P11 P12 P14 P16 P19 P21]: PASS.
- 2.3 (line 44) "Pairing enabled or started from a web page or vendor app" [P01 P03 P12 P15 P17 P18]: PASS.
- 2.4 (line 45) "Pairing window opened by a spoken command" [P17]: PASS.
- 2.5 (line 46) "Unit enters pairing mode by itself at power-on or when no known device is found" [P08 P09 P10 P12 P19]: PASS.
- 2.6 (line 47) "Pairing window has a stated time limit (1 or 2 minutes or until stopped; 60 seconds; three minutes)" [P03 P11 P21]: PASS.
- 2.7 (line 48) "Optional confirmation code shown and confirmed on both sides" [P01]: PASS.
- 2.8 (line 49) "Optional legacy PIN mode" [P02 P04]: PASS.
- 2.9 (line 50) "Pairing done with operating-system Bluetooth tools outside the product" [P04 P05 P20 (inf.)]: PASS.
- 2.10 (line 51) "Device name seen by the phone is set by the user or developer" [P01 P02 P04 P07]: PASS.
- 2.11 (line 52) "Device name seen by the phone is printed in the manual" [P08 P09 P11 P14 P15 P21]: PASS.
- 2.12 (line 58) "One device connected at a time, stated" [P04 P08]: PASS.
- 2.13 (line 59) "Two or more devices connected at once, stated" [P05 P10 P21]: PASS.
- 2.14 (line 60) "More than one client implied by plural wording" [P01 (inf.)]: PASS.
- 2.15 (line 61) "Number of remembered devices stated (six; eight; eight)" [P08 P09 P11]: PASS.
- 2.16 (line 62) "An action on the receiver lets a new device in while another is connected (button hold or app option)" [P08 P10 P12 P19 P21]: PASS.
- 2.17 (line 63) "Most recently paired device is the one connected" [P18]: PASS.
- 2.18 (line 64) "First device to start playing holds the output; others wait (option)" [P05]: PASS.
- 2.19 (line 65) "Discoverability switched off while a device is connected" [P04]: PASS.
- 2.20 (line 71) "Automatic reconnection to a known device, stated" [P02 P09 P10 P12 P16 P17 P19 P21]: PASS.
- 2.21 (line 72) "Automatic disconnect after an idle period (setting)" [P01 P16 P19]: PASS.
- 2.22 (line 73) "A mode setting governs whether an incoming Bluetooth stream takes over the player (Manual, Automatic, Guest, Disabled)" [P13]: FAIL D. P13's record lists the four mode names and marks their definitions NOT DETERMINED; what the mode governs is not stated there.
- 2.23 (line 74) "Clear-all-pairings action (web command, button hold, power-button sequence, or spoken command)" [P01 P10 P11 P18]: PASS.
- 2.24 (line 75) "Per-device connect, disconnect and forget from a web page or app" [P01 P12 P18]: FAIL D. P18's record states per-device Unpair in the app only; connect and disconnect appear there as voice commands.
- 2.25 (line 76) "Pairing procedure not described in the pages opened" [P06 P07]: PASS.

### 3. Controls

- 3.1 (line 82) "Physical controls limited to power and pairing" [P08 P11]: PASS.
- 3.2 (line 83) "Physical controls beyond power and pairing (transport, volume, input, mode or source)" [P06 P09 P10 P12 P13 P15 P21]: PASS.
- 3.3 (line 84) "A button dedicated to Bluetooth" [P14 P16 P19 P21]: PASS.
- 3.4 (line 85) "Pairing shares a button or button combination with another function" [P08 P09 P10 P12 P19]: PASS.
- 3.5 (line 86) "Browser page served by the device" [P01 P02 (inf.) P03 P06]: PASS.
- 3.6 (line 87) "Vendor mobile app" [P09 P10 P12 P13 P14 P15 P16 P17 P18 P19 P21]: PASS.
- 3.7 (line 88) "Spoken commands for Bluetooth functions" [P17 P18]: PASS.
- 3.8 (line 89) "Voice assistant present, stated as not available in Bluetooth mode" [P21]: PASS.
- 3.9 (line 90) "Remote-control handset" [P06 P12 P13 P15]: PASS.
- 3.10 (line 91) "Shell commands" [P01 P04 P05]: PASS.
- 3.11 (line 92) "Cloud dashboard for device management" [P02]: PASS.
- 3.12 (line 93) "Controls are whatever the host application provides" [P20]: PASS.
- 3.13 (line 94) "Transport commands sent from the receiver side to the phone, documented" [P01 P07 P21]: FAIL D. P01's record says the commands are forwarded to the renderer and quotes the alert "Playback cannot be controlled from moOde for Bluetooth"; delivery to the phone is not stated.
- 3.14 (line 95) "Phone's volume requests applied on the receiver, documented (option or mode)" [P04 P05 P07]: PASS.
- 3.15 (line 96) "Volume ceiling applied to Bluetooth sessions" [P01]: PASS.
- 3.16 (line 97) "Playback and volume left to the phone, stated" [P02 P08 P17 P18]: FAIL D. P17 and P18 records state only that playback is started on the phone, with nothing on volume; P02's record lists its own volume variable and volume API route.

### 4. Status feedback

- 4.1 (line 103) "One light whose blink or steady state shows pairing and connected" [P08 P09 P11 P12 P14 P16 P19 P21]: FAIL D. P09's record gives the Bluetooth-mode LED as blinking for searching and for pairing only; no connected state is stated.
- 4.2 (line 104) "Light colour shows the codec in use" [P08 P09]: PASS.
- 4.3 (line 105) "Light colour shows the active input or mode" [P12 P15]: PASS.
- 4.4 (line 106) "Text or graphic display on the unit" [P06 P10]: PASS.
- 4.5 (line 107) "Chimes or tones at pairing and connection" [P16 P19]: PASS.
- 4.6 (line 108) "Spoken prompt or announcement" [P09 P17]: PASS.
- 4.7 (line 109) "Spoken answer to a status question" [P18]: PASS.
- 4.8 (line 110) "Overlay in the web page while Bluetooth is the active source, with track metadata where the phone provides it" [P01]: PASS.
- 4.9 (line 111) "Paired or connected devices listed in a web page or app" [P01 P12 P18]: PASS.
- 4.10 (line 112) "Status read through an API (source or mode value, state property, metadata)" [P01 P03 P12 P13 P15 P20]: PASS.
- 4.11 (line 113) "Status through command-line listing, logs or a cloud dashboard" [P02 P05]: PASS.
- 4.12 (line 114) "Indicator can be switched off by the user" [P09]: PASS.
- 4.13 (line 115) "Status light off unless enabled in the app" [P16]: PASS.
- 4.14 (line 116) "Maker's light-meaning table contains no Bluetooth state" [P17]: PASS.
- 4.15 (line 117) "Lights present, meanings not established" [P06 P13]: PASS.
- 4.16 (line 118) "None built in; left to the developer's code" [P07]: PASS.
- 4.17 (line 119) "NOT DETERMINED (whole field)" [P04]: PASS.

### 5. Settings exposed, and their defaults

- 5.1 (line 127) "Browser page served by the device" [P01 P03 P06]: PASS.
- 5.2 (line 128) "Environment variables in a cloud dashboard" [P02]: PASS.
- 5.3 (line 129) "Hand-edited configuration files or command-line options" [P04 P05]: PASS.
- 5.4 (line 130) "Source code" [P07]: PASS.
- 5.5 (line 131) "Vendor mobile app" [P10 P12 P14 P15 P16 P17 P18 P19 P21]: PASS.
- 5.6 (line 132) "Buttons on the unit" [P09 P10]: PASS.
- 5.7 (line 133) "No user setting described" [P08 (inf.) P11 (inf.) P20 (inf.)]: PASS.
- 5.8 (line 139) "Bluetooth on/off" [P01 P02 P03 P13]: PASS.
- 5.9 (line 140) "Bluetooth device name" [P01 P02 P04 P07]: PASS.
- 5.10 (line 141) "Pairing security (confirmation on/off, pairing mode, PIN)" [P01 P02 P04]: PASS.
- 5.11 (line 142) "Pairing window length" [P03]: PASS.
- 5.12 (line 143) "Buffer or latency" [P01 P02 P05]: PASS.
- 5.13 (line 144) "Codec quality or codec enable list" [P01 P10]: PASS.
- 5.14 (line 145) "Equaliser applied to Bluetooth input" [P10]: PASS.
- 5.15 (line 146) "Start-up volume or volume ceiling" [P01 P02]: PASS.
- 5.16 (line 147) "Idle auto-disconnect" [P01 P16 P19]: PASS.
- 5.17 (line 148) "Automatic reconnect to the last device" [P19]: PASS.
- 5.18 (line 149) "Behaviour when a Bluetooth stream arrives (auto-switch mode)" [P13]: FAIL D. P13's record lists "Bluetooth mode (four values)" with no description of behaviour when a stream arrives.
- 5.19 (line 150) "Resume the previous source after Bluetooth ends" [P01]: PASS.
- 5.20 (line 151) "Indicator light or voice prompt on/off" [P09 P16]: PASS.
- 5.21 (line 152) "Renaming or hiding inputs" [P14]: PASS.
- 5.22 (line 153) "Paired-device list management" [P01 P12 P17 P18]: PASS.
- 5.23 (line 159) "At least one Bluetooth-related default stated in the documentation" [P01 P02 P05 P07 P09 P16 P19]: PASS.
- 5.24 (line 160) "Stated defaults that leave a feature on: LED display and voice prompt (P09); secure simple pairing without PIN (P02)" [P02 P09]: PASS.
- 5.25 (line 161) "Stated defaults that leave a feature off: idle auto-disconnect (P16 P19); auto-connect (P19); status light (P16)" [P16 P19]: PASS.
- 5.26 (line 162) "Default auto-switch mode stated only by maker staff in a forum" [P13]: PASS.
- 5.27 (line 163) "Settings exist, defaults not established" [P03 P10 P12 P17 P18]: PASS.

### 6. Output or destination selection

- 6.1 (line 169) "Fixed set of output connectors, no selector described" [P08 P09 P10 P11]: PASS.
- 6.2 (line 170) "Output device picked in a browser page" [P01]: PASS.
- 6.3 (line 171) "Output set by an environment variable, with an automatic detection order as the default" [P02]: PASS.
- 6.4 (line 172) "Output set in a configuration file, command-line option, stored parameter or code" [P04 P05 P06 P07]: PASS.
- 6.5 (line 173) "Output picked in the vendor app" [P12]: PASS.
- 6.6 (line 174) "Plays on the host system's audio endpoints" [P20]: PASS.
- 6.7 (line 175) "Bluetooth input can feed a multi-room group, stated" [P01 P02 P12 P15 P16 P19]: PASS.
- 6.8 (line 176) "Multi-room grouping exists; Bluetooth as the group source not established" [P13 P14 P21]: PASS.
- 6.9 (line 177) "Group membership has to be kept for sharing to continue, stated" [P16]: PASS.
- 6.10 (line 178) "Receiving device becomes group master automatically" [P02]: PASS.
- 6.11 (line 179) "The same product can also send audio out to a Bluetooth speaker or headphones" [P01 P06 P09 P12 P13]: PASS.
- 6.12 (line 180) "Bluetooth switched off in a particular speaker role (surround)" [P16 P19]: PASS.
- 6.13 (line 181) "NOT DETERMINED (whole field)" [P03 P17 P18]: PASS.

### 7. Error and failure communication

- 7.1 (line 187) "Symptom-keyed troubleshooting text in the manual or help pages" [P02 P08 P10 P11 P14 P17 P21]: PASS.
- 7.2 (line 188) "Light pattern for a failed Bluetooth pairing" [P14]: PASS.
- 7.3 (line 189) "Light pattern for non-Bluetooth faults (network, firmware, hardware)" [P12 P14]: PASS.
- 7.4 (line 190) "Message in the web page" [P01 (inf.)]: PASS.
- 7.5 (line 191) "Text logs (utility, dashboard, stderr/syslog, serial or telnet)" [P01 P02 P05 P06]: PASS.
- 7.6 (line 192) "Error value returned to a calling program" [P01 P20]: PASS.
- 7.7 (line 193) "Documentation's stated remedy is to re-pair, clear pairings, power-cycle, reboot, reset or reinstall" [P03 P04 P08 P10 P11 P14 P15 P17 P21]: PASS.
- 7.8 (line 194) "Documentation attributes an audio fault to radio interference on the onboard chip" [P02]: PASS.
- 7.9 (line 195) "NOT DETERMINED (whole field; any remedy text is listed above)" [P03 P04 P07 P13 P15 P16 P17 P18 P19]: PASS.

### 8. Programmatic surfaces

- 8.1 (line 201) "HTTP API on the device over the local network" [P01 P02 (inf.) P03 P12 P13 P15]: PASS.
- 8.2 (line 202) "... in which Bluetooth appears as a source or mode value" [P01 P12 P13 P15]: PASS.
- 8.3 (line 203) "... with commands specific to Bluetooth (restart or switch the service, answer a pairing request, set the auto-switch mode)" [P01 P13]: PASS.
- 8.4 (line 204) "... with no Bluetooth pairing or device-list command found" [P12 (inf.) P15 (inf.)]: PASS.
- 8.5 (line 205) "D-Bus interface" [P03 P05]: FAIL D. P03's record has no D-Bus statement; the nearest is "MPRIS for Bluetooth is provided through mpris-proxy" under Status feedback.
- 8.6 (line 206) "Environment variables" [P02]: PASS.
- 8.7 (line 207) "Configuration files or stored key-value parameters" [P03 P04 P06]: PASS.
- 8.8 (line 208) "Command-line utilities" [P01 P04 P05]: PASS.
- 8.9 (line 209) "Code-level class API (library or operating-system API)" [P07 P20]: PASS.
- 8.10 (line 210) "Change notification without repeated polling (long poll or event)" [P13 P20]: PASS.
- 8.11 (line 211) "Plug-in or extension mechanism" [P02 P03]: FAIL D. P02's record mentions per-plugin disable switches and a `<plugin>` part of the device name only; no plug-in or extension mechanism is described.
- 8.12 (line 212) "Driver packages for third-party control systems" [P13]: PASS.
- 8.13 (line 213) "Vendor app is the only surface named" [P09 P10]: PASS.
- 8.14 (line 214) "No programmatic surface described" [P08 (inf.) P11 (inf.)]: PASS.
- 8.15 (line 215) "NOT DETERMINED (whole field)" [P14 P16 P17 P18 P19 P21]: PASS.

## Observations that did not change a verdict

- P18 and P21 each cite a single URL, so one page was opened for each, not two.
- P13: the record's field text and the BLOCKED line in the tally say the definitions of the four Bluetooth modes are NOT DETERMINED. Two of the record's own cited pages carry them: S3 (the `/Settings?id=capture` example response: "Automatic mode switches to the Bluetooth source when a connected Bluetooth device starts playing audio") and S5 (staff post: "Automatic (default mode selected)"). This bears on pattern rows 2.22 and 5.18, which fail against the record but agree with those pages.
- P14: the record marks LED colours NOT DETERMINED; S1 reads "a pattern of two green flashes on the status LED".
- P02: the Controls statement "control is from the sending device (S3)" is graded STATED; S3 has no sentence about controls, so the statement is a reading of what the page leaves out.
- P18: the page title on S1 now reads "Play media on Google smart speaker or display using Bluetooth"; the record gives it as "Play media on your Google Nest speaker or display using Bluetooth". The body text matches the record.
- Row 1.4, P05: S1 says the daemon "shall be run as a root during system startup".
- Row 1.11, P15: S1 heads the setup section "First time usage & BLE WiFi Setup"; the page has the point, the record does not.
- Row 8.11, P02: S3 has a "Plugin system" section; the page has the point, the record does not.
- Rows passed on a loose match: 2.1 (P02: the record gives a phone-only procedure and no PIN; "discoverable" and "no action on the receiver" are not worded there), 3.6 and 8.13 (P09: the record names a "GAIA APP" for a reset setting and does not say whose app it is), 3.14 (P07: the record says volume synchronisation is named on a wiki page that was not opened), 5.15 (P02: the record names `SOUND_VOLUME` without the words start-up), 5.23 (P07: the stated defaults are I2S pin numbers), 6.7 (P02: the record's statement covers any received stream and does not single out Bluetooth).
- The Count column equals the number of P numbers listed in all 138 pattern rows.
- Check C and check E: of the words barred by rules.md Rule 6, one occurs in the two files, inside a quotation (P17, Output selection, "should"). Evaluative wording elsewhere is attributed to a named reviewer (P08 S3, P09 S3).

## Totals

| Set | PASS | FAIL |
|---|---|---|
| Product records (21) | 21 | 0 |
| Patterns (138) | 126 | 12 |
| All verdict lines (159) | 147 | 12 |

PASS: 147. FAIL: 12.
