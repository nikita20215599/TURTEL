# API COVERAGE — What this plugin implements

Chazy Control’s published API is broad (system, TX/RX, Dante, video wall, device discovery, etc.). This plugin intentionally starts with the most common needs for Q‑SYS integration: **routing + status + preview URLs**.

Source reference: `docs/Chazy-Control-API.pdf`.

## Implemented

- **System status**
  - `GET STATUS`
- **RX (decoder) routing**
  - `SET DEC <rx> SWITCH <tx> ALL`
  - `SET DEC <rx> SWITCH 0 ALL`
- **RX status polling (to infer active routes)**
  - `GET DEC STATUS` / `GET DEC <rx> STATUS`
- **TX secondary stream preview URL (mainstream)**
  - `GET ENC <tx> SS MSURL`

## Not implemented (yet)

- **Granular route locks**
  - `SET DEC <rx> SWITCH <tx> VIDEO|AUDIO|IR|RS232|USB|CEC`
- **TX / RX naming, EDID, resolution, mute, multicast, etc.**
- **Dante control module**
- **Video wall module**
- **System management (search/add/remove devices)**
- **GPIO / serial configuration**

If you want to expand coverage, `docs/DEVELOPER_REFERENCE.md` outlines where to add new controls and which parsing patterns may need hardening.

