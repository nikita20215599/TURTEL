# PLUGIN PUBLIC API — TurtleAV‑Chazy‑Control (`.qplug`)

This document describes **all public-facing “API” surfaces** of the plugin:

- **Plugin Properties** (configured in the Q‑SYS Designer Properties pane)
- **Controls / Pins** (what you wire to in the schematic)
- **On‑device commands used** (what gets sent to Chazy Control)

## Plugin Properties

- **Host** (`string`)
  - Chazy Control IP/hostname (example `192.168.6.100`)
- **Port** (`integer`, default `23`)
  - TELNET port used by Chazy Control API (see `docs/Chazy-Control-API.pdf`)
- **Auth Mode** (`enum`)
  - `None` (default)
  - `Username/Password (best-effort)` (only used if the TELNET session prompts)
- **Username** (`string`)
  - Hidden unless Auth Mode is enabled
- **Password** (`string`)
  - Hidden unless Auth Mode is enabled
- **RX Count** (`integer`, 1–64)
  - Number of RX rows to expose as controls/pins
- **TX Count** (`integer`, 1–64)
  - Number of TX preview URL outputs to expose
- **Poll Interval (s)** (`integer`, 0–60)
  - `0` disables polling
- **Preview URL Mode** (`enum`)
  - `Off`
  - `Fetch TX MSURL (recommended)` (default)
- **Debug** (`enum`)
  - `Off`, `Errors`, `Info`, `Verbose`

## Controls / Pins

### Session controls

- **`Connect`** (Button, Trigger, **Input pin**)
  - Connects to Chazy Control via TCP.
- **`Disconnect`** (Button, Trigger, **Input pin**)
  - Disconnects socket and clears pending command queue.
- **`Refresh`** (Button, Trigger, **Input pin**)
  - Triggers an immediate status refresh.
- **`FetchPreviewUrls`** (Button, Trigger, **Input pin**)
  - Re-fetches all TX preview URLs (MSURL) and updates outputs.

- **`Connected`** (Indicator, LED, **Output pin**)
  - `1` when connected; `0` otherwise.
- **`Status`** (Indicator, Text, **Output pin**)
  - Human readable status string (connecting, connected, reconnecting, etc.).
- **`LastError`** (Indicator, Text, **Output pin**)
  - Latest socket/protocol error (best-effort).
- **`LastResponse`** (Indicator, Text, **Output pin**)
  - Raw text response captured for the last command (useful for diagnostics).

### Routing controls (per RX)

For each RX `NN` from `01..RX Count`:

- **`RxNN_SelectTx`** (Knob, Integer, **Input pin**)
  - Desired TX number to route to RX `NN`.
  - Expected values: `0..TX Count` (the plugin clamps out-of-range).
- **`RxNN_Take`** (Button, Trigger, **Input pin**)
  - Executes the route command for RX `NN` using current `RxNN_SelectTx`.
- **`RxNN_Clear`** (Button, Trigger, **Input pin**)
  - Clears routing for RX `NN` (routes to `0`).
- **`RxNN_ActiveTx`** (Indicator, Text, **Output pin**)
  - Active routed TX (as reported by `GET DEC ... STATUS` parsing). Displays `—` if unrouted/unknown.
- **`RxNN_PreviewUrl`** (Indicator, Text, **Output pin**)
  - Preview URL for the TX currently routed to this RX.

### Preview outputs (per TX)

For each TX `NN` from `01..TX Count`:

- **`TxNN_MSURL`** (Indicator, Text, **Output pin**)
  - “Mainstream” preview URL returned by `GET ENC <tx> SS MSURL`.
  - Gen 2 devices are documented as **MJPEG** URLs.
  - Gen 1 devices are documented as **RTSP** URLs.

## Device commands used by this plugin

All commands are from the published “Chazy Control API” (see `docs/Chazy-Control-API.pdf`).

### Connect / session

No special “handshake” command is required; commands are sent as lines terminated by `\r\n`.

### Status and parsing

- `GET STATUS`
  - Used for basic “is the controller responding” and general info.
- `GET DEC STATUS`
  - Used to infer each RX’s current routed TX.

### Routing

- `SET DEC <rx> SWITCH <tx> ALL`
  - Routes RX video/audio/IR/RS‑232/USB/CEC from TX.
- `SET DEC <rx> SWITCH 0 ALL`
  - Cancels routing (“unselect encoder”).

### Preview URL discovery

- `GET ENC <tx> SS MSURL`
  - Returns TX secondary stream “mainstream” URL.

