# Verification of 01-products.md and 02-patterns.md

Verifier run, 2026-11-18. Checks as briefed: A (two cited URLs opened; do they carry the statements that cite them), B (a cited page confirms the product accepts Bluetooth audio), C (no field ranks, scores, judges or proposes), D (each P number under a pattern has a matching statement in that product's record), E (no pattern line judges or proposes). Nothing in the two files was changed.

This file replaces an earlier verification at the same path, which was written on 2026-11-17 against a 138-row version of `02-patterns.md` (commit c1e065a). The patterns file read in this run has 146 rows. No verdict below was carried over; every check was run again.

How the checks were run:

- S keys are the source keys in each record of `/home/user/audio/interfaces/01-products.md`; the URLs are listed there. Every page named below was opened on 2026-11-18. Except as noted in the next item, each was fetched by direct download with a browser-style User-Agent, and its text (PDFs converted to text) was read in full or searched for each statement that cites it.
- github.com HTML pages returned HTTP 403 to a direct download. P06 S2 was read through the fetch tool, which returns a model-written extract. P07 S2 was read through the fetch tool and also from the wiki's raw source, https://raw.githubusercontent.com/wiki/pschatzmann/ESP32-A2DP/A2DP-Sink.md (2026-11-18). No other source was affected.
- Check A is a sample: statements citing sources that were not opened are outside it. Standard applied: PASS when every statement citing an opened source was found on that source in substance; FAIL when such a statement is absent from the source or at odds with it. Differences of wording, of evidence grade, or of locale that leave the substance in place are listed under "Observations that did not change a verdict".
- Check B was answered from a page opened for check A; the confirming words are quoted (at most 25 words).
- Check C: every field of every record was read, and both files were searched for the words barred by rules.md Rule 6 and for evaluative wording.
- Check D was run against the record text only, not against the web pages. Standard applied: a row fails when, for at least one listed P number, the record has no statement matching the row, or has a statement at odds with it. Rows where the match rests on a looser reading are listed under Observations.
- Text on fetched pages addressed to an automated reader: none was met (Rule 10).

## Product records (checks A, B, C)

- P01 moOde audio player: PASS. A: opened S1, S4, S5 (also S3, S6). B: S3: "When a client connects to one of the renderers MPD will stop and the renderer will begin playback", Bluetooth being one; S6 lists `bluetooth` as a `source` value. C: none found.
- P02 IoTSound: PASS. A: opened S2, S3 (also S7, S8). B: S3: "If using Bluetooth: search for your device on your phone or laptop and pair." C: none found.
- P03 HiFiBerryOS (legacy): PASS. A: opened S1, S6 (also S4, S5, S7). B: S1: "Bluetooth A2DP sink"; S4: "act as a Bluetooth speaker". C: none found.
- P04 rpi-audio-receiver: PASS. A: opened S1, S2. B: S1: "audio receiver with Bluetooth (A2DP)". C: none found.
- P05 BlueALSA bluealsa-aplay: PASS. A: opened S1, S2. B: S2: "Capture audio streams from Bluetooth devices" and play them to an ALSA playback device. C: none found.
- P06 squeezelite-esp32: PASS. A: opened S1, S2 (S2 through the fetch tool). B: S1: "Stream from a **Bluetooth** device (iPhone, Android)". C: none found.
- P07 ESP32-A2DP: PASS. A: opened S1, S2 (S2 through the fetch tool and the raw wiki source). B: S1: "receive sound data e.g. from your Mobile Phone". C: none found.
- P08 Audioengine B1: PASS. A: opened S1, S2 (also S3). B: S2 quick setup: select "Audioengine B1" on the device, then "Play your music". C: none found.
- P09 iFi ZEN Blue 3: PASS. A: opened S1, S2 (also S3). B: S2: "RX mode for receiving Bluetooth signals from your smartphone and outputting to your Hi-Fi system". C: none found.
- P10 FiiO BR13: PASS. A: opened S2, S3 (also S5). B: S3: "How to use the BR13 as a Bluetooth receiver?". C: none found.
- P11 Pro-Ject BT Box S2 HD: PASS. A: opened S1, S2. B: S2: "play music from your Android device over Bluetooth to BT_Box_S2 HD". C: none found.
- P12 WiiM Pro: PASS. A: opened S2, S3 (also S1). B: S1 section 7.1: "stream audio from an external device, such as a smartphone or tablet, to the WiiM Pro". C: none found.
- P13 Bluesound NODE: PASS. A: opened S1, S3 (also S5). B: S1: Bluetooth connectivity "Two-Way (transmit & receive)"; S3 input item `inputType="bluetooth"`. C: none found. See the first observation below.
- P14 Denon Home 150: PASS. A: opened S1, S2 (also S4, S5, S7). B: S1: after pairing, "The sound will now play from the speaker." C: none found.
- P15 Arylic S10: PASS. A: opened S1, S2. B: S1 section 8.3.5 "To Play Bluetooth Mode": "Pair the device Bluetooth (SoundSystem) and play." C: none found.
- P16 Sonos Era 100: PASS. A: opened S1, S2. B: S1: "pair, disconnect, and reconnect your Bluetooth devices to your Era 100"; S2 lists Era 100 among products that share Bluetooth audio. C: none found.
- P17 Amazon Echo: PASS. A: opened S1, S4 (also S2, S3). B: S4: "stream music and other audio to the Echo device as if it were a standard portable Bluetooth speaker". C: none found.
- P18 Google Nest and Home: PASS. A: opened S1 (the record cites one URL; one opened). B: S1: "Connect your mobile device to your Google smart speaker or display with Bluetooth to listen to music, audiobooks, podcasts". C: none found.
- P19 Sonos Roam and Move 2: PASS. A: opened S1, S2. B: S1 and S2: each article covers how to "pair, disconnect, and reconnect your devices" to the speaker "via Bluetooth". C: none found.
- P20 Windows AudioPlaybackConnection: PASS. A: opened S1, S2. B: S2: "configuring a PC to behave like a Bluetooth speaker". C: none found.
- P21 JBL Authentics 200 and 500: PASS. A: opened S1 (the record cites one URL; one opened). B: S1 section 7.2 "Play from the Bluetooth source". C: none found.

## Patterns (checks D, E)

Row labels are the surface number and the row's position in that surface's tables, with the line number in `/home/user/audio/interfaces/02-patterns.md`. Check E found no line that judges or proposes; each verdict below is therefore the result of check D.

### 1. Setup and onboarding

- 1.1 (line 19) "Write an OS image to boot media, then finish configuration in a browser on the local network" [P01]: PASS.
- 1.2 (line 20) "OS image downloads per board model offered on the maker's page, with linked guides for installing and for Wi-Fi; installation steps not established for the legacy release examined" [P03]: PASS.
- 1.3 (line 21) "Create a fleet in a cloud device-management account, flash the image it issues, and let the application download to the device" [P02]: PASS.
- 1.4 (line 22) "Run an interactive install script in a shell on an existing OS, answering a prompt per component" [P04]: PASS.
- 1.5 (line 23) "Build and install from source; the daemon has to be running before a device can pair, connect and be used" [P05]: PASS.
- 1.6 (line 24) "Compile the function into firmware by writing code against a library" [P07]: PASS.
- 1.7 (line 25) "Device starts its own Wi-Fi access point; the user joins it and configures in a web page at a fixed address" [P06]: PASS.
- 1.8 (line 26) "Setup described as steps on the unit (power, with cabling or button presses) followed by pairing from the phone" [P08 P09 P10 P11]: PASS.
- 1.9 (line 27) "Maker states no software, drivers, apps, network setup or passwords are needed" [P08]: PASS.
- 1.10 (line 28) "Home-network connection together with the vendor mobile app comes first" [P12 P14 P15 P16 P17 P19 P21]: PASS.
- 1.11 (line 29) "Maker states Bluetooth cannot be used until the Wi-Fi/app setup has been done" [P16 P19]: PASS.
- 1.12 (line 30) "Bluetooth pairing has to be switched on in the vendor app before first use, and the phone is stated to need the same Wi-Fi network" [P18]: PASS.
- 1.13 (line 31) "Unit broadcasts over Bluetooth LE during Wi-Fi setup so that the app can find it" [P12]: PASS.
- 1.14 (line 32) "Phone has Bluetooth and location switched on while the app lists the device during Wi-Fi setup" [P15]: PASS.
- 1.15 (line 33) "An account with the maker's online service is part of setup" [P02 P17]: PASS.
- 1.16 (line 34) "A host application has to enable the sink; the operating system does not do it by itself" [P20]: PASS.
- 1.17 (line 35) "First start completes without user action after automatic reboots" [P01]: PASS.
- 1.18 (line 36) "Fallback path offered when the first network setup method fails (own hotspot or own Wi-Fi network)" [P01 P15]: PASS.
- 1.19 (line 37) "NOT DETERMINED (whole field)" [P13]: PASS.

### 2. Pairing and connection

- 2.1 (line 45) "Receiver is discoverable and accepts pairing with no action on the receiver and no code" [P02 P04]: PASS.
- 2.2 (line 46) "Pairing window opened by pressing or holding a button on the unit" [P08 P09 P10 P11 P12 P14 P16 P19 P21]: PASS.
- 2.3 (line 47) "Pairing enabled or started from a web page or vendor app" [P01 P03 P12 P15 P17 P18]: PASS.
- 2.4 (line 48) "Pairing window opened by a spoken command" [P17]: PASS.
- 2.5 (line 49) "Unit enters pairing mode by itself at power-on or when no known device is found" [P08 P09 P10 P12 P19]: PASS.
- 2.6 (line 50) "Pairing window has a stated time limit (1 or 2 minutes or until stopped; 60 seconds; three minutes)" [P03 P11 P21]: PASS.
- 2.7 (line 51) "Optional confirmation code shown and confirmed on both sides" [P01]: PASS.
- 2.8 (line 52) "Optional legacy PIN mode" [P02 P04]: PASS.
- 2.9 (line 53) "Pairing done with operating-system Bluetooth tools outside the product" [P04 P05 P20 (inf.)]: PASS.
- 2.10 (line 54) "Device name seen by the phone is set by the user or developer" [P01 P02 P04 P07]: PASS.
- 2.11 (line 55) "Device name seen by the phone is printed in the manual" [P08 P09 P11 P14 P15 P21]: PASS.
- 2.12 (line 61) "One device connected at a time, stated" [P04 P08]: PASS.
- 2.13 (line 62) "Two or more devices connected at once, stated" [P05 P10 P21]: PASS.
- 2.14 (line 63) "More than one client implied by plural wording" [P01 (inf.)]: PASS.
- 2.15 (line 64) "Number of remembered devices stated (six; eight; eight)" [P08 P09 P11]: PASS.
- 2.16 (line 65) "An action on the receiver lets a new device in while another is connected (button hold or app option)" [P08 P10 P12 P19 P21]: PASS.
- 2.17 (line 66) "Most recently paired device is the one connected" [P18]: PASS.
- 2.18 (line 67) "First device to start playing holds the output; others wait (option)" [P05]: PASS.
- 2.19 (line 68) "Discoverability switched off while a device is connected" [P04]: PASS.
- 2.20 (line 74) "Automatic reconnection to a known device, stated" [P02 P09 P10 P12 P16 P17 P19 P21]: PASS.
- 2.21 (line 75) "Automatic disconnect after an idle period (setting)" [P01 P16 P19]: PASS.
- 2.22 (line 76) "A Bluetooth mode setting with four named values (Manual, Automatic, Guest, Disabled); what each value does is not established" [P13]: PASS.
- 2.23 (line 77) "Clear-all-pairings action (web command, button hold, power-button sequence, or spoken command)" [P01 P10 P11 P18]: PASS.
- 2.24 (line 78) "Per-device connect, disconnect and forget from a web page or app" [P01 P12]: PASS.
- 2.25 (line 79) "Per-device unpair in the app's paired-devices list; connect and disconnect by spoken command" [P18]: PASS.
- 2.26 (line 80) "Pairing procedure not described in the pages opened" [P06 P07]: PASS.

### 3. Controls

- 3.1 (line 86) "Physical controls limited to power and pairing" [P08 P11]: PASS.
- 3.2 (line 87) "Physical controls beyond power and pairing (transport, volume, input, mode or source)" [P06 P09 P10 P12 P13 P15 P21]: PASS.
- 3.3 (line 88) "A button dedicated to Bluetooth" [P14 P16 P19 P21]: PASS.
- 3.4 (line 89) "Pairing shares a button or button combination with another function" [P08 P09 P10 P12 P19]: PASS.
- 3.5 (line 90) "Browser page served by the device" [P01 P02 (inf.) P03 P06]: PASS.
- 3.6 (line 91) "Vendor mobile app" [P09 P10 P12 P13 P14 P15 P16 P17 P18 P19 P21]: PASS.
- 3.7 (line 92) "Spoken commands for Bluetooth functions" [P17 P18]: PASS.
- 3.8 (line 93) "Voice assistant present, stated as not available in Bluetooth mode" [P21]: PASS.
- 3.9 (line 94) "Remote-control handset" [P06 P12 P13 P15]: PASS.
- 3.10 (line 95) "Shell commands" [P01 P04 P05]: PASS.
- 3.11 (line 96) "Cloud dashboard for device management" [P02]: PASS.
- 3.12 (line 97) "Controls are whatever the host application provides" [P20]: PASS.
- 3.13 (line 98) "Transport commands issued on the receiver, documented for Bluetooth (firmware calls sent to the phone; a playback button listed as working for Bluetooth)" [P07 P21]: PASS.
- 3.14 (line 99) "API transport commands forwarded to the Bluetooth renderer; the documented responses include an alert that playback cannot be controlled from the product for Bluetooth" [P01]: PASS.
- 3.15 (line 100) "Phone's volume requests applied on the receiver, documented (option or mode)" [P04 P05 P07]: PASS.
- 3.16 (line 101) "Volume ceiling applied to Bluetooth sessions" [P01]: PASS.
- 3.17 (line 102) "Playback started or controlled on the sending device, stated" [P02 P08 P17 P18]: PASS.
- 3.18 (line 103) "Volume adjusted on the sending device, stated" [P08]: PASS.

### 4. Status feedback

- 4.1 (line 109) "One light whose blink or steady state shows pairing and connected" [P08 P11 P12 P14 P16 P19 P21]: PASS.
- 4.2 (line 110) "Bluetooth-mode light blinks for searching and for pairing; no connected state stated" [P09]: PASS.
- 4.3 (line 111) "Light colour shows the codec in use" [P08 P09]: PASS.
- 4.4 (line 112) "Light colour shows the active input or mode" [P12 P15]: PASS.
- 4.5 (line 113) "Text or graphic display on the unit" [P06 P10]: PASS.
- 4.6 (line 114) "Chimes or tones at pairing and connection" [P16 P19]: PASS.
- 4.7 (line 115) "Spoken prompt or announcement" [P09 P17]: PASS.
- 4.8 (line 116) "Spoken answer to a status question" [P18]: PASS.
- 4.9 (line 117) "Overlay in the web page while Bluetooth is the active source, with track metadata where the phone provides it" [P01]: PASS.
- 4.10 (line 118) "Paired or connected devices listed in a web page or app" [P01 P12 P18]: PASS.
- 4.11 (line 119) "Status read through an API (source or mode value, state property, metadata)" [P01 P03 P12 P13 P15 P20]: PASS.
- 4.12 (line 120) "Status through command-line listing, logs or a cloud dashboard" [P02 P05]: PASS.
- 4.13 (line 121) "Indicator can be switched off by the user" [P09]: PASS.
- 4.14 (line 122) "Status light off unless enabled in the app" [P16]: PASS.
- 4.15 (line 123) "Maker's light-meaning table contains no Bluetooth state" [P17]: PASS.
- 4.16 (line 124) "Lights present, meanings not established" [P06 P13]: PASS.
- 4.17 (line 125) "None built in; left to the developer's code" [P07]: PASS.
- 4.18 (line 126) "NOT DETERMINED (whole field)" [P04]: PASS.

### 5. Settings exposed, and their defaults

- 5.1 (line 134) "Browser page served by the device" [P01 P03 P06]: PASS.
- 5.2 (line 135) "Environment variables in a cloud dashboard" [P02]: PASS.
- 5.3 (line 136) "Hand-edited configuration files or command-line options" [P04 P05]: PASS.
- 5.4 (line 137) "Source code" [P07]: PASS.
- 5.5 (line 138) "Vendor mobile app" [P10 P12 P14 P15 P16 P17 P18 P19 P21]: PASS.
- 5.6 (line 139) "Buttons on the unit" [P09 P10]: PASS.
- 5.7 (line 140) "No user setting described" [P08 (inf.) P11 (inf.) P20 (inf.)]: PASS.
- 5.8 (line 146) "Bluetooth on/off" [P01 P02 P03 P13]: PASS.
- 5.9 (line 147) "Bluetooth device name" [P01 P02 P04 P07]: PASS.
- 5.10 (line 148) "Pairing security (confirmation on/off, pairing mode, PIN)" [P01 P02 P04]: PASS.
- 5.11 (line 149) "Pairing window length" [P03]: PASS.
- 5.12 (line 150) "Buffer or latency" [P01 P02 P05]: PASS.
- 5.13 (line 151) "Codec quality or codec enable list" [P01 P10]: PASS.
- 5.14 (line 152) "Equaliser applied to Bluetooth input" [P10]: PASS.
- 5.15 (line 153) "Start-up volume or volume ceiling" [P01 P02]: PASS.
- 5.16 (line 154) "Idle auto-disconnect" [P01 P16 P19]: PASS.
- 5.17 (line 155) "Automatic reconnect to the last device" [P19]: PASS.
- 5.18 (line 156) "Bluetooth mode (four values; what each value does is not established)" [P13]: PASS.
- 5.19 (line 157) "Resume the previous source after Bluetooth ends" [P01]: PASS.
- 5.20 (line 158) "Indicator light or voice prompt on/off" [P09 P16]: PASS.
- 5.21 (line 159) "Renaming or hiding inputs" [P14]: PASS.
- 5.22 (line 160) "Paired-device list management" [P01 P12 P17 P18]: PASS.
- 5.23 (line 166) "At least one Bluetooth-related default stated in the documentation" [P01 P02 P05 P07 P09 P16 P19]: PASS.
- 5.24 (line 167) "Stated defaults that leave a feature on: LED display and voice prompt (P09); secure simple pairing without PIN (P02)" [P02 P09]: PASS.
- 5.25 (line 168) "Stated defaults that leave a feature off: idle auto-disconnect (P16 P19); auto-connect (P19); status light (P16)" [P16 P19]: PASS.
- 5.26 (line 169) "Default auto-switch mode stated only by maker staff in a forum" [P13]: PASS.
- 5.27 (line 170) "Settings exist, defaults not established" [P03 P10 P12 P17 P18]: PASS.

### 6. Output or destination selection

- 6.1 (line 176) "Fixed set of output connectors, no selector described" [P08 P09 P10 P11]: PASS.
- 6.2 (line 177) "Output device picked in a browser page" [P01]: PASS.
- 6.3 (line 178) "Output set by an environment variable, with an automatic detection order as the default" [P02]: PASS.
- 6.4 (line 179) "Output set in a configuration file, command-line option, stored parameter or code" [P04 P05 P06 P07]: PASS.
- 6.5 (line 180) "Output picked in the vendor app" [P12]: PASS.
- 6.6 (line 181) "Plays on the host system's audio endpoints" [P20]: PASS.
- 6.7 (line 182) "Bluetooth input can feed a multi-room group, stated" [P01 P02 P12 P15 P16 P19]: PASS.
- 6.8 (line 183) "Multi-room grouping exists; Bluetooth as the group source not established" [P13 P14 P21]: PASS.
- 6.9 (line 184) "Group membership has to be kept for sharing to continue, stated" [P16]: PASS.
- 6.10 (line 185) "Receiving device becomes group master automatically" [P02]: PASS.
- 6.11 (line 186) "The same product can also send audio out to a Bluetooth speaker or headphones" [P01 P06 P09 P12 P13]: PASS.
- 6.12 (line 187) "Bluetooth switched off in a particular speaker role (surround)" [P16 P19]: PASS.
- 6.13 (line 188) "NOT DETERMINED (whole field)" [P03 P17 P18]: PASS.

### 7. Error and failure communication

- 7.1 (line 194) "Symptom-keyed troubleshooting text in the manual or help pages" [P02 P08 P10 P11 P14 P17 P21]: PASS.
- 7.2 (line 195) "Light pattern for a failed Bluetooth pairing" [P14]: PASS.
- 7.3 (line 196) "Light pattern for non-Bluetooth faults (network, firmware, hardware)" [P12 P14]: PASS.
- 7.4 (line 197) "Message in the web page" [P01 (inf.)]: PASS.
- 7.5 (line 198) "Text logs (utility, dashboard, stderr/syslog, serial or telnet)" [P01 P02 P05 P06]: PASS.
- 7.6 (line 199) "Error value returned to a calling program" [P01 P20]: PASS.
- 7.7 (line 200) "Documentation's stated remedy is to re-pair, clear pairings, power-cycle, reboot, reset or reinstall" [P03 P04 P08 P10 P11 P14 P15 P17 P21]: PASS.
- 7.8 (line 201) "Documentation attributes an audio fault to radio interference on the onboard chip" [P02]: PASS.
- 7.9 (line 202) "NOT DETERMINED (whole field; any remedy text is listed above)" [P03 P04 P07 P13 P15 P16 P17 P18 P19]: PASS.

### 8. Programmatic surfaces

- 8.1 (line 208) "HTTP API on the device over the local network" [P01 P02 (inf.) P03 P12 P13 P15]: PASS.
- 8.2 (line 209) "... in which Bluetooth appears as a source or mode value" [P01 P12 P13 P15]: PASS.
- 8.3 (line 210) "... with commands specific to Bluetooth (restart or switch the service, answer a pairing request, set the auto-switch mode)" [P01 P13]: PASS.
- 8.4 (line 211) "... with no Bluetooth pairing or device-list command found" [P12 (inf.) P15 (inf.)]: PASS.
- 8.5 (line 212) "D-Bus interface" [P05]: PASS.
- 8.6 (line 213) "MPRIS for Bluetooth provided through mpris-proxy (recorded under Status feedback)" [P03]: PASS.
- 8.7 (line 214) "Environment variables" [P02]: PASS.
- 8.8 (line 215) "Configuration files or stored key-value parameters" [P03 P04 P06]: PASS.
- 8.9 (line 216) "Command-line utilities" [P01 P04 P05]: PASS.
- 8.10 (line 217) "Code-level class API (library or operating-system API)" [P07 P20]: PASS.
- 8.11 (line 218) "Change notification without repeated polling (long poll or event)" [P13 P20]: PASS.
- 8.12 (line 219) "Plug-in or extension mechanism" [P03]: PASS.
- 8.13 (line 220) "Driver packages for third-party control systems" [P13]: PASS.
- 8.14 (line 221) "Vendor app is the only surface named" [P09 P10]: PASS.
- 8.15 (line 222) "No programmatic surface described" [P08 (inf.) P11 (inf.)]: PASS.
- 8.16 (line 223) "NOT DETERMINED (whole field)" [P14 P16 P17 P18 P19 P21]: PASS.

## Observations that did not change a verdict

Product records:

- P13, Bluetooth modes. The BLOCKED line in the tally of `01-products.md` (line 503) says the definitions of the four Bluetooth modes are NOT DETERMINED, and rows 2.22 and 5.18 of `02-patterns.md` say "what each value does is not established". Two pages that the P13 record cites, both opened in this run, carry the definitions: S3, in the example response to `/Settings?id=capture` ("Automatic mode switches to the Bluetooth source when a connected Bluetooth device starts playing audio", with matching sentences for Manual, Guest and Disabled), and S5, in a staff post that lists the four modes and marks "Automatic (default mode selected)". The statement sits in the tally, not in the P13 record, and the two pattern rows match the record, so checks A and D pass; the record and the rows differ from the cited pages. The same patterns file names this setting "auto-switch mode" in rows 5.26 and 8.3 and lists P13 under "Bluetooth on/off" in row 5.8, which presupposes what the values do.
- P18, quoted wording. The record gives the S1 page title as "Play media on your Google Nest speaker or display using Bluetooth" and quotes "Google Nest speaker or display" in its Version field. S1 as opened on 2026-11-18 is titled "Play media on Google smart speaker or display using Bluetooth" and its body uses "Google smart speaker or display"; the quoted wording does not occur on the page. The statements in the record's fields were found.
- P09, price and stock. S1 as served to this session shows $299.00 (store country US, currency USD) and schema.org availability `InStock`. The record gives £299 and "In stock"; the visible page text has no "In stock" string. The page source for this session carries store country US, so the currency shown may follow the visitor's country [INFERRED from that setting].
- P02, Controls. "control is from the sending device (S3)" is graded STATED. S3 has no sentence on where playback is controlled; every usage step it gives is carried out on the sending device, so the statement is a reading of the page. Row 3.17 lists P02 under "... on the sending device, stated". In Error communication, "service logs viewed in the dashboard" cites S8; S8 names service logs and does not contain the word dashboard.
- P05, Pairing. "Not handled by BlueALSA itself; done with BlueZ tools" is graded STATED and attributed to a wiki guide named in S1; that guide is not among the record's sources. S1 itself says a wiki guide covers "Bluetooth pairing and connecting procedures on Linux".
- P14, Status feedback. The record marks LED colours NOT DETERMINED; S1 reads "a pattern of two green flashes on the status LED". S2 says "The LEDs on the front panel"; the record says one status LED.
- P20, Error communication. "`TryCreateFromId` returns null when no connection is available for the device (S2)": S2 shows a null test in its sample code and has no sentence giving the condition.
- P09, Status feedback. "Voice prompts announce Bluetooth events (S2)": S2 names a "BT voice prompt" that is switched on or off and does not say what is announced. The record's own Not determined line lists the content of voice prompts.
- P17, Status feedback. S3 also has a "Teal Blue" entry that the record's list of colours does not name; that entry has no Bluetooth meaning either.
- P15, Controls. The record says "infrared remote"; the text extracted from S1 says "remote control" and has no "infrared" or "IR". PDF pages 2 to 8 of 24 yielded no text on extraction and were not read.
- P18 and P21 each cite a single URL, so one page was opened for each, not two.
- Outside the five checks, against Rule 11: three records reproduce default credential values printed in public maker documents (P06 Setup: default access-point SSID and password; P02 Pairing and Settings: default PIN; P11 Pairing: legacy pairing code). The values are not repeated here.

Patterns, rows passed on a looser reading of the record:

- 1.8 (P09): the record's Setup field gives steps on the unit and marks their order INFERRED; pairing from the phone is in its Pairing field. The row carries no (inf.) mark for P09.
- 1.17 (P01): the record says the first start "reboots three times automatically"; "without user action" is not worded there.
- 2.1 (P02): the record gives a phone-only procedure and no PIN; "discoverable" and "no action on the receiver" are not worded there.
- 2.3 (P15): the record has the user switch to Bluetooth in the app or with the remote and then pair from the phone.
- 2.20 (P16): the record's reconnection follows a press of the Bluetooth button on the speaker.
- 2.23 (P11): the record calls the power-button sequence "a memory reset" and does not say it removes pairings.
- 3.6 and 8.14 (P09): the record names a "GAIA APP" for a reset setting and does not say whose app it is.
- 3.15 (P07): the record says volume synchronisation is named on a wiki page that was not opened.
- 4.8 (P18): the record lists the spoken status questions and marks what the speaker says NOT DETERMINED.
- 5.8, 5.26 and 8.3 (P13): see the first observation above.
- 5.15 (P02): the record names `SOUND_VOLUME` without the words start-up.
- 5.23 (P07): the stated defaults are I2S pin numbers.
- 6.7 (P02): the record's statement covers any received stream and does not single out Bluetooth.
- 8.9 (P04): the record lists a shell script, systemd units, a udev rule and configuration files.

Other:

- The Count column equals the number of P numbers listed in all 146 pattern rows.
- The NOT DETERMINED table in the tally of `02-patterns.md` (20 whole, 47 partly) equals the table in the tally of `01-products.md`, surface by surface. The five whole-field rows (1.19, 4.18, 6.13, 7.9, 8.16) list the same P numbers as that table.
- The twelve rows that failed check D in the earlier verification have each been reworded, split, or had the P number taken out; none of those twelve mismatches is present in the file read in this run.
- Check C and check E: of the words barred by rules.md Rule 6, one occurs in the two files, inside a quotation (P17, Output selection). Evaluative wording elsewhere is attributed to a named reviewer or to the maker's own document (P03 S5, P08 S3, P09 S3).

## Totals

| Set | PASS | FAIL |
|---|---|---|
| Product records (21) | 21 | 0 |
| Patterns (146) | 146 | 0 |
| All verdict lines (167) | 167 | 0 |

PASS: 167. FAIL: 0.
