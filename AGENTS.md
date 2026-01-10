# Repository Guidelines

## Project Structure and Module Organization
- `index.html` is the entry page and references the frontend source assets.
- `frontend/src/app.js` contains the Web Audio engine, SVG rendering, and input handling logic.
- `frontend/src/styles/main.css` holds layout, typography, and responsive rules.
- `frontend/src/components/` is for reusable UI or SVG helpers shared across features.
- `frontend/src/utils/` is for shared helpers (math, audio utils, DOM helpers).
- `frontend/assets/` is for static assets (images, audio, docs).
- `frontend/tests/` is reserved for future automated tests.
- `README.md` provides the user-facing overview; update it when structure changes.

There are no build artifacts or generated files tracked in this repo.

## Build, Test, and Development Commands
No build step is required. Use one of the following:

```sh
# Open directly (simple, but some browsers restrict audio)
open index.html

# Preferred: run a local static server
python3 -m http.server 8000
```

Then open `http://localhost:8000` in a browser and click the Start button to unlock audio.

## Coding Style and Naming Conventions
- Indentation: 4 spaces in HTML, CSS, and JS.
- HTML: prefer class-based styling; keep inline styles only for small, static tweaks.
- CSS: use kebab-case class names (e.g., `.app-container`, `.center-hub`).
- JS: use camelCase for variables and functions; keep globals minimal. The `startApp` function must stay global because the Start button calls it.

## Testing Guidelines
There are no automated tests currently. Do a quick manual smoke test:
- Load the page and confirm the SVG wheel renders.
- Click segments and use keyboard input to trigger audio.
- Check the browser console for errors.

If you add tests in the future, place them under `frontend/tests/` and document how to run them here.

## Commit and Pull Request Guidelines
- Commit messages are short and imperative, sometimes using conventional prefixes like `feat:` or `refactor:`. Follow that pattern.
- PRs should include a concise summary, manual test notes, and screenshots for any UI changes. Link related issues when applicable.
