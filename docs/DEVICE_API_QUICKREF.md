# DEVICE API QUICK REFERENCE — Turtle AV Chazy Control (TELNET/SSH)

This is a **practical** subset of the published “Chazy Control API” focused on what the Q‑SYS plugin uses (and what you’ll likely need for routing + preview).

Source: `docs/Chazy-Control-API.pdf` (also available as text at `docs/Chazy-Control-API.txt`).

## How to access the API

Per the API reference:

- Connect via **TELNET** (default port `23`) or **SSH**
- Commands are **line-based** and examples show simple ASCII with responses like `[SUCCESS]...`

## Core status commands

### Controller + overall system status

**Command**

- `GET STATUS`

**What you get**

- Controller firmware version, LAN configuration
- Summary of TX (ENC) and RX (DEC) entries known to the controller

## Routing (matrix switching)

### Route an RX to a TX (all signals)

**Command**

- `SET DEC <dec> SWITCH <enc> ALL`

**Meaning**

- Route RX (decoder) `<dec>` to TX (encoder) `<enc>` for:
  - Video, Audio, IR, RS‑232, USB, CEC

**Examples**

- Route RX 1 to TX 3:
  - `SET DEC 1 SWITCH 3 ALL`
- Clear RX 1 route:
  - `SET DEC 1 SWITCH 0 ALL`

### Get RX status

**Command**

- `GET DEC <dec> STATUS`
  - `GET DEC STATUS` is equivalent to `GET DEC 0 STATUS` (all RX)

**What you get**

- Version/network info and (critically) the “Vid /Aud /IR /Ser /USB /CEC” routing numbers.

## Preview stream URLs (“SS module”)

Chazy Control supports “live video previews” in its Web GUI. The API exposes **secondary stream** URLs per TX.

### Get TX mainstream preview URL

**Command**

- `GET ENC <enc> SS MSURL`

**Notes from the API doc**

- Gen 1: mainstream format is **RTSP**
- Gen 2: mainstream format is **MJPG** (MJPEG over HTTP)

**Example responses shown in the API doc**

- `Encoder 002 secondary stream MSURL:rtsp://169.254.110.1/live/main/av_stream.`
- `Encoder 001 secondary stream MSURL:http://169.254.10.2:8080/?action=stream.`

### Get TX substream URL (Gen 1 only)

**Command**

- `GET ENC <enc> SS SSURL`

**Notes**

- Documented as **not supported by Gen 2** devices.

## Practical “can we show previews in Q‑SYS?”

- **Inside the plugin UI**: not directly (Q‑SYS plugin UIs don’t include a native video renderer).
- **What you *can* do**: use `GET ENC <enc> SS MSURL` and present the URL to:
  - Open in a browser (MJPEG)
  - Open in VLC (RTSP)
  - Embed/bridge into a separate UI system if required

