# HTML Motion Graphics

Build cinema-grade, deterministic motion graphics as a zero-server, standalone `index.html` file powered by GSAP, directional SVG motion blur, a unified `#world` camera rig, and optional Three.js WebGL backgrounds.

## When to Use

- Creating a promotional video, product launch opener, teaser, or brand bumper in `index.html`.
- Building kinetic typography, SaaS UI product tours, or multi-scene visual explainers.
- Converting static slide-like web animations into true camera-driven motion design.
- Delivering a portable `index.html` that opens directly via `file://` and supports frame-accurate scrubbing and headless MP4 rendering.

## Core Workflow

### Step 1: Lock Creative Brief and Concept Frame

Before writing code, define a concise style brief so the output never looks like a generic slideshow template:

1. **Aspect Ratio and Resolution**: Default to `1920x1080` (16:9) for desktop/YouTube, or `1080x1920` (9:16) for Reels/TikTok/Shorts (wrap content in a `.safe` container scaled to `0.78` at `transform-origin: 50% 45%` for 9:16 social HUD clearance).
2. **Concept Architecture**: Pick a clear structural concept based on the subject instead of defaulting to `hook -> 3 icon tiles -> CTA`:
   - *Continuous World / Camera Journey*: One unified space where the camera dollies, orbits, or dives through scenes.
   - *Living UI Tour*: Rebuilt DOM/SVG product components where a cursor/tap interacts, panels expand, and data animates deterministically.
   - *Problem-to-Clarity*: Visual chaos builds up, then a single product action sweeps the frame clean into the brand mark.
   - *Pure Kinetic Typography*: Words enter from varied axes, shift weight/scale, and assemble into the final mark.
   - *Stage Cartoon / Continuous Action*: Character or hero subject stays active while backgrounds, instruments, and diegetic labels change around it.
3. **Color and Surface System**: Define CSS custom properties (`--base`, `--ink`, `--accent`, `--accent-lit`). Never leave a flat single-color background. Combine at least two layers (e.g., radial horizon glow + linear gradient, soft mesh blobs, or WebGL nebula + drifting dust + SVG film grain overlay).
4. **Audio and Timing Plan**: Lock total duration (`DURATION` in seconds) and map every beat to absolute timestamps in seconds.

### Step 2: Build the Standalone `index.html` Architecture

Deliver a single self-contained `index.html` with inline `<style>` and `<script type="module">` importing GSAP (and Three.js if 3D depth/particles are needed) from `https://esm.sh` or `https://cdn.jsdelivr.net`.

- **Fixed-Resolution Stage**: Lock `#stage` to `1920x1080` (or `1080x1920`) and scale proportionally to fit any viewport via CSS `--fit` so composition coordinates never shift across monitors.
- **Unified `#world` Camera Rig**: Place `<canvas>` (if WebGL), background gradients, and `#dom` scene layers inside `#world`. When the camera zooms or pans, both background and foreground move together. Place lens overlays (light leaks, vignette, film grain) outside `#world`.
- **CSS-First Hidden States**: Set `opacity: 0` on animated elements (`.line`, `.ch`, `.hl-t`, `.obj`) in CSS and use `immediateRender: false` on GSAP `fromTo` tweens to prevent first-frame flashes.

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Motion Graphic</title>
<style>
  :root {
    --base: #070913;
    --ink: #f8fafc;
    --accent: #38bdf8;
    --accent-lit: #bae6fd;
    --fit: 1;
  }
  * { box-sizing: border-box; margin: 0; padding: 0; }
  html, body { width: 100%; height: 100%; overflow: hidden; background: #000; font-family: system-ui, -apple-system, sans-serif; }
  #stage {
    position: fixed; left: 50%; top: 50%; width: 1920px; height: 1080px;
    transform: translate(-50%, -50%) scale(var(--fit));
    overflow: hidden; background: var(--base); color: var(--ink);
  }
  #world { position: absolute; inset: -240px; transform-origin: 50% 50%; will-change: transform; }
  .bg-layer {
    position: absolute; inset: 0;
    background:
      radial-gradient(70% 45% at 50% 105%, color-mix(in srgb, var(--accent) 32%, transparent), transparent 70%),
      linear-gradient(180deg, #04060c 0%, var(--base) 60%, #0d1326 100%);
  }
  .scene { position: absolute; inset: 240px; display: grid; place-items: center; pointer-events: none; }
  .line { font-size: 112px; font-weight: 600; letter-spacing: -0.02em; line-height: 1.1; white-space: nowrap; opacity: 0; text-shadow: 0 6px 32px rgba(0,0,0,0.55); }
  .wd { display: inline-block; white-space: nowrap; }
  .ch { display: inline-block; opacity: 0; will-change: transform, opacity, filter; }
  .vignette { position: absolute; inset: 0; pointer-events: none; background: radial-gradient(circle at 50% 50%, transparent 58%, rgba(0,0,0,0.65) 100%); }
  .grain { position: absolute; inset: -60px; pointer-events: none; filter: url(#grain); opacity: 0.16; mix-blend-mode: overlay; }
  #hud { position: fixed; bottom: 16px; left: 50%; transform: translateX(-50%); display: none; gap: 10px; align-items: center; background: rgba(15,23,42,0.85); padding: 8px 14px; border-radius: 999px; color: #fff; font: 13px/1 monospace; z-index: 99; }
  #hud.show { display: flex; }
</style>
</head>
<body>
<svg width="0" height="0" style="position:absolute">
  <defs>
    <filter id="grain" x="0" y="0" width="100%" height="100%">
      <feTurbulence type="fractalNoise" baseFrequency="0.75" numOctaves="3" stitchTiles="stitch" />
      <feColorMatrix type="saturate" values="0" />
    </filter>
  </defs>
</svg>
<div id="stage">
  <div id="world">
    <div class="bg-layer"></div>
    <div class="scene" id="s1"><h1 class="line" id="t1">Motion without limits</h1></div>
  </div>
  <div class="vignette"></div>
  <div class="grain" id="grain"></div>
</div>
<div id="hud">
  <button id="btnPlay">Pause</button>
  <input id="scrub" type="range" min="0" max="1" step="0.001" value="0" style="width:260px" />
  <span id="clock">0.00s</span>
</div>
<script type="module">
import gsap from "https://esm.sh/gsap@3.12.5";

const W = 1920, H = 1080, DURATION = 6.0;
const stage = document.getElementById("stage");
const world = document.getElementById("world");
const grain = document.getElementById("grain");

function fitStage() {
  const s = Math.min(window.innerWidth / W, window.innerHeight / H);
  stage.style.setProperty("--fit", s);
}
window.addEventListener("resize", fitStage);
fitStage();

function splitChars(el) {
  const text = el.textContent.trim();
  el.innerHTML = text.split(/\s+/).map(word =>
    `<span class="wd">${[...word].map(c => `<span class="ch">${c}</span>`).join("")}</span>`
  ).join(" ");
  return el.querySelectorAll(".ch");
}

function blurTween(tl, target, at, fromV, toV, dur, axis = "x", ease = "power3.out") {
  const id = "mb_" + Math.random().toString(36).slice(2, 8);
  const defs = document.querySelector("svg defs");
  const filter = document.createElementNS("http://www.w3.org/2000/svg", "filter");
  filter.setAttribute("id", id);
  filter.setAttribute("x", "-70%"); filter.setAttribute("y", "-70%");
  filter.setAttribute("width", "240%"); filter.setAttribute("height", "240%");
  const blur = document.createElementNS("http://www.w3.org/2000/svg", "feGaussianBlur");
  blur.setAttribute("stdDeviation", "0 0");
  filter.appendChild(blur);
  defs.appendChild(filter);
  const proxy = { v: fromV };
  tl.set(target, { filter: `url(#${id})` }, at);
  tl.to(proxy, {
    v: toV, duration: dur, ease,
    onUpdate() {
      const a = proxy.v.toFixed(2), b = (proxy.v * 0.08).toFixed(2);
      blur.setAttribute("stdDeviation", axis === "x" ? `${a} ${b}` : `${b} ${a}`);
    },
    onComplete() { target.style.filter = "none"; }
  }, at);
}

const CAM = { s: 1, fx: W / 2, fy: H / 2 };
function applyCam(t) {
  const breathe = CAM.s * (1 + 0.012 * Math.sin(t * 0.9));
  const tx = W / 2 - CAM.fx * breathe;
  const ty = H / 2 - CAM.fy * breathe;
  world.style.transform = `translate(${tx}px, ${ty}px) scale(${breathe})`;
  const k = Math.floor(t * 12);
  grain.style.transform = `translate(${(k * 37 % 60) - 30}px, ${(k * 61 % 60) - 30}px)`;
}

const tl = gsap.timeline({
  paused: true,
  onUpdate() { applyCam(tl.time()); }
});

await document.fonts.ready;
const t1 = document.getElementById("t1");
const chars1 = splitChars(t1);
tl.set(t1, { opacity: 1 }, 0.2);
tl.fromTo(chars1,
  { opacity: 0, yPercent: 65, scaleY: 1.4 },
  { opacity: 1, yPercent: 0, scaleY: 1, duration: 0.85, ease: "power4.out", stagger: 0.022, immediateRender: false },
  0.2
);
blurTween(tl, t1, 0.2, 28, 0, 0.85, "y", "power4.out");
tl.to(CAM, { s: 1.08, duration: 3.0, ease: "sine.inOut" }, 0.2);
tl.to(chars1, { opacity: 0, yPercent: -65, duration: 0.42, ease: "power2.in", stagger: 0.01 }, 4.6);
blurTween(tl, t1, 4.6, 0, 42, 0.48, "y", "power2.in");
tl.to({}, { duration: 0.01 }, DURATION);

const params = new URLSearchParams(location.search);
const isClean = params.get("clean") === "1";
const isDebug = params.get("debug") === "1";
const hud = document.getElementById("hud");
const scrub = document.getElementById("scrub");
const clock = document.getElementById("clock");
const btnPlay = document.getElementById("btnPlay");

if (isDebug) hud.classList.add("show");

function seek(t) {
  const clamped = Math.max(0, Math.min(DURATION, t));
  tl.pause(clamped, false);
  gsap.ticker.tick();
  applyCam(clamped);
  scrub.value = clamped / DURATION;
  clock.textContent = `${clamped.toFixed(2)}s`;
}

tl.eventCallback("onUpdate", () => {
  const t = tl.time();
  applyCam(t);
  scrub.value = t / DURATION;
  clock.textContent = `${t.toFixed(2)}s`;
});
tl.eventCallback("onComplete", () => { if (!isClean) tl.restart(); });

btnPlay.onclick = () => {
  if (tl.paused()) { tl.play(); btnPlay.textContent = "Pause"; }
  else { tl.pause(); btnPlay.textContent = "Play"; }
};
scrub.oninput = () => seek(parseFloat(scrub.value) * DURATION);
window.addEventListener("keydown", e => {
  if (e.code === "Space") { e.preventDefault(); btnPlay.click(); }
  if (e.code === "KeyR") { tl.restart(); }
});

window.OPENER = { tl, DURATION, seek };
applyCam(0);
if (!isClean) tl.play(0);
</script>
</body>
</html>
```

### Step 3: Enforce Anti-PPT Motion Design Rules

Apply these craft rules across every scene so the result reads as motion design rather than an animated slide deck:

1. **One Statement per Beat**: Keep headlines to 2-5 words in sentence case (weight 500-600). Never stack eyebrow kicker + 2-line title + 2-line body + 4 icon badges in a static grid.
2. **Asymmetric Timing**: Enter with decisive deceleration (`0.7-1.1s`, `power4.out` or `back.out(1.5)`) and exit faster (`0.35-0.5s`, `power2.in`). Overlap outgoing and incoming elements by `0.15-0.3s` so the screen never goes dead.
3. **Directional SVG Motion Blur**: Use `feGaussianBlur` with split `stdDeviation="x y"` aligned to movement direction, expanding the filter region (`x="-70%" y="-70%" width="240%" height="240%"`) so blur tails are never clipped. Remove the filter (`filter: none`) on completion.
4. **Motivated Camera Movement**: Combine continuous subtle camera breathing (`+/-1-3%` sine drift) with purposeful shot changes (wide -> medium close-up -> close-up on the active element). Never pan a scene so far that background edges expose empty black bars (provide `240px+` overscan on `#world`).
5. **Contrast First**: Keep title text crisp (`#fff` with soft readability shadow, no heavy multi-layer neon bloom on text). Reserve glow and bloom for hero objects, UI highlights, and 3D accents.

### Step 4: Guarantee 100% Determinism

Every visual state must be a pure function of `t = tl.time()` so scrubbing (`?debug=1`) and headless MP4 rendering produce identical frames:

- **Forbidden in Render Loop**: Never use `Math.random()`, `Date.now()`, `performance.now()`, `setInterval`, or cumulative integration (`x += vx * dt`).
- **Deterministic Replacements**: Drive particles, film grain offsets, and oscillations using seeded phase constants and `Math.sin(t * freq + seed)` or tweened distance proxies (`flow`).
- **Absolute Timeline Placement**: Schedule tweens using explicit numeric timestamps (`tl.to(..., 2.40)`) rather than chained relative offsets (`+=0.3`) so adjusting one beat never breaks downstream audio sync.

### Step 5: Verify Visually and Export (When Requested)

1. **Key-Timestamp Inspection**: Open `index.html?debug=1` in a browser (or capture screenshots via Playwright/Puppeteer using `window.OPENER.seek(t)`) at every scene peak and transition midpoint. Verify zero text clipping, high foreground/background contrast, and smooth overlaps.
2. **MP4 Export Pipeline**: When the user requests a video file, render deterministic frames using `?clean=1` and `window.OPENER.seek(frame / fps)` at 60fps, then encode with FFmpeg:

```bash
ffmpeg -framerate 60 -i frames/f%05d.png -c:v libx264 -pix_fmt yuv420p -crf 14 -tune grain output.mp4
```

## Gotchas

- **FOUC Before Font Load**: Splitting text into `.ch` spans before web fonts finish loading miscalculates glyph bounding boxes. Always `await document.fonts.ready` before running `splitChars()`.
- **GSAP `transformOrigin` on SVG Groups**: Animating `rotation` or `scale` on nested `<g>` elements shifts their bounding box and detaches joints. Drive SVG transforms via a JS proxy object that writes `translate(x,y) rotate(r) scale(sx,sy)` directly in `onUpdate`.
- **Module CORS on `file://`**: External `<script type="module" src="...">` fails when opened directly from disk due to `null` origin CORS rules. Keep CSS and JS inline inside `index.html` while importing libraries from HTTPS CDNs.
- **Hidden Tab `requestAnimationFrame` Throttling**: Headless browsers throttle `rAF` in background tabs. Always call `gsap.ticker.tick()` and `applyCam(t)` synchronously inside `window.OPENER.seek(t)`.
