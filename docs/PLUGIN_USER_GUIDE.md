# PLUGIN USER GUIDE — Turtle AV Chazy Control / Chazy4K Router

## Overview

The `TurtleAV-Chazy-Control.qplug` plugin provides **matrix routing control** for **Turtle AV Chazy4K** endpoints managed by **Chazy Control**.

It talks directly to the controller using the **published “Chazy Control API”** over a **TELNET-style TCP session** (default **port 23**).

## Requirements

- **Q‑SYS Designer / Q‑SYS Core** capable of running Lua plugins.
- A **Chazy Control** device reachable from the Q‑SYS Core on the network.
- Chazy4K **TX (encoders)** and **RX (decoders)** added/managed by Chazy Control.

## Network notes (important)

The user manual describes two network ports:

- **Control LAN** (typical default `192.168.6.100`)
- **Video LAN** (typical default `169.254.8.100`)

The API can be reached via “Web GUI/Telnet or SSH login/API commands” on either network depending on how you deploy the system and whether you isolate the LANs.

## Setup in Q‑SYS Designer

1. Install the plugin (`plugin/TurtleAV-Chazy-Control.qplug`).
2. Drag the plugin into your schematic.
3. Select the plugin and configure **Properties**:
   - **Host**: IP/hostname of Chazy Control (example: `192.168.6.100`)
   - **Port**: `23` (default TELNET port per API reference)
   - **RX Count**: how many RX rows you want exposed (1–64)
   - **TX Count**: how many TX entries you want exposed (1–64)
   - **Poll Interval (s)**: `0` disables polling; otherwise polls status periodically
   - **Preview URL Mode**: keep “Fetch TX MSURL” enabled to fetch TX preview URLs
   - **Auth Mode / Username / Password**: optional “best-effort” support (many firmwares don’t prompt)

## Using the plugin (routing)

### Connect

- Press **Connect**.
- The plugin will:
  - Open a TCP session to Chazy Control.
  - Fetch global status (`GET STATUS`).
  - Fetch decoder status (`GET DEC STATUS`) to populate **Active TX**.
  - (Optionally) fetch TX preview URLs (`GET ENC <tx> SS MSURL`).

### Route RX → TX

For each RX row:

1. Set **Select TX** to the desired encoder number (e.g. `3`).
2. Press **Take**.

This sends:

- `SET DEC <rx> SWITCH <tx> ALL`

### Clear a route

Press **Clear** on that RX row.

This sends:

- `SET DEC <rx> SWITCH 0 ALL`

### Refresh status

Press **Refresh Status**:

- `GET STATUS`
- `GET DEC STATUS`

## Preview of inputs/outputs

### What the device provides

The API exposes “secondary stream” URLs per TX:

- **Mainstream URL**: `GET ENC <tx> SS MSURL`
  - Gen 2 devices: **MJPEG** (HTTP)
  - Gen 1 devices: **RTSP**

### What the plugin provides

- `TxNN_MSURL`: the mainstream preview URL for each TX (`NN` is 01..TX Count)
- `RxNN_PreviewUrl`: the preview URL for the TX currently routed to RX `NN`

### What the plugin does *not* do

Q‑SYS plugins do not include a built-in video player/renderer, so the plugin **does not display video previews inside the plugin UI**.

Instead, it surfaces the URLs so you can:

- Open MJPEG links in a browser
- Open RTSP links in VLC (or another RTSP player)
- Embed/bridge the preview into another system/UI as needed

## Troubleshooting

- **“Not connected” errors**: make sure the Q‑SYS Core can reach Chazy Control on TCP/23.
- **No preview URL**:
  - Confirm the TX supports SS preview.
  - Press **Refresh Preview URLs**.
  - Some “SS” commands are not supported on Gen 2 for certain operations; `MSURL` *is* documented for Gen 2.
- **Status parsing looks wrong**:
  - Firmware output formats can vary. Use the **Diagnostics → Last Response** to see raw text and adjust parsing if needed.

## Safety / operational advice

- Avoid setting Poll Interval too low on very large systems (e.g., 64×64) to reduce controller load.
- If your network isolates Control LAN vs Video LAN, ensure the Q‑SYS Core is on the correct side for control access.

