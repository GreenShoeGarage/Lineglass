# LINEGLASS — Serial Communications Workbench

**Version 1.0.0**

LINEGLASS is a local-first browser instrument for connecting to serial adapters, preserving raw bytes, framing messages, decoding serial protocols, visualizing behavior, replaying sessions, comparing captures, and exporting engineering evidence.

> **Guiding principle:** Bytes become frames. Frames become behavior.

The application is delivered as one self-contained file: `lineglass.html`. It does not require a build system, public CDN, cloud account, telemetry service, remote font, or analytics service.

## Package contents

```text
lineglass-build/
├── lineglass.html               Complete application
├── README.md                    This guide
├── CHANGELOG.md                 Release history
├── TESTING.md                   Verification notes and acceptance coverage
├── SHA256SUMS.txt               Package file hashes
└── samples/
    ├── 01-...lineglass.json     Timestamped importable sample sessions
    ├── ...
    ├── 10-...lineglass.json
    ├── ascii-temperature.txt    Plain-text import example
    ├── modbus-valid.hex         Hex-log import example
    ├── nmea-gps.txt             NMEA text import example
    ├── timestamped-capture.csv  Timestamp/direction CSV import example
    ├── manifest.json            Machine-readable sample index
    └── SHA256SUMS.txt           Sample hashes
```

## Start LINEGLASS

A local web server is the recommended way to run the application. It gives the page a stable local origin and is required for direct serial access in browsers that expose Web Serial only in secure contexts.

### macOS or Linux

```bash
cd lineglass-build
python3 -m http.server 8080
```

Open:

```text
http://localhost:8080/lineglass.html
```

### Windows

```powershell
cd lineglass-build
py -m http.server 8080
```

Open the same localhost address.

Opening `lineglass.html` directly from disk may still permit imported captures and demonstrations, but browser storage and direct hardware behavior can vary under a `file:` origin.

## Browser and hardware access

LINEGLASS feature-detects `navigator.serial` at runtime. Direct hardware access requires a desktop browser/environment that implements the Web Serial API, allows it for the page’s origin, and has an operating-system driver for the adapter. The user must choose a port from the browser’s device picker; the application cannot silently enumerate every attached device.

Authoritative references:

- Web Serial draft: `https://wicg.github.io/serial/`
- Chrome implementation guide: `https://developer.chrome.com/docs/capabilities/serial`

When Web Serial is unavailable, every analysis feature remains usable with imported or demonstration captures.

## Essential workflow

1. Open **Connect** and acknowledge the hardware safety checks.
2. Select RS-232, RS-485, RS-422, TTL UART, or Generic Serial.
3. Configure baud rate, data bits, parity, stop bits, and flow control.
4. Connect a port or deliberately load a sample capture.
5. Start capture and inspect synchronized Text, Hex, and Mixed views.
6. Select or test a framing rule.
7. Choose a decoder.
8. Inspect frames and decoded fields.
9. Build visualizations or explore an unknown protocol.
10. Save, replay, compare, and export the evidence.

LINEGLASS opens in **Monitor Mode**. Transmission is locked on every launch, reload, reconnect, project import, and session import.

## Implemented capabilities

### Connection and capture

- Web Serial feature detection and user-selected ports
- Independent Port A and Port B settings
- Previously authorized-port reconnection
- USB vendor/product identifiers when exposed
- Clean reader/writer cancellation and disconnect recovery
- Port signals where supported: CTS, DSR, DCD, RI, DTR, RTS, and break
- Raw byte preservation, including NUL and bytes above `0x7F`
- Host-arrival wall-clock and monotonic relative timestamps
- Display pause independent of recording
- Bookmarks, events, annotations, and session splitting
- Bounded live rendering and configurable in-memory limits
- Chunked IndexedDB session storage where available

### Raw and framed views

- Text, hexadecimal, and mixed escaped views
- Visible control characters
- Offset, channel, direction, time, and frame boundaries
- Framing rules for chunks, CR, LF, CR/LF, arbitrary delimiter, fixed length, inactivity gap, STX/ETX, start/end delimiters, length-prefixed data, COBS, and SLIP
- Framing preview, malformed/leftover counts, and saved framing profiles

### Decoders

- Raw UART
- Modbus RTU with CRC validation and request/response pairing
- Modbus ASCII with LRC validation
- NMEA 0183 with checksum validation and navigation fields
- ASCII command/response traffic
- Declarative custom binary schemas with numeric, text, bit-field, scaled, and chartable fields
- Additive, XOR, LRC, CRC-8, CRC-16, CRC-32, and Modbus CRC support in the schema/integrity workbench
- SLCAN serial transport decoding for compatible USB-CAN adapters
- Registry placeholders and evidence-aware descriptions for BACnet MS/TP, DMX/RDM, LIN, and IEC 62056-21 hardware bridges

### Analysis and visualization

- Search and filtering by channel, direction, decoder, integrity state, text, hex, and time
- Linked frame inspector with byte and field boundaries
- Activity timeline
- Sequence diagram
- Numeric field plots
- Traffic rate and estimated line utilization
- Inter-frame gap distribution
- Frame-length histogram
- Byte heatmap
- Local NMEA position track
- Frame clustering by length, prefix, suffix, and channel
- Byte-position entropy/change analysis
- Candidate counter, state, constant, and integrity-field suggestions with confidence and reasoning
- Integrity explorer and periodicity summary
- Named baselines and capture comparison

### Replay, transmit, and evidence

- Replay at 0.1× through maximum speed, plus frame stepping
- Separate replay and transmit paths; replay never transmits automatically
- Text, hex, escaped-byte, and file send
- Exact transmit-byte preview
- CR, LF, CR/LF, or no line ending
- Named command library, finite repetition, timeout/stop conditions, rate limits, and immediate stop
- Every transmitted byte written to the capture as TX evidence
- Project JSON, session JSON, raw binary, hex, text, frame CSV, decoded-field CSV, chart PNG, and self-contained HTML report exports
- SHA-256 raw-capture hashes through browser cryptography
- Local autosave indicator and explicit Fresh Start/Clear Local Data controls
- Easy and Advanced modes that materially change the visible interface
- Dark, light, system, and high-contrast themes

## Sample captures

The ten JSON samples under `samples/` preserve timestamps, channels, directions, decoder choice, framing configuration, and custom schemas. Load one through:

**Sessions → Import Capture → choose a `.lineglass.json` file**

The application also includes the same ten demonstrations internally. Internal demonstrations are loaded only when the user explicitly selects **Load Demonstration Capture**.

No sample data is placed into a new project automatically.

## Important measurement limits

LINEGLASS reports what a browser and conventional serial adapter can actually observe:

- Normal timestamps are **host arrival timestamps**, not wire-edge timestamps.
- A USB serial adapter provides decoded bytes, not an analog RS-232 or RS-485 voltage waveform.
- Estimated byte timing and line utilization are labeled as estimates.
- A single RS-232 receive channel normally observes one direction.
- A two-wire RS-485 capture may contain both directions without revealing the physical transmitter.
- Inferred direction is labeled as inferred and should not be treated as certainty.
- Generic adapters may not expose parity, framing, break, or hardware-overrun events.
- LINEGLASS cannot verify voltage standard, pinout, polarity, isolation, common reference, termination, or bias.
- A LINEGLASS report does not prove electrical compliance, protocol conformance, functional safety, or authorization to transmit.

Logic-analyzer-grade timing and oscilloscope-grade voltage evidence require suitable external hardware.

## Hardware safety

Before connecting to equipment:

- Verify the electrical standard and pinout. RS-232 is not TTL UART.
- Confirm TTL logic voltage before connecting a TTL adapter.
- Treat RS-485 A/B labels as equipment-specific; consult both manuals.
- Prefer an isolated adapter around industrial equipment or uncertain ground potential.
- Begin in receive-only Monitor Mode.
- Do not add termination or bias to an operating bus without understanding the existing topology.
- Do not transmit to safety-critical or production equipment without authorization and a reviewed procedure.

## Local data model

Lightweight project settings are stored in local browser storage. Larger saved sessions are stored in IndexedDB when available. Export important work as project/session files; clearing browser site data also clears browser-resident LINEGLASS data.

LINEGLASS does not send projects, serial traffic, adapter identifiers, or usage data to a server.

## Keyboard shortcuts

- `Ctrl/Cmd + K` — focus frame search
- `Ctrl/Cmd + S` — save the current session locally
- `Escape` — stop repeating transmission before closing dialogs

## Troubleshooting

### “Web Serial unavailable”

Use a browser that exposes `navigator.serial`, serve the file from `localhost` or another permitted secure origin, and verify that device access is not blocked by browser or enterprise policy. Imported and sample captures remain available.

### The adapter appears but will not open

Close other serial terminals, development tools, or background services that may own the port. Confirm the operating-system driver, reconnect the adapter, and use **Reconnect Previously Authorized Port**.

### Data is unreadable

Verify electrical standard, voltage, wiring, baud rate, data bits, parity, stop bits, inversion, and grounding. The Baud Finder ranks candidate settings heuristically; it does not measure the electrical bit period.

### Frames do not align

Open **Frames**, choose the appropriate rule, and use **Test Rule** before applying. Try delimiter, fixed-length, inactivity-gap, start/end, or length-prefixed framing depending on the observed traffic.

### The report hash says “Unavailable”

Browser cryptography may be unavailable under an unusual or restricted origin. Run from `localhost` or an HTTPS origin and regenerate the report.

## Development notes

The application is intentionally dependency-free. Decoder work runs in an inline Web Worker created from a Blob URL where the browser permits it. Canvas is used for bounded charts and heatmaps. Long data tables render a limited window instead of inserting one DOM node per recorded frame.

See `TESTING.md` for the verification performed on this release and the hardware-dependent acceptance items that require a real adapter/device.
