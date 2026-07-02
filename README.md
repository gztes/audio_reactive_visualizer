# 🎵 Audio Reactive Visualizer

A real-time, microphone-driven particle visualizer built with **Three.js** and the **Web Audio API**. A disc of ~1,500 glowing particles ripples, pulses, and shifts colour in response to live sound — with bloom post-processing, an animated gradient backdrop, and a live control panel.

No build step, no dependencies to install. It's a single `index.html` that pulls Three.js and lil-gui from a CDN.

![status](https://img.shields.io/badge/status-working-brightgreen) ![deps](https://img.shields.io/badge/build-none-blue)

---

## ✨ Features

- **Live microphone input** via the Web Audio API's `AnalyserNode`
- **Demo mode** — synthesised audio so you can see it react without granting mic access
- **Full-disc particle field** — concentric rings mapped across the frequency spectrum (bass at the centre, treble at the edge)
- **Logarithmic frequency mapping** so bass, mids, and treble each get fair visual space (see [How it works](#-how-it-works))
- **Auto-calibrating noise gate** — samples your room for ~1.2s on start, then only reacts to sound above the ambient floor
- **Bloom post-processing** for genuine glow on loud particles
- **Animated CSS gradient background**
- **Live control panel** (lil-gui) for bloom, motion, colours — plus 4 one-click presets

---

## 🚀 Usage

Because microphone access requires a secure context, open `index.html` in one of these ways:

**Option A — just open the file**
Double-click `index.html`, or drag it into a Chrome/Firefox tab. `file://` works for mic access in most desktop browsers.

**Option B — local server (recommended)**
```bash
# Python
python3 -m http.server 8000
# then visit http://localhost:8000

# or Node
npx serve
```

Then click **🎙 Enable microphone**, allow access, stay quiet for the ~1 second calibration, and play music or talk. Or click **▶ Demo mode** to skip the mic entirely.

> ⚠️ **It won't work inside an embedded iframe/preview** (e.g. some chat or docs previews) — browsers block mic access there. Open it in a real browser tab.

---

## 🎛 Controls

| Folder | Control | What it does |
|---|---|---|
| **Bloom / Glow** | Strength | Overall glow intensity |
| | Radius | How far the glow spreads |
| | Threshold | Brightness needed before a pixel glows (lower = more glow) |
| | Glow boost | How bright loud particles get |
| **Motion** | Reactivity | How far loud particles push outward |
| | Smoothing | Low = fluid/laggy, high = snappy |
| | Dome depth | 3D lift of loud particles toward the camera |
| | Spin speed | Disc rotation |
| | Tilt | Subtle 3D tilt |
| **Colours** | Quiet → Loud | Four-stop gradient the amplitude maps across |
| **Presets** | Cyberpunk / Inferno / Aurora / Mono ice | One-click looks |

Try **Inferno** with Reactivity ~2.5 and Smoothing ~0.12.

---

## 🔬 How it works

**Frequency analysis.** The `AnalyserNode` runs an FFT on the mic input (`fftSize = 2048` → 1024 frequency bins, each a 0–255 amplitude). We read this every frame with `getByteFrequencyData()`.

**Logarithmic mapping.** Raw FFT bins are *linear* in Hz, but musical energy is concentrated in the low end — a naive `particle[i] → bin[i]` mapping leaves ~90% of the disc sampling near-silent high frequencies. Each ring instead reads an **exponentially-spaced** bin (`Math.pow(1024, ringT)`), so bass/mid/treble each occupy a fair share of the disc.

**Noise gate.** Mics never read true silence (fan hum, room tone, self-noise). On start we spend ~1.2s measuring the **peak** ambient level per bin, then subtract that floor from live readings and rescale the remainder — so only real sound above the room baseline moves the particles.

**Perceptual curve.** Amplitudes are passed through a `sqrt` curve so moderate volumes are still visible, roughly matching how loudness is perceived.

**Rendering.** Particles are drawn as `THREE.Points` with a custom shader (soft round dots via `smoothstep` on `gl_PointCoord`) using **additive blending**, so overlapping particles build brightness. An `UnrealBloomPass` then extracts and blurs the bright areas for the glow. The WebGL canvas is transparent so the CSS gradient shows through behind it.

---

## 🎨 Customising

All tunables live in the `params` object near the top of the `<script>`. Change the defaults there, or add your own preset to the `presets` object at the bottom:

```js
'My vibe': () => applyPreset({
  colorQuiet:'#111', colorLow:'#0af', colorMid:'#0fa', colorLoud:'#fff',
  bloomStrength: 1.2
}),
```

Particle density: change `RING_COUNT` (default 26) and the `8 + ringT * 90` count-per-ring formula.

---

## 🛠 Built with

- [Three.js](https://threejs.org/) r163
- [lil-gui](https://lil-gui.georgealways.com/)
- Web Audio API

## 📄 License

MIT — see [LICENSE](LICENSE).
