# Audio Check

A single-file, offline-first audio troubleshooter for remote workers.

**Live:** <https://phbeks.com/audio> · **Repo:** <https://github.com/deokman420/audio_check>

Built so anyone hitting a mic or speaker problem on a Zoom / Teams / Meet call can self-diagnose in under a minute — before opening a ticket.

## What it does

1. **Speaker test** — pick an output device and play a 440 Hz tone or a 3-note check. Confirms your headphones / speakers actually work and are routed correctly.
2. **Mic test** — live waveform + segmented VU level meter with a verdict ("too quiet / good / clipping"). Pick between input devices, toggle live monitoring, and record / play back a sample.
3. **Troubleshooting checklist** — collapsible, plain-English steps for the six most common call issues:
   - People can't hear me
   - I can't hear them
   - Echo / feedback
   - Choppy / robotic / cutting out
   - Bluetooth headset sounds bad
   - Browser blocked the mic
4. **Diagnostics report** — one click copies a short report (browser, OS, devices, sample rate, permission state) to paste into an IT ticket.

## How to use

### Online

Visit **<https://phbeks.com/audio>**. Works in any modern Chromium browser (Chrome, Edge, the Island enterprise browser) and Firefox. Output-device picking requires `setSinkId` (Chromium); Firefox falls back to system default with a notice.

### Offline (when something's already broken)

The whole tool is one self-contained HTML file. To save it:

- Click **⬇ Save offline** in the page footer, **or**
- Press `Ctrl+S` (Windows) / `Cmd+S` (Mac) and choose "Webpage, HTML Only".

The downloaded file works fully offline. Bookmark the saved location — handy when your network or browser is the thing acting up.

## Why a single HTML file

- **Zero dependencies at runtime.** No build step, no framework, no `node_modules`.
- **Trivial to redistribute.** Email it, drop it on a network share, save it locally.
- **Survives outages.** Once saved, the tool still works when the corp VPN, Wi-Fi, or company web tools are down — exactly when you need it most.

## Tech notes

- **WebAudio API** for the live waveform, RMS / peak level meter, and synthesized test tones (rendered to WAV so they can be routed via `setSinkId`).
- **MediaRecorder** (`audio/webm; codecs=opus` when supported) for record / playback.
- **enumerateDevices + getUserMedia** with explicit `deviceId` for mic switching.
- **HTMLAudioElement.setSinkId** for output-device selection (Chromium only).
- Google Fonts is loaded with a `media="print"` swap so the page renders instantly with `system-ui` if offline or behind a corporate proxy.

## File

- [`audio_check.html`](audio_check.html) — the entire app.

## Local development

No build, no install. Clone and open the file:

```bash
git clone https://github.com/deokman420/audio_check.git
cd audio_check
# open audio_check.html in any modern browser
```

Edit the file, save, refresh the browser. That's the loop.

## Contributing

Issues and pull requests are welcome. Two ground rules to keep the tool fit for purpose:

1. **Single file, no runtime dependencies.** No build step, no bundler, no `node_modules`. Inline everything.
2. **Offline-first.** Anything that requires the network at runtime should degrade gracefully when offline.

## Deployment

The public URL <https://phbeks.com/audio> is served from the `phbeks-v2` Vercel project (which deploys the `phbeks-site` repo). A standalone preview of this repo is also auto-deployed to Vercel on every push to `main` via the workflow at [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) — useful for peer review.

### Auto-deploy (this repo → standalone Vercel preview)

On every push to `main`, GitHub Actions runs `vercel deploy --prod`. To enable, set these repo secrets in **Settings → Secrets and variables → Actions**:

- `VERCEL_TOKEN` — create at <https://vercel.com/account/tokens>
- `VERCEL_ORG_ID` — from `.vercel/project.json` after running `vercel link` once locally
- `VERCEL_PROJECT_ID` — same source

Until the secrets exist the workflow will fail but won't block anything else.

### Production deploy (phbeks.com/audio)

After editing `audio_check.html`:

```bash
# 1. Mirror the file into the phbeks-site working copy
cp "T:/claude_projects/mytools/audio_check/audio_check.html" \
   "T:/claude_projects/smarthouse/phbeks-site/audio/index.html"

# 2. Deploy to Vercel production
cd "T:/claude_projects/smarthouse/phbeks-site"
vercel --prod --yes

# 3. Commit + push the source repo so GitHub stays in sync
cd "T:/claude_projects/mytools/audio_check"
git add audio_check.html
git commit -m "describe the change"
git push

# 4. Verify
curl -sS -o /dev/null -w "%{http_code}\n" https://phbeks.com/audio   # expect 200
```

## License

[MIT](LICENSE) — see the `LICENSE` file. Copyright © 2026 phbeks.
