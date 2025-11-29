# Copilot Instructions for project-unjinx

These instructions help AI coding agents work productively in this repo. Focus on the concrete, existing patterns in this Astro project.

## Big Picture
- **Framework:** Astro 3.x, output is `server` with Netlify Functions adapter (`@astrojs/netlify/functions`). See `astro.config.mjs`.
- **App Type:** Single page (`src/pages/index.astro`) that renders a countdown timer UI with dynamic background rotation, audio playlist, and confetti celebration.
- **Structure:**
  - `src/layouts/Layout.astro`: Global HTML scaffold + global styles.
  - `src/pages/index.astro`: Main countdown page logic and styles; imports `Layout` and `Card`.
  - `src/components/Card.astro`: Video component displayed when countdown completes.
  - `public/assets/nevado/`: Static images (`bg*.jpeg`) and audio (`song*.mp3`) consumed at runtime.
- **Why server output:** Netlify Functions adapter requires `output: "server"` to serve dynamic features.

## Developer Workflows
- **Install & Dev:**
  - `npm i`
  - `npm run dev` (or `npm start`) starts Astro dev server.
- **Build & Preview:**
  - `npm run build`
  - `npm run preview`
- **Deploy:** Netlify. The app is configured for Netlify Functions via `@astrojs/netlify/functions`.

## Conventions & Patterns
- **Layouts:** Pages wrap with `Layout.astro` and pass `title` prop. Keep global CSS in `Layout.astro` using `style is:global`.
- **Page script pattern:** Use `<script>` inside `.astro` with `import` at top. In `index.astro`, JS runs inside `DOMContentLoaded` to safely access DOM.
- **Assets:** Reference static assets under `public/` using absolute paths (e.g., `/assets/nevado/audio/song1.mp3`) or relative where appropriate. Do not import assets via modules.
- **Styling:** Scoped styles are written inside each `.astro` file; use nested selectors. Keep responsive adjustments via `@media` within the same style block.
- **Countdown behavior:**
  - Target date is set in `index.astro` (`const targetTime = new Date('October 12 2024 05:00:00')`). Update this value for new events.
  - Timer updates every second; when it hits zero, triggers confetti (`canvas-confetti`) and reveals `Card` component.
  - Background images rotate on load and every 30s via `updateBackground()`.
  - Audio playlist is created with an `Audio` element, `autoplay`, and `onended` selects the next random track.
- **Dependencies:**
  - `astro`
  - `@astrojs/netlify` adapter (functions)
  - `canvas-confetti` used client-side in `index.astro`.

## Extension Points
- **Add pages:** Create new files under `src/pages/` (e.g., `about.astro`). Wrap content with `Layout` for consistent global styles.
- **Change event date:** Modify `targetTime` in `src/pages/index.astro`. Keep diff non-negative by preserving the `if (diff < 0) diff = 0` safeguard.
- **Add/replace media:** Drop new backgrounds or audio into `public/assets/nevado/` and update arrays `backgrounds` and `songs` in `index.astro`. Keep absolute URLs for assets in `public/`.
- **Confetti tuning:** Adjust `duration`, `defaults`, and `particleCount` in `callFireworks()` within `index.astro`.

## Gotchas
- **Adapter config:** Keep `output: 'server'` with `adapter: netlify()` in `astro.config.mjs` or Netlify deploys may break.
- **Public paths:** Assets must reside under `public/` to be accessible at runtime. Using `src` for static media won’t work without import pipelines.
- **Autoplay policies:** Browsers may block autoplay without user gesture; current setup uses `controls` and `autoplay`. If needed, attach play to a user interaction.
- **Timezones:** `new Date('October 12 2024 05:00:00')` is locale-dependent. Prefer ISO strings (e.g., `2024-10-12T05:00:00Z`) for consistency.

## Examples
- **New page using layout:**
  ```astro
  ---
  import Layout from "../layouts/Layout.astro"
  ---
  <Layout title="About">
    <main>
      <h1>About This Event</h1>
    </main>
  </Layout>
  ```
- **Add another audio track:** Place `song9.mp3` in `public/assets/nevado/audio/` and append `"/assets/nevado/audio/song9.mp3"` to `songs` in `index.astro`.

## Key Files
- `astro.config.mjs` — output and Netlify adapter.
- `package.json` — scripts and dependencies.
- `src/layouts/Layout.astro` — global UI, styles.
- `src/pages/index.astro` — countdown logic, backgrounds, audio, confetti.
- `src/components/Card.astro` — video shown when countdown completes.
- `public/assets/nevado/**` — images/audio referenced by the page.

---

If any part of these instructions is unclear or incomplete (e.g., deployment settings, environment variables, or asset paths), tell me what you need clarified and I’ll refine this file.