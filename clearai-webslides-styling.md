---
description: Build a ClearAI-branded interactive pitch deck website (React + Vite) matching the exact styling, fonts, colours, and navigation used in the QSHF Scoping Webslides project. Use when a user wants to create a new pitch deck for a client.
---

You are building a ClearAI-branded interactive pitch deck as a React + Vite single-page application. Follow every specification below exactly. Do not deviate from the colour palette, font stack, or component patterns unless the user explicitly instructs a change.

---

## 1. Project Setup

Bootstrap with Vite + React:
```bash
npm create vite@latest <project-name> -- --template react
cd <project-name>
npm install
npm install html2canvas
```

Set `vite.config.js` base to `/` for general deployments (override only if deploying to a GitHub Pages sub-path):
```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
export default defineConfig({ plugins: [react()], base: '/' })
```

Add `netlify.toml` for Netlify deployment:
```toml
[build]
  command = "npx vite build --base /"
  publish = "dist"
```

Add `.node-version` containing `20` to ensure Node 20 is used on CI/CD platforms.

---

## 2. Font

The only font used is **Thorndale AMT Regular** (a system serif font). Never use Google Fonts or any other typeface.

Add to `index.html` inside `<head>`:
```html
<style>
  @font-face {
    font-family: 'Thorndale AMT Regular';
    src: local('Thorndale AMT Regular'), local('ThorndalAMT-Regular'), local('Thorndale AMT');
    font-weight: normal;
    font-style: normal;
  }
  * { font-family: 'Thorndale AMT Regular', 'Thorndale AMT', 'Times New Roman', serif !important; }
</style>
```

In all React components, always reference:
```js
const F = { main: "'Thorndale AMT Regular', 'Thorndale AMT', 'Times New Roman', serif" }
```

---

## 3. Colour Palette

Define these constants at the top of the pitch deck file. Never hardcode hex values elsewhere — always reference `C.<name>`:

```js
const C = {
  parchment: '#F4F0ED',  // warm off-white — app background, light slide backgrounds
  obsidian:  '#22190C',  // near-black brown — dark slides, nav bar, table headers
  clay:      '#834A33',  // warm mid-brown — accents, recommended card bg, section tags
  red:       '#FF4832',  // ClearAI red — CTAs, highlights, dot icon, "Recommended" badge
  lightClay: '#C9A494',  // muted clay — labels, secondary text
  mid:       '#EAE4DE',  // light warm grey — alternate row bg, card backgrounds
  white:     '#FFFFFF',  // pure white — content slides that need white bg
}
```

### Colour usage rules
| Element | Colour |
|---|---|
| App / page background | `C.parchment` |
| Navigation bar | `C.obsidian` |
| Dark (cover / thank you) slides | `C.obsidian` |
| White content slides | `C.white` |
| Parchment content slides | `C.parchment` (via `<Wrap>`) |
| Section headings (large) | `#000` (pure black) |
| Body text | `C.obsidian` |
| Accent / rule lines | `C.clay` at 0.3 opacity |
| Table headers | `C.obsidian` bg, `C.parchment` text |
| Highlighted / recommended card | `C.clay` bg, `C.parchment` text |
| Standard card bg | `C.mid` |
| CTA / badge | `C.red` |
| Brand dot icon | `C.red` |

---

## 4. Shared Component Library

Define these components once near the top of the pitch deck `.jsx` file. All slides use them.

### `Rule` — horizontal divider
```jsx
function Rule({ color = C.clay, op = 0.3, my = 28 }) {
  return <div style={{ height: '1px', background: color, opacity: op, margin: `${my}px 0` }} />
}
```

### `Wrap` — parchment-bg slide content wrapper
Use for slides that sit on the parchment background (not white, not dark).
```jsx
function Wrap({ children, justify = 'center' }) {
  return (
    <div style={{ width: '100%', height: '100%', display: 'flex', flexDirection: 'column', justifyContent: justify, padding: '56px 80px', boxSizing: 'border-box' }}>
      {children}
    </div>
  )
}
```
Always pass `justify="flex-start"` when the slide has a large section heading at the top.

### `SlideFooter` — bottom bar on white slides
```jsx
function SlideFooter({ section }) {
  return (
    <div style={{
      height: '40px', borderTop: '1px solid rgba(0,0,0,0.1)',
      display: 'flex', alignItems: 'center', justifyContent: 'space-between',
      padding: '0 56px', flexShrink: 0, background: C.white,
    }}>
      <div style={{ fontFamily: F.main, fontSize: '13px', color: '#000', display: 'flex', alignItems: 'baseline', gap: '1px' }}>
        Clear<span style={{ fontStyle: 'italic' }}>AI</span>
      </div>
      <div style={{ fontFamily: F.main, fontSize: '9px', letterSpacing: '2.5px', textTransform: 'uppercase', color: 'rgba(0,0,0,0.4)' }}>
        {/* Company name & proposal date */}
      </div>
      <div style={{ fontFamily: F.main, fontSize: '9px', letterSpacing: '2.5px', textTransform: 'uppercase', color: 'rgba(0,0,0,0.4)' }}>
        {section}
      </div>
    </div>
  )
}
```

### `WhiteSlide` — white background slide with footer
```jsx
function WhiteSlide({ children, section }) {
  return (
    <div style={{ width: '100%', height: '100%', background: C.white, display: 'flex', flexDirection: 'column' }}>
      <div style={{ flex: 1, overflow: 'hidden', display: 'flex', flexDirection: 'column' }}>
        {children}
      </div>
      <SlideFooter section={section} />
    </div>
  )
}
```

### `DotIcon` — 3×2 red dot grid (brand icon)
Used on cover and thank-you slides, bottom-right corner.
```jsx
function DotIcon({ size = 13, gap = 5, color = C.red }) {
  const cols = 3, rows = 2
  const w = cols * size + (cols - 1) * gap
  const h = rows * size + (rows - 1) * gap
  return (
    <svg width={w} height={h} viewBox={`0 0 ${w} ${h}`}>
      {Array.from({ length: rows }, (_, r) =>
        Array.from({ length: cols }, (_, c) => (
          <circle key={`${r}-${c}`}
            cx={c * (size + gap) + size / 2}
            cy={r * (size + gap) + size / 2}
            r={size / 2}
            fill={color}
          />
        ))
      )}
    </svg>
  )
}
```

---

## 5. Slide Type Patterns

### Dark slide (Cover / Thank You)
```jsx
<div style={{
  width: '100%', height: '100%', background: C.obsidian,
  display: 'flex', flexDirection: 'column',
  padding: '40px 56px', boxSizing: 'border-box',
}}>
  {/* Top bar */}
  <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', flexShrink: 0 }}>
    <div style={{ fontFamily: F.main, fontSize: '10px', letterSpacing: '3.5px', color: 'rgba(244,240,237,0.7)', textTransform: 'uppercase' }}>
      {/* Company name */}
    </div>
    <div style={{ fontFamily: F.main, fontSize: '15px', color: 'rgba(244,240,237,0.85)', display: 'flex', alignItems: 'baseline', gap: '1px' }}>
      Clear<span style={{ fontStyle: 'italic' }}>AI</span>
    </div>
  </div>

  {/* Main title — vertically centred */}
  <div style={{ flex: 1, display: 'flex', alignItems: 'center' }}>
    <div style={{ fontFamily: F.main, fontSize: 'clamp(56px, 7.2vw, 120px)', lineHeight: 1.08, color: '#F4F0ED', fontWeight: 400 }}>
      {/* Deck title */}
    </div>
  </div>

  {/* Bottom bar */}
  <div style={{ display: 'flex', justifyContent: 'space-between', alignItems: 'center', flexShrink: 0 }}>
    <div style={{ fontFamily: F.main, fontSize: '10px', letterSpacing: '3.5px', color: 'rgba(244,240,237,0.55)', textTransform: 'uppercase' }}>
      {/* Proposal date e.g. "Proposal DD-MM-YYYY" */}
    </div>
    <DotIcon size={13} gap={5} color={C.red} />
  </div>
</div>
```

### White content slide with large section heading
```jsx
<WhiteSlide section="Section Name">
  <div style={{ padding: '44px 56px 20px', display: 'flex', flexDirection: 'column', height: '100%', boxSizing: 'border-box' }}>
    <div style={{ fontFamily: F.main, fontSize: '52px', lineHeight: 1.05, color: '#000', marginBottom: '16px' }}>
      Section Title
    </div>
    <div style={{ height: '1px', background: 'rgba(0,0,0,0.12)', marginBottom: '20px' }} />
    {/* Content */}
  </div>
</WhiteSlide>
```

### Parchment slide with large section heading (most content slides)
```jsx
<Wrap justify="flex-start">
  <div style={{ fontFamily: F.main, fontSize: '52px', lineHeight: 1.05, color: '#000', marginBottom: '16px' }}>
    Section Title
  </div>
  <div style={{ height: '1px', background: 'rgba(0,0,0,0.12)', marginBottom: '20px' }} />
  <div style={{ fontFamily: F.main, fontSize: '24px', color: C.obsidian, marginBottom: '24px' }}>
    Slide Sub-heading
  </div>
  {/* Content */}
</Wrap>
```

---

## 6. Navigation & Animation

### State
```jsx
const TOTAL = /* number of slides */
const [slide, setSlide] = useState(0)
const [animDir, setAnimDir] = useState('right')
const [animKey, setAnimKey] = useState(0)
```

### Keyboard handler — include `slide` in dependency array so handler always sees current value and does NOT fire animation at boundaries
```jsx
useEffect(() => {
  const handleKey = (e) => {
    if (e.key === 'ArrowRight' || e.key === 'ArrowDown') {
      if (slide < TOTAL - 1) { setAnimDir('right'); setAnimKey(k => k + 1); setSlide(s => s + 1) }
    }
    if (e.key === 'ArrowLeft' || e.key === 'ArrowUp') {
      if (slide > 0) { setAnimDir('left'); setAnimKey(k => k + 1); setSlide(s => s - 1) }
    }
  }
  window.addEventListener('keydown', handleKey)
  return () => window.removeEventListener('keydown', handleKey)
}, [slide])
```

### Slide-in CSS animation
```jsx
<style>{`
  @keyframes slideInR { from { opacity: 0; transform: translateX(36px); } to { opacity: 1; transform: translateX(0); } }
  @keyframes slideInL { from { opacity: 0; transform: translateX(-36px); } to { opacity: 1; transform: translateX(0); } }
  .sir { animation: slideInR 0.32s cubic-bezier(0.25,0.46,0.45,0.94) forwards; }
  .sil { animation: slideInL 0.32s cubic-bezier(0.25,0.46,0.45,0.94) forwards; }
`}</style>
<div key={animKey} className={animDir === 'right' ? 'sir' : 'sil'} style={{ flex: 1, overflow: 'hidden' }}>
  <SlideContent />
</div>
```

### Navigation bar (bottom, obsidian background)
```jsx
<div style={{ height: '52px', background: C.obsidian, display: 'flex', alignItems: 'center', padding: '0 28px', gap: '12px', flexShrink: 0 }}>
  {/* Back arrow */}
  <button onClick={prev} disabled={slide === 0}
    style={{ background: 'transparent', border: '1px solid rgba(244,240,237,0.25)', color: slide === 0 ? 'rgba(244,240,237,0.2)' : C.parchment, width: '30px', height: '30px', borderRadius: '4px', cursor: slide === 0 ? 'default' : 'pointer', fontSize: '14px', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
    &#8592;
  </button>

  {/* Dot indicators — active dot widens to 22px pill */}
  <div style={{ display: 'flex', gap: '5px', flex: 1, justifyContent: 'center' }}>
    {SLIDES.map((_, i) => (
      <button key={i} onClick={() => goTo(i)}
        style={{ width: i === slide ? '22px' : '7px', height: '7px', borderRadius: '4px', background: i === slide ? C.clay : 'rgba(244,240,237,0.2)', border: 'none', cursor: 'pointer', padding: 0, transition: 'all 0.2s' }} />
    ))}
  </div>

  {/* Slide counter e.g. 01 / 14 */}
  <div style={{ fontFamily: F.main, fontSize: '10px', color: 'rgba(244,240,237,0.4)', letterSpacing: '1px' }}>
    {String(slide + 1).padStart(2, '0')} / {String(TOTAL).padStart(2, '0')}
  </div>

  {/* Forward arrow */}
  <button onClick={next} disabled={slide === TOTAL - 1}
    style={{ background: 'transparent', border: '1px solid rgba(244,240,237,0.25)', color: slide === TOTAL - 1 ? 'rgba(244,240,237,0.2)' : C.parchment, width: '30px', height: '30px', borderRadius: '4px', cursor: slide === TOTAL - 1 ? 'default' : 'pointer', fontSize: '14px', display: 'flex', alignItems: 'center', justifyContent: 'center' }}>
    &#8594;
  </button>
</div>
```

---

## 7. Dynamic Calculator Pattern

For slides that drive financial calculations from editable inputs, lift state to the top-level component and pass `calc` / `setCalc` props down to each slide that needs them.

### Shared derive function
```js
function derive(c) {
  // All computed values come from raw c inputs
  // e.g. hourly rates, annual costs, scenario savings, payback periods
  return { /* computed fields */ }
}
```

### Editable input style (boxed, clearly editable)
```js
const inputStyle = {
  fontFamily: F.main, fontSize: '13px',
  background: '#fff',
  border: '1.5px solid rgba(34,25,12,0.3)',
  borderRadius: '3px',
  padding: '4px 8px',
  width: '80px',
  textAlign: 'right',
  outline: 'none',
  color: C.obsidian,
}
```

### Read-only source input (italic, muted)
```js
const sourceInputStyle = {
  fontFamily: F.main, fontSize: '11px', color: C.clay,
  background: 'transparent', border: 'none',
  borderBottom: '1px solid rgba(131,74,51,0.2)',
  padding: '2px 4px', width: '100%', outline: 'none',
}
```

---

## 8. Images

Always import images as ES modules (never use public folder paths — they break when Vite `base` is not `/`):
```js
import myImage from './assets/my-image.png'
// then use: <img src={myImage} ... />
```

For images that need to be constrained within a flex container without overflow:
```jsx
<img src={img} style={{ width: 'auto', height: 'auto', maxWidth: '84%', maxHeight: '52vh' }} />
```

---

## 9. Slide Architecture Checklist

Every new deck should include at minimum:

| Slide | Type | Pattern |
|---|---|---|
| Cover | Dark | `C.obsidian` bg, large `clamp()` title, `DotIcon` bottom-right |
| Problem statement | White | `WhiteSlide` + 52px heading + rule + process diagram |
| Evidence / numbers | White | `WhiteSlide` + split layout (text left, image/chart right) |
| Solution statement | White | `WhiteSlide` + 52px heading + rule + centred subtitle |
| Solution demo | White | `WhiteSlide` + 52px heading + rule + centred screenshot |
| Calculator | Parchment | `Wrap` + editable table left + derived chart right |
| ROI | Parchment | `Wrap` + scenario cards (highlighted = base case) |
| Investment | Parchment | `Wrap` + proposal cards (highlighted = recommended) |
| Timeline | Parchment | `Wrap` + phase columns with coloured top bars |
| Thank You | Dark | Mirrors cover layout, `DotIcon` bottom-right |
| Appendix slides | Parchment | `Wrap` + 52px "Appendix" heading + rule + content |

---

## 10. App Shell (App.jsx)

The app shell wraps the pitch deck component with a minimal branded top bar:
```jsx
<div style={{ fontFamily: F.main, background: C.parchment, minHeight: '100vh' }}>
  <div style={{ display: 'flex', gap: '2px', padding: '16px 24px', background: C.obsidian, position: 'sticky', top: 0, zIndex: 100, alignItems: 'center' }}>
    <div style={{ color: C.parchment, fontWeight: 700, fontSize: '14px', marginRight: '24px' }}>
      {/* Company name — e.g. "Client Name - Project Name" */}
    </div>
  </div>
  <PitchDeckComponent />
</div>
```

---

## 11. How to Use This Skill

When the user asks you to build a new pitch deck for a client, do the following:

1. **Ask for**: client name, project/solution name, proposal date, number of slides needed, and any specific content (problem statement, solution details, financials, timeline).
2. **Scaffold** the project using the Vite + React setup above.
3. **Copy** the shared component library (Rule, Wrap, SlideFooter, WhiteSlide, DotIcon) exactly as specified.
4. **Apply** the colour constants and font stack exactly — no deviations.
5. **Build each slide** following the patterns in Section 9, substituting client-specific content.
6. **For financial slides**, implement the `derive()` pattern and pass `calc`/`setCalc` as props.
7. **Deploy** to Netlify using `netlify deploy --create-site <site-name> --dir dist --prod` after building locally with `npx vite build --base /`.

The result should be a polished, fully navigable interactive pitch deck that matches the ClearAI brand standards exactly.
