# DEVELOPER REFERENCE — TurtleAV‑Chazy‑Control Plugin

This document is for maintaining/extending `plugin/TurtleAV-Chazy-Control.qplug`.

## Architecture summary

- **Transport**: `TcpSocket` (Q‑SYS Lua) to Chazy Control **TELNET port** (default `23`)
- **Protocol**: line-based ASCII commands terminated by `\r\n`
- **Execution model**:
  - Commands are **queued** and executed one at a time (`queue` + `inFlight`)
  - Responses are captured as lines into `respLines`
  - A short **idle timer** (`respIdleTimer`, 0.2s) determines “response complete”

## Public vs internal surfaces

- Public/user-facing API is documented in `docs/PLUGIN_PUBLIC_API.md`.
- Everything below is internal implementation detail.

## Key internal functions

### Command queue

- `enqueue(cmd, parser)`
  - Adds a command to the queue and sends immediately if idle/connected.

### Response finalization

- `respIdleTimer.EventHandler`
  - Concatenates `respLines` into `raw`
  - Writes `raw` to `Controls.LastResponse`
  - Calls the per-command `parser(lines, raw)` inside `pcall`
  - Sends the next queued command

### TELNET negotiation stripping (best-effort)

- `sanitizeTelnetGarbage(s)`
  - Removes sequences starting with byte `0xFF` (TELNET IAC) by skipping 3 bytes.
  - This is intentionally minimal; if a specific firmware emits more complex negotiation,
    extend this into a proper TELNET filter.

### Parsing

- `parseSuccessOrError(lines)`
  - Searches for `[ERROR]...` or `[SUCCESS]...` in the response.
- `parseRxStatusForRoutes(lines)`
  - Looks for the “Vid /Aud /IR /Ser /USB /CEC” line and parses the next line’s
    `NNN / NNN / ...` values.
  - Uses the first value as “active TX for RX” (video route).
  - **Note**: Controller output formatting can vary by firmware; adjust patterns if needed.
- `parseTxMsurl(tx, lines)`
  - Extracts the substring after `MSURL:` and removes trailing `.`.

## Extending the plugin

### Add per-signal routing (video-only, audio-only)

The device supports lock-style commands like:

- `SET DEC <rx> SWITCH <tx> VIDEO`
- `SET DEC <rx> SWITCH <tx> AUDIO`

If you expose these, be careful: the API describes these as “lock in” routes rather than a
simple “temporary switch”, so be sure you also implement the corresponding unlock behavior.

### Add naming

The API supports:

- `SET DEC <rx> NAME <name>`
- `SET ENC <tx> NAME <name>`

You can add text inputs and “Apply” triggers per TX/RX.

### Scale beyond 64

The device document references IDs up to 762, but this plugin intentionally caps at 64 for UI
and performance. If you need more, increase the property maximum and redesign the UI layout.

## Debugging tips

- Use **Diagnostics → Last Response** to see what the controller actually sent.
- Temporarily set property **Debug = Verbose** to print received lines to the Q‑SYS script console.

