### AI Generated 🤮
| Repository | Role | Subsystem & Footprint | Commits |
| :--- | :--- | :--- | :---: |
| **[`astro`](https://github.com/narkFerrret/astro)** | **The Watchdog · Security & Vision** | Python · FastAPI · YOLO + CoreML · Qwen3-VL (Ollama) · Hue v2 SSE · 3×1TB Rotating SSDs | `54` |
| **[`remy`](https://github.com/narkFerrret/remy)** | **The Chef · Kitchen & Retail Desk** | Node.js (stdlib) · Persistent Chromium CDP · Pescatarian Gate · SQLite Approval Ledger | `36` |
| **[`jeeves`](https://github.com/narkFerrret/jeeves)** | **The Butler · 2D Observatory & Comms** | Node.js · 190px Single-Rail Hub · Mail.app JXA · 365d Parcel Tracker · WhatsApp Shot Clock | `32` |
| **[`residence`](https://github.com/narkFerrret/residence)** | **The Building · 3D Spatial Twin** | Three.js / WebGL · 3.8fps NDJSON Frame Mux · HTTP/2 gRPC Starlink · 206 Byte-Range Proxy | `19` |

*(Each public repository above is paired with its private full-source counterpart: `astro-engine`, `remy-engine`, `jeeves-engine`, and `residence-engine`.)*

---

## System Topology

```
                     ┌────────────────────────────────────────────────────────┐
                     │             residence (`home.local:8844`)              │
                     │    3D WebGL Digital Twin · Live Camera Textures        │
                     │    Hue Emissive Shaders · Starlink gRPC · Omnibox      │
                     └───────────────────────────┬────────────────────────────┘
                                                 │
                     ┌───────────────────────────▼────────────────────────────┐
                     │            jeeves · 2D Hub (`10.0.10.10:8800`)         │
                     │     Single-Rail 2D Observatory · Scrypt LAN Gate       │
                     │     Cross-Origin postMessage Bus · Word-Density Cap    │
                     └──────┬────────────────────┼─────────────────────┬──────┘
                            │                    │                     │
        ┌───────────────────▼──────┐   ┌─────────▼──────────┐   ┌──────▼───────────────────┐
        │   astro (`:8787`)        │   │   remy (`:8790`)   │   │   jeeves API (`:8795`)   │
        │ • 4× RTSP H.264 Feeds    │   │ • 6-Platform CDP   │   │ • Mail.app JXA Reader    │
        │ • YOLO + Qwen3-VL BEV    │   │ • Pescatarian Gate │   │ • 365d Parcel State Mach │
        │ • Hue v2 SSE Floorplan   │   │ • Coffee Dispenser │   │ • WhatsApp NBA ShotClock │
        │ • 3×1TB SSD Failover     │   │ • Approval Token   │   │ • RFC 8058 Unsubscriber  │
        │ • Firewalla + YubiKey    │   │   Checkout Handover│   │ • Locked-Chat Guardrail  │
        └──────────────────────────┘   └────────────────────┘   └──────────────────────────┘
```

---

## Recent Field Notes (Build Log)

### Nov 10 · Migrating the 3D Residence Twin from the M1 Edge Node to the M3 Host (`residence`)
We originally prototyped the Three.js isometric residence console on a dedicated M1 Mac (`edge-node:8844`). Moving it onto the primary M3 Pro host (`hub-host`) let us unify the adapter layer (`astro-local-adapter`) so the 3D scene pulls live 3.8 fps NDJSON camera streams, HTTP/2 gRPC Starlink dish metrics, and Hue Bridge state from a single process while advertising `http://home.local/` over mDNS.
- **What bit us:** Safari/WebKit silently aborts `<video>` MP4 playback if an initial `Range: bytes=0-1` probe returns `200 OK` chunked instead of `206 Partial Content`. Forwarding `Range` headers end-to-end with explicit `Content-Range` and `Accept-Ranges: bytes` fixed instant clip scrubbing inside the 3D HUD.

### Nov 06 · Why Outbound AI Replies Need an "NBA Shot Clock" (`jeeves`)
Letting an autonomous butler draft and send messaging replies is terrifying without a human intercept window—and useless if it requires manual approval every single time. We built a configurable **Shot Clock** (`10s` fast break, `14s` reset, `24s` regulation, or `coach_hold`).
- Outbound replies stage in memory and start a live visual countdown on `/whatsapp`.
- Touching a single key inside the staged reply textarea fires `{ action: 'edit', pause: true }` (`⏸ HELD IN TIMEOUT`), freezing the clock so it never expires mid-edit. Clicking **Wave Off** cancels delivery and logs `waved_off` to the audit trail.
- Non-admin auto-replies are hardware/protocol-gated to **Locked Chats only** at the bridge boundary.

### Nov 03 · Cross-Camera BEV Corroboration & Non-Event Storage Pruning (`astro`)
Single-camera motion triggers in a back garden are noisy: wind in foliage, shifting shadows, and rain all look like activity to a bounding-box detector.
- When `CAM3` (Back Yard Door) triggers, the worker now interleaves 3 primary keyframes with 3 timestamp-synchronized frames from `CAM4` (Back Yard Bird's-Eye-View) before handing the batch to local `qwen3-vl:4b-instruct` (with an 8,192-token context budget and real-time Hue sensor/light state folded into the prompt).
- If semantic verification confirms `0` human observations, the worker immediately unlinks the temporary MP4 from internal storage and records `dismissed_non_event`, reclaiming ~1.3 GB/day of wasted SSD writes.

### Nov 02 · Cutting UI Word Density by 49% ("Facts, Not Sentences") (`remy` & `jeeves`)
Early versions of the dashboards read like documentation—subtitles under every heading, explanatory paragraphs in panel footers, and verbose status banners.
- We wrote a headless Chromium word-density auditor (`remy-density.cjs` / `hub-text-density.cjs`) and stripped Remy's interface from **3,987 words down to 2,021 words (-49.3%)** without losing a single data point.
- Status lines now carry figures only (`cameras 4/4 · network OK`, `1 in flight · 4/4 platforms`). Ranking reasons, dietary match tags, and staleness timestamps live in `title=` tooltips. If a block has no live data source, it is removed or badged `SAMPLE`—never faked.

### Nov 01 · Surviving Clamshell DarkWake & 3-SSD Rotation (`astro`)
Recording four H.264 streams at ~93 GiB/day across three external 1 TB USB-C SSDs exposed two gnarly macOS behaviors:
1. Under `launchd`, `TMPDIR` resolves through `/var` (a symlink to `private/var`), which tripped our `O_NOFOLLOW` ancestor directory guard (`OwnedDirectory`) during clip encoding until we canonicalized scratch paths with `os.path.realpath`.
2. When an active SSD fills past `target_free_fraction`, the recorder rotates to the next verified volume UUID (`VolumeGuard`) and **persists the failover back to `video-archive.json`** so a service restart never puts the recorder back onto a full disk.

---

## Design Principles

1. **Reported ≠ Verified.** A reachable HTTP 200 health endpoint does not mean cameras are delivering fresh frames, the Hue Bridge SSE stream is live, or the archive SSD is mounted. Every layer is probed and badged independently.
2. **Hard Financial & Privacy Boundaries.** `remy` can search six platforms in parallel, filter out meat/poultry via a deterministic pescatarian ruleset, and stage a basket to checkout—but it cannot spend a penny without a recorded human approval token (`apr_*`) and a full-page verification screenshot.
3. **Zero-Dependency Longevity.** `jeeves` and `remy` run on the Node.js standard library (`node:http`, `node:sqlite`, `node:crypto`) plus vanilla HTML/CSS/JS. No bundler, no React hydration waterfall, no `node_modules` rot.

---

## Other Local-First & Privacy Projects

| Repository | Focus |
| :--- | :--- |
| **[`noor`](https://github.com/narkFerrret/noor)** | Android-based, agent-focused LightOS environment for the Light Phone III. |
| **[`project-nomad`](https://github.com/narkFerrret/project-nomad)** | Self-contained offline computer packed with local tools, knowledge bases, and on-device AI. |
| **[`dpsm`](https://github.com/narkFerrret/dpsm)** | Practical, plain-language operational security guide for protecting sources and communications. |
