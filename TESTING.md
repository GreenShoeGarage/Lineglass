# LINEGLASS 1.0.0 — Verification Notes

## Automated/static verification performed

- Extracted both embedded JavaScript blocks and passed them through `node --check` without syntax errors.
- Loaded the self-contained page in a Chromium automation environment and verified that the application shell renders.
- Verified visible application name and version.
- Loaded all ten internal demonstration captures through the actual UI and waited for worker decoding. Verified expected frame counts: 60 ASCII temperature, 96 SCPI, 160 valid Modbus, 68 faulted Modbus, 180 NMEA, 180 fixed binary, 120 variable binary, 80 dual-tap, 420 unknown-protocol, and 5,000 high-volume frames.
- Verified that frame rows, statistics, linked inspector content, the 180-point NMEA local track, Modbus checksum-failure counts, unknown-protocol clustering/candidate analysis, evidence report generation, and mode switching update from the shared capture data.
- Imported all ten external `.lineglass.json` sample sessions through the actual file-input workflow and verified their frame counts and decoder selections.
- Imported the included plain-text, hexadecimal, NMEA text, and timestamped CSV examples through their normal file paths.
- Verified that no uncaught page exception or filtered console error occurred during the comprehensive browser workflows.
- Verified that every HTML control identifier is unique.
- Verified that the package contains no public CDN, remote font, telemetry, analytics, or cloud API dependency.
- Verified SHA-256 package manifests after packaging.

## Browser-origin limitation of the build environment

The automation environment blocks direct navigation to arbitrary local HTTP/file origins. The UI smoke test therefore injected the complete document into a browser page rather than navigating to the local server. Under that opaque test origin, `localStorage` and IndexedDB are intentionally unavailable; the resulting autosave error state was expected and did not indicate an application logic failure.

The release should be run from `http://localhost` or an HTTPS origin for normal browser storage and direct serial access.

## Hardware-dependent acceptance items

The following require a physical adapter and target/loopback fixture and could not be truthfully validated inside the artifact build environment:

- Opening and reading an actual operating-system serial port
- Adapter-specific arbitrary baud-rate acceptance
- Real unplug/replug and browser-suspension recovery
- DTR, RTS, CTS, DSR, DCD, RI, and break behavior on a particular adapter/driver
- RS-485 automatic-direction and RTS-direction timing
- Dual-port simultaneous physical capture
- Actual parity/framing/break/overrun reporting where supported by hardware
- Electrical voltage, polarity, grounding, termination, bias, and isolation

The application feature-detects and handles these operations, but the result depends on browser, operating system, driver, adapter, and connected equipment.

## Recommended hardware acceptance procedure

1. Start LINEGLASS from `localhost` in a Web-Serial-capable desktop browser.
2. Use a USB-to-TTL loopback fixture first; verify binary values `00 7F 80 FF` round-trip without corruption.
3. Unplug and reconnect while capturing; confirm the interface recovers and TX remains locked.
4. Verify 9600 8N1, then one parity and one alternate stop-bit configuration with a known source.
5. Verify finite repetition and the immediate **STOP TRANSMISSION** control on an isolated loopback target.
6. Verify DTR/RTS and input signals only with an adapter that exposes them.
7. Test RS-485 with an isolated auto-direction adapter before testing RTS-controlled direction.
8. Compare the exported/reimported session frame count, byte count, decoder results, and SHA-256 raw-capture hash.
9. Repeat with the display paused to confirm recording continues.
10. Perform production-equipment tests only under an authorized and reviewed procedure.
