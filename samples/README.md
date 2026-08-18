# LINEGLASS Sample Captures

These files are synthetic, deterministic engineering demonstrations. Load them deliberately from **Sessions → Import Capture**. They are not recordings from production equipment.

| File | Decoder | Framing | Events | Bytes | Purpose |
|---|---|---:|---:|---:|---|
| `01-rs232-ascii-temperature.lineglass.json` | ascii | crlf | 60 | 840 | One line per second from an ASCII temperature instrument using CR/LF termination. |
| `02-rs232-scpi-command-response.lineglass.json` | ascii | crlf | 96 | 1,404 | Alternating SCPI-style commands and instrument responses with host-arrival response timing. |
| `03-rs485-modbus-valid.lineglass.json` | modbus-rtu | gap | 160 | 1,360 | Valid function 03 requests and responses with changing register values and correct Modbus CRCs. |
| `04-modbus-errors-timeouts-exception.lineglass.json` | modbus-rtu | gap | 68 | 564 | A diagnostic capture containing invalid CRCs, long unanswered request gaps, and exception responses. |
| `05-nmea0183-gps.lineglass.json` | nmea | crlf | 180 | 12,837 | Valid GGA and RMC sentences forming a small local position track with speed and heading changes. |
| `06-custom-fixed-binary-sensor.lineglass.json` | custom | fixed | 180 | 1,620 | Nine-byte frames with sync, address, signed scaled temperature, a counter, flags, and additive checksum. |
| `07-custom-variable-length-crc.lineglass.json` | custom | length | 120 | 840 | Length-prefixed seven-byte telemetry frames with a scaled pressure field and CRC-16 integrity check. |
| `08-dual-channel-rs232-tap.lineglass.json` | ascii | crlf | 80 | 830 | Two receive-only channels capture controller and device conductors independently; direction is not asserted. |
| `09-unknown-binary-protocol.lineglass.json` | raw | fixed | 420 | 4,200 | A ten-byte opaque protocol designed for clustering, byte-position entropy, counters, state fields, and checksum exploration. |
| `10-high-volume-rendering-test.lineglass.json` | custom | fixed | 5,000 | 45,000 | Five thousand compact sensor frames for testing bounded rendering, charting, replay, and local storage behavior. |

The `.txt`, `.hex`, and `.csv` files exercise LINEGLASS’s simpler import paths. The JSON files retain timestamps, channel labels, directions, decoder selection, framing configuration, and custom schemas.
