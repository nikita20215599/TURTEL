# Turtle AV Chazy4K / Chazy Control — Q‑SYS Plugin

This repo contains a **working Q‑SYS `.qplug` plugin** for **Turtle AV Chazy Control** (the controller for the **Chazy4K** TX/RX family). The plugin uses the **published Chazy Control API** (TELNET/SSH terminal commands) to:

- Route **TX (encoders)** → **RX (decoders)** via the controller (matrix switching).
- Poll basic status.
- Expose **preview stream URLs** (when available) for TX streams so you can view them in an external player/browser.

## What’s in this repo

- **Plugin**: `plugin/TurtleAV-Chazy-Control.qplug`
- **Device API references (downloaded)**:
  - `docs/Chazy-Control-API.pdf` + `docs/Chazy-Control-API.txt`
  - `docs/User-Guide-Chazy-Control.pdf` + `docs/User-Guide-Chazy-Control.txt`
- **Documentation**:
  - `docs/PLUGIN_USER_GUIDE.md`
  - `docs/PLUGIN_PUBLIC_API.md`
  - `docs/DEVICE_API_QUICKREF.md`
  - `docs/API_COVERAGE.md`
  - `docs/DEVELOPER_REFERENCE.md`

## Install the plugin

1. Copy `plugin/TurtleAV-Chazy-Control.qplug` onto your PC.
2. Install it into Q‑SYS Designer:
   - **Option A**: double‑click the `.qplug`
   - **Option B**: copy it into your Q‑SYS Designer plugins folder (User plugins)
3. Restart Q‑SYS Designer (if it doesn’t appear immediately).

## Quick start

1. Drag **“Turtle AV → Chazy Control → Chazy4K Router”** into your design.
2. **Click the plugin block in the schematic** so the **Properties** pane populates, then:
   - Set **Host** to your Chazy Control IP (common defaults: `192.168.6.100` or `169.254.8.100`)
   - Leave **Port** as `23` (TELNET default)
   - Set **RX Count** and **TX Count** to match your system
3. **Run the design** (Emulate / load to Core), then click **Connect** (or wait for auto-connect).
4. For any RX row:
   - Set **Select TX**
   - Press **Take** (or press **Clear** to unroute)

## Preview (inputs/outputs)

Chazy Control supports “live video previews” in its Web GUI, and the API exposes **secondary stream URLs**:

- **TX preview URL**: `GET ENC <tx> SS MSURL`
  - Gen 2: **MJPEG** (typically `http://…:8080/?action=stream`)
  - Gen 1: **RTSP** (`rtsp://…/live/main/av_stream`)

This plugin **cannot decode video inside Q‑SYS** (Q‑SYS plugins don’t provide a built‑in video renderer), but it *does* surface these URLs as text pins so you can open them in a browser/player or embed them in an external UI.

## Documentation

Start here: `docs/PLUGIN_USER_GUIDE.md`.