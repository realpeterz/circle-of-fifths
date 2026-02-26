# Circle of Fifths — Agent & Developer Reference

This file is the single source of truth for agents and developers working on this repository.
`CLAUDE.md` points here.

---

## 1. Project Overview

**Circle of Fifths Interactive Keyboard** is a vanilla web application that visualises the Circle
of Fifths — a fundamental music-theory tool showing the relationships between the 12 tones of
the chromatic scale, their corresponding key signatures, and the associated major and minor keys.
It also provides a fully functional polyphonic synthesiser playable from the mouse or keyboard.

### Feature highlights
- Interactive SVG circle with 12 coloured segments (major outer ring, minor inner ring)
- Click or keyboard-trigger any key to hear it and see chord details
- Real-time chord detection (triad, 7th, sus, etc.) driven by modifier keys
- 15 instrument synthesis presets (Analog Synth, Piano, Organ, Guitar, etc.)
- Caps Lock toggles major ↔ minor mode for the circle keys
- Arrow-key octave shifting; Space / Enter for temporary octave offsets
- Responsive layout for desktop, tablet, and mobile

---

## 2. Tech Stack

| Layer | Technology |
|---|---|
| Markup | HTML5 (semantic, SEO metadata, Open Graph, JSON-LD) |
| Styling | CSS3 (Flexbox, backdrop-filter, transitions, CSS variables) |
| Logic | Vanilla JavaScript ES6+ — no framework, no bundler |
| Graphics | SVG (inline, generated at runtime) |
| Audio | Web Audio API (oscillators, filters, envelopes, reverb, compressor) |
| Build | **None** — static files served directly |
| Tests | **None automated** — manual smoke tests only (see §8) |
| Dependencies | **None** — zero external libraries or package manager |

Browser requirements: any modern browser with Web Audio API support (Chrome, Firefox, Safari,
Edge). Safari needs the `webkitAudioContext` fallback already present in `app.js`.

---

## 3. Repository Layout

```
circle-of-fifths/
├── .gitignore              # Ignores .DS_Store
├── agents.md               # ← YOU ARE HERE — full developer reference
├── CLAUDE.md               # Points to agents.md
├── README.md               # User-facing overview
├── index.html              # Application entry point
└── frontend/
    ├── assets/             # Static assets (images, audio, docs) — currently empty
    │   └── .gitkeep
    └── src/
        ├── app.js          # All application logic (~1 255 lines)
        ├── styles/
        │   └── main.css    # All styles (~400 lines)
        ├── components/     # Reserved for future reusable UI/SVG helpers
        │   └── .gitkeep
        ├── utils/          # Reserved for future shared helpers
        │   └── .gitkeep
        └── tests/          # Reserved for future automated tests
            └── .gitkeep
```

There are **no build artefacts or generated files** tracked in this repo.

---

## 4. Running the Application

No build step is required.

```sh
# Option 1 — Python (recommended, available everywhere)
python3 -m http.server 8000
# then open http://localhost:8000

# Option 2 — Node.js
npx http-server

# Option 3 — PHP
php -S localhost:8000

# Option 4 — direct file open (audio may be blocked by browser security)
open index.html
```

After the page loads, click **START ENGINE** to initialise the Web Audio context (browsers
require a user gesture before audio can play).

---

## 5. Key Source Files

### `index.html`
- HTML skeleton with SEO metadata (Open Graph, Twitter Card, JSON-LD `MusicApplication` schema)
- Loads `frontend/src/styles/main.css` and `frontend/src/app.js`
- Key DOM elements:
  - `<svg id="wheel">` — circle is drawn into this at runtime
  - `.center-hub` — shows selected key name, mood, and relative minor
  - `.chord-display` — shows chord name, notes, and intervals
  - `#instrument-select` — dropdown for synthesiser preset
  - `#start-overlay` — full-screen overlay with START ENGINE button

### `frontend/src/app.js` (~1 255 lines)
The entire application lives here. Main sections:

| Section | Description |
|---|---|
| **Data** | `keys` (12 major/minor pairs), `noteNames`, `pianoKeys`, `instruments` |
| **Audio init** | `initAudio()` — creates AudioContext, compressor, reverb, saturation chain |
| **Voice creation** | `createVoice()` — builds per-note oscillator + filter + envelope + panner |
| **Note control** | `startNote()`, `startPianoNote()`, `stopNote()`, `stopAllNotes()` |
| **Chord logic** | `spellChord()`, `detectChordType()`, `analyzeActiveNotes()` |
| **SVG rendering** | `createWheel()` — draws all 12 segments with labels and colours |
| **UI updates** | `updateHub()`, `showChordInfo()`, `highlightSegment()` |
| **Input handlers** | `keydown`/`keyup` for keyboard; `click` for SVG segments; `change` for instrument |
| **State** | `activeVoices` Map, `activeNotes` Map, `shiftState` object, `state` object |

#### Audio signal chain
```
Voice (oscillators → filter → panner)
       ├─► Dry bus ──────────────────────► masterGain → compressor → destination
       └─► Wet bus → convolver (reverb) ──┘
                          ↑
                  wave shaper (saturation)
```

#### `instruments` presets (15 total)
Each preset defines `oscillators[]`, `env` (ADSR), `filter`, `vibrato`, `reverb`, `level`,
and `randomDetune`. Examples: `Analog Synth`, `Studio Piano`, `Felt Piano`, `Electric Piano`,
`Organ`, `Strings`, `Airy Pad`, `Nylon Guitar`, `Harp`, `Bass`, `Brass`, `Flute`, `Choir`,
`Bell`, `Mallet`.

#### `keys` data structure (per entry)
```js
{
  label: 'C',          // Display label
  note: 'C',           // Note name
  relMinor: 'Am',      // Relative minor label
  chromaticIndex: 0,   // 0–11 position on chromatic scale
  freq: 261.63,        // Major root frequency (Hz)
  minorFreq: 220.00,   // Relative minor root frequency
  keyCode: 'KeyD',     // Physical key (piano layout)
  mood: '…',           // Major mood description
  minorMood: '…',      // Minor mood description
  color: '#…'          // Segment fill colour
}
```

### `frontend/src/styles/main.css`
- Dark theme: background `#050505`, text `#f0f0f0`, accent `#45a29e`
- Glass-morphism panels with `backdrop-filter: blur`
- Responsive breakpoints: desktop (height > 800 px), tablet (700–800 px), mobile (< 767 px)
- Key selectors: `.app-container`, `.center-hub`, `.chord-display`, `.controls-bar`,
  `.segment` (SVG paths), `.key-label`, `.minor-label`

---

## 6. Keyboard Layout

```
Piano mode (always active):
  Top row  (black keys):  W   R T   U I O   [
  Notes:                 Bb  Db Eb  F# Ab Bb  Db

  Home row (white keys):  A S D F G H J K L ; '
  Notes:                  A B C D E F G A B C D

Circle mode (Caps Lock ON = minor, OFF = major):
  Each key maps to a segment of the Circle of Fifths

Modifiers:
  Shift / Ctrl / Alt  →  different chord qualities
  Arrow Up / Down     →  octave shift ± 1
  Space               →  temporary octave −1
  Enter               →  temporary octave +1
  Caps Lock           →  toggle major / minor mode
```

---

## 7. Coding Conventions

- **Indentation**: 4 spaces in HTML, CSS, and JS
- **HTML**: prefer class-based styling; inline styles only for trivial, static values
- **CSS**: kebab-case class names (`.app-container`, `.center-hub`, `.chord-display`)
- **JS**: camelCase for variables and functions; keep globals minimal
  - `startApp()` **must** remain global — the Start button calls it via `onclick`
- **Commit messages**: short, imperative; use conventional prefixes where appropriate
  (`feat:`, `fix:`, `refactor:`, `docs:`, `style:`)
  - Examples: `feat: add chord detection`, `fix(piano): adjust key layout`
- **PRs**: include a concise summary, manual test notes, and screenshots for UI changes;
  link related issues

---

## 8. Testing

There are no automated tests. Perform a manual smoke test after every change:

1. Serve the project locally and open `http://localhost:8000`
2. Click **START ENGINE** — overlay should disappear
3. Confirm the SVG wheel renders with 12 coloured segments
4. Click several circle segments — verify audio plays and the hub updates
5. Press keyboard keys — verify notes sound and the chord display updates
6. Switch the instrument dropdown — verify the timbre changes
7. Toggle Caps Lock — verify the hub switches between major and minor labels
8. Shift Arrow Up / Down — verify octave indicator changes
9. Open the browser console — there should be **zero errors**
10. Resize the browser window to a narrow viewport — confirm responsive layout holds

Future automated tests should live in `frontend/tests/`.

---

## 9. Dependencies & Browser Compatibility

| Dependency | Version | Notes |
|---|---|---|
| External libraries | none | — |
| Package manager | none | — |
| Build tool | none | — |
| Web Audio API | browser built-in | `webkitAudioContext` fallback for Safari |
| SVG | browser built-in | Generated via JS DOM APIs |

Minimum browser versions that support all features used:
Chrome 66+, Firefox 76+, Safari 14.1+, Edge 79+.

---

## 10. Git Workflow

- Default branch: `master` / `main`
- Feature branches follow the pattern `claude/<short-description>-<id>`
- Always push with `git push -u origin <branch-name>`
- Open a PR with a summary, test notes, and screenshots (for UI changes)

---

## 11. Future Work / Reserved Directories

| Directory | Intended Use |
|---|---|
| `frontend/src/components/` | Reusable UI or SVG helper modules |
| `frontend/src/utils/` | Shared helpers (math, audio utils, DOM helpers) |
| `frontend/assets/` | Images, audio samples, static documents |
| `frontend/tests/` | Automated tests (unit and/or integration) |
