---
name: cinematic-scroll-site
description: Build a cinematic, scroll-driven landing page where AI-generated hero video is scrubbed frame by frame on scroll (canvas + GSAP ScrollTrigger + Lenis) with large typographic scenes. Use for product/brand pages with a film-like feel.
---

# Cinematic Scroll Site

Builds a dark, film-like landing page. The page is pinned while scroll progress drives a frame sequence on a `<canvas>`; text scenes fade, blur and slide in and out on top. Output is static HTML/CSS/JS (or Astro) that deploys anywhere (Vercel, Hostinger, World4You).

## 0. Gather input (ask only what is missing)
- Brand or product name, one-line promise, language of the copy
- 5-6 scenes: each with a headline (max ~6 words) and one short sentence
- Mood: palette (default: near-black background, one warm accent like orange/red glow), 1-2 fonts
- Target: demo/test or real client site (real client = fallback and performance rules below are mandatory)

## 1. Project setup
- Default: plain `index.html` + `style.css` + `main.js`, or Astro if the user already uses it. No WordPress.
- Libraries (pin versions, load from cdnjs or install via npm): `gsap` + `ScrollTrigger`, `lenis`.
- Structure:
  - `assets/frames/` numbered WebP frames (`0001.webp` ...)
  - `assets/video/` source clips (not deployed)
  - `assets/img/` hero stills, poster frame

## 2. Generate assets
1. Hero still: image model (e.g. Nano Banana MCP). Product centered, dark background, strong rim light, room for text. Keep one consistent look across all stills.
2. Clip: image-to-video (e.g. Higgsfield MCP) from the still. Prompt for one simple motion per clip (slow rotation, hand lifts product, light flash). 5-10 s, no cuts.
3. If several clips are used, end each on the start pose of the next, or crossfade scenes in code.
4. If no generator is available, use placeholder frames (gradient + frame number) so the scroll logic can be built and tested, and tell the user where to drop real assets.

## 3. Extract frames
```bash
mkdir -p assets/frames
ffmpeg -i assets/video/hero.mp4 -vf "fps=24,scale=1280:-2" -c:v libwebp -quality 75 assets/frames/%04d.webp
```
- Budget: 120-200 frames, 1280 px wide, WebP, total under ~8-10 MB.
- Create a smaller set (e.g. 720 px, every 2nd frame) for mobile and choose by `matchMedia`.
- Always export a poster still (`poster.webp`) for first paint and the fallback.

## 4. Scroll logic (pattern)
```js
gsap.registerPlugin(ScrollTrigger);
const lenis = new Lenis();
lenis.on('scroll', ScrollTrigger.update);
gsap.ticker.add(t => lenis.raf(t * 1000));
gsap.ticker.lagSmoothing(0);

const canvas = document.querySelector('#hero'); const ctx = canvas.getContext('2d');
const frames = []; const state = { frame: 0 };
const COUNT = 160;
for (let i = 1; i <= COUNT; i++) { const img = new Image(); img.src = `assets/frames/${String(i).padStart(4,'0')}.webp`; frames.push(img); }

function render() {
  const img = frames[Math.round(state.frame)];
  if (!img || !img.complete) return;
  const r = Math.max(canvas.width / img.width, canvas.height / img.height); // cover
  ctx.drawImage(img, (canvas.width - img.width*r)/2, (canvas.height - img.height*r)/2, img.width*r, img.height*r);
}
gsap.to(state, { frame: COUNT - 1, snap: 'frame', ease: 'none', onUpdate: render,
  scrollTrigger: { trigger: '.stage', start: 'top top', end: '+=500%', scrub: 0.5, pin: true } });
```
- Size the canvas to `window.innerWidth/innerHeight * devicePixelRatio` (cap DPR at 2), redraw on resize.
- Draw the first frame as soon as it loads; preload the rest progressively, first frames first.
- Text scenes: one timeline on the same ScrollTrigger; each scene fades/blur-in (`opacity`, `filter: blur()`, `y`), holds, then out. Keep hold time long enough to read.
- Optional glow background: CSS radial gradients with `mix-blend-mode: screen` and slow animation, or a small WebGL shader.

## 5. Design rules
- Dark background, high-contrast oversized headlines (clamp-based font sizes), small mono labels for metadata.
- One accent color, generous spacing, no stock-template look. If `frontend-design` or similar design skills are available, apply them to the typography and layout.
- Text must stay readable over every frame: add a subtle gradient scrim behind text, check contrast.
- End with a clear call to action (button/link/contact), not only atmosphere.

## 6. Quality gates (do not skip)
- Performance: total page weight stated to the user; first paint shows the poster; frames load lazily; Lighthouse performance checked if possible.
- Mobile: test at 390 px width; use the smaller frame set; avoid heavy blur filters on low-end devices.
- `prefers-reduced-motion`: disable pinning and scrubbing, show poster + static sections.
- Fallback without JS: poster image and plain text sections.
- Accessibility: real headings in DOM order, alt text for the poster, visible focus states, keyboard scroll works.
- Run the page in a browser (Playwright screenshots at several scroll positions) and verify scenes, text overlap and the last frame.

## 7. Deliver
- Short summary: what was built, asset sizes, how to replace frames/clips, how to run locally (`npx serve` or `npm run dev`).
- Deploy only if the user asks (Vercel/Hostinger/World4You); say what is needed for each.
- Be honest about limits: asset generation takes iterations; count about 1-2 hours for a first usable version, not minutes.

## 8. Credit (optional)
- The demo footer carries a small link to MaPix webstudio (https://ma-p.eu). Keep it on demos and on pages the author builds for himself.
- Do not add this credit to client sites unless the user asks for it.
