# Game Clock

A self-contained countdown timer for running games, matches, and rounds.
One HTML file, no backend, no build step. Open it in a browser, or host it on
a home server and reach it privately over [Tailscale](https://tailscale.com).

> **⚠️ Unmaintained.** This repository is shared as-is so others can freely use
> and build on the source. It is **not actively maintained** — there is no
> support, and issues/pull requests may not be reviewed. Fork it and make it
> your own.

```
gameclock/
├── index.html            ← the whole app
├── serve.json            ← Tailscale Serve config (only needed for hosting)
├── docker-compose.yaml   ← optional: run it as its own Tailscale node
├── .env.example          ← template for your Tailscale auth key
└── sounds/               ← your audio cues (bring your own clips)
    └── README.txt
```

## Run it locally

Just open `index.html` in any modern browser — that's it. Sound cues need audio
files in the `sounds/` folder beside it (see [Sound files](#sound-files)), or you
can load clips on the fly with each row's **Choose file** button.

## Features

- **Set the clock** — hours / minutes / seconds, plus presets (5 / 10 / 20 / 45 min).
- **Start · Pause/Resume · Reset** — `Space` toggles start/pause, `R` resets.
- **Test sound** — unlocks/activates audio and plays a cue *without* starting the
  countdown. Use it to confirm sound works before a game.
- **Adjust time** — add or remove time while paused *or* running. Pushing time back
  above 30s / 10s re-arms those cues.
- **Sound cues** — start countdown · 5 min · 1 min · 30s · 10s · game over. A green
  dot confirms a clip loaded; **Test** previews it.
- **Sound & start options** (remembered between reloads):
  - **Sound cues on/off** — master mute for all cues (Test still works).
  - **Wait for start sound** — the clock begins only after the start clip finishes
    (e.g. counts down on "GO") instead of starting immediately. *On by default.*
  - **Background alarm** — keeps the countdown and its sounds firing when the phone
    screen is locked or you switch apps, so the final horn still goes off even if you
    put the phone down. Works by playing a silent looping clip to keep the page alive
    and driving the clock off a wall-clock heartbeat instead of the (throttled-when-
    hidden) animation loop. *On by default* — turn it off if you'd rather the app never
    interrupt other music playing on the device.
- **Visual urgency** — clock shifts primary → amber (≤30s) → red (≤10s).
- **Appearance** (its own panel, remembered between reloads):
  - **Light / dark mode** toggle.
  - **Custom button colours** picked from a colour-wheel, plus **Reset colours**.
  - **Warning colors** on/off — off keeps the clock one colour the whole time.
  - **Strobe final 10s** — the clock flashes during the last 10 seconds
    (*on by default*; turn it off to keep a gentler pulse instead).
- **Wake Lock** — screen stays awake while the clock is running.

> Browsers block audio until you interact with the page, so the first **Start**
> (or **Test sound**) tap unlocks sound. After that, all cues fire on their own.

## Sound files

Put clips in `sounds/` using the default names (`start.mp3`, `5min.mp3`,
`1min.mp3`, `30s.mp3`, `10s.mp3`, `gameover.mp3`, `test.mp3`). Any format your
browser supports works. To use different names, edit the `DEFAULT_SRC` block near
the top of the `<script>` in `index.html`, or load clips at runtime with each
row's **Choose file** button. See `sounds/README.txt` for details.

*(No audio ships with this repo — bring your own clips.)*

## Hosting it privately over Tailscale (optional)

This is the interesting part: run the clock as its **own dedicated Tailscale node**
so you can share *only* the clock with guests, without exposing anything else on
your tailnet — and with no router port-forwarding.

Two containers share one network namespace:

- **`ts-gameclock`** (`tailscale/tailscale`) — joins your tailnet as a machine named
  `gameclock` and runs [Tailscale Serve](https://tailscale.com/kb/1242/tailscale-serve)
  to terminate HTTPS and proxy to nginx.
- **`gameclock`** (`nginx:alpine`) — serves `index.html`, sharing the Tailscale
  container's network via `network_mode: service:ts-gameclock`.

### Setup

1. **Get a Tailscale auth key.** In the [admin console](https://login.tailscale.com/admin/settings/keys),
   create a reusable, tagged key (tag `tag:gameclock`, matching `--advertise-tags`
   in the compose file). Add an ACL grant so the tag is reachable by whoever you
   share it with.
2. **Configure secrets.** Copy `.env.example` to `.env` and paste your key:
   ```
   cp .env.example .env
   # edit .env -> TS_AUTHKEY=tskey-auth-...
   ```
   `.env` is gitignored — keep it that way.
3. **Set your paths.** In `docker-compose.yaml`, replace the `/path/to/gameclock/...`
   volume paths with real directories on your host. Put `index.html` and `sounds/`
   in the `web` directory, and `serve.json` in the `config` directory.
4. **Launch:**
   ```
   docker compose up -d
   ```
5. **Open it** from any device on your tailnet at
   `https://gameclock.<your-tailnet>.ts.net`.

> **TrueNAS note:** on TrueNAS SCALE, paste `docker-compose.yaml` into a
> **Custom App** instead of running `docker compose` directly.

### Two gotchas worth knowing

- **Use the MagicDNS hostname, not the raw IP.** Tailscale Serve's TLS cert is
  issued for the node's MagicDNS name only; hitting the raw `100.x.y.z` address
  throws a certificate error.
- **Restart the whole stack together.** `ts-gameclock` owns the shared network
  namespace. Restarting *only* it orphans nginx (which stays attached to the dead
  namespace) and Serve returns HTTP 502. Restart both containers, or the whole
  compose stack, together.

## AI-generated code

All of the code in this project was generated with AI assistance
(Anthropic's Claude). Review it yourself before relying on it — it is provided
"as is," with no warranty (see the LICENSE).

## License

[MIT](LICENSE) — free to use, copy, modify, and distribute (including
commercially), with attribution. Copyright (c) 2026 @domesticanadian on GitHub.
