# AGENTS.md — timetill.xyz

This file contains project-specific context for AI coding agents. Read this first before making any changes.

---

## Project Overview

**timetill.xyz** (package name: `countdown`) is a minimal, client-side single-page application for calculating time differences between now and a future point in time. It offers two modes:

- **Timer**: count down a user-specified duration (hours, minutes, seconds).
- **Countdown**: count down until a specific date and time.

The app features an optional canvas-based visualization where squares disappear at a rate matching the chosen time unit, giving a visual representation of elapsed time. User preferences (target date and display unit) are persisted via cookies so the countdown survives page reloads.

The project is intentionally small and self-contained — there are no ads, no tracking, and it is designed to work on mobile.

---

## Technology Stack

| Layer | Technology |
|-------|------------|
| Framework | Vue 3 (`vue@^3.2.41`) |
| Build Tool | Vue CLI 4.5 (`@vue/cli-service`) |
| Transpiler | Babel (`@vue/cli-plugin-babel`) |
| Linter | ESLint 6 + `eslint-plugin-vue` 7 |
| Cookies | `vue-cookie-next` (`^1.3.0`) |
| Styling | Plain CSS (scoped in SFC), heavy use of inline styles in templates |
| Visualization | HTML5 Canvas 2D API |

**Important**: The project does **not** use TypeScript, Vue Router, Pinia/Vuex, or any UI component library.

---

## Project Structure

```
.
├── public/
│   ├── index.html          # HTML template (title: "TimeTill")
│   └── favicon.ico
├── src/
│   ├── main.js             # Entry point: creates Vue app, mounts cookie plugin
│   ├── App.vue             # Root component — only renders <Countdown/>
│   ├── components/
│   │   └── countdown.vue   # Main (and only) feature component — all UI, logic, canvas
│   └── assets/
│       └── logo.png        # Currently unused
├── dist/                   # Production build output (generated)
├── .github/workflows/
│   └── deploy.yml          # GitHub Pages CI/CD
├── package.json
├── vue.config.js           # publicPath config for GitHub Pages
├── babel.config.js         # Standard Vue CLI Babel preset
├── static.json             # Static hosting config (SPA fallback)
└── README.md
```

The entire application logic lives in a single component: `src/components/countdown.vue` (~612 lines). Keep this in mind when planning refactors — the component handles:

- Template UI for both timer and countdown modes
- Cookie read/write via `vue-cookie-next`
- Canvas grid calculation and drawing
- `setInterval` timers for both the digit display and the canvas animation
- All user input validation and error handling

---

## Build and Development Commands

Prerequisites (per `README.md`):
- Use Node 16 locally: `nvm install 16 && nvm use 16`
- Install dependencies: `npm install`

Available scripts (from `package.json`):

```bash
# Development server with hot-reload
npm run serve

# Production build (outputs to dist/)
npm run build

# Lint with ESLint
npm run lint
```

**Note**: The `README.md` mentions `npm run build --production`, but the standard Vue CLI `build` command already sets `NODE_ENV=production`. The `--production` flag is not required and may be ignored by Vue CLI.

### Build Configuration

- `vue.config.js` sets `publicPath` to `/timetill.xyz/` in production and `/` in development. This is required because the app is hosted in a subdirectory on GitHub Pages.
- `babel.config.js` uses the standard `@vue/cli-plugin-babel/preset`.

---

## Code Style Guidelines

- **Single File Components (SFC)**: All Vue components are `.vue` files with `<template>`, `<script>`, and `<style scoped>` blocks.
- **No TypeScript**: Use plain JavaScript. The project uses `babel-eslint` as the parser.
- **Styling**: The project mixes scoped CSS in `<style scoped>` with extensive inline `style` attributes in the template. When adding new UI elements, follow the existing pattern — simple inline styles for layout tweaks, scoped CSS for reusable classes.
- **ESLint**: Extends `plugin:vue/vue3-essential` and `eslint:recommended`. No custom rules are defined.
- **Naming**: The single component uses `name: "HelloWorld"` (a legacy name), while the file is `countdown.vue`. The root component is `App.vue`.

---

## Testing

**There is no test suite in this project.** There are no unit tests, integration tests, or E2E tests. If you add tests, you will need to set up the testing framework yourself (e.g., Jest, Vitest, or Cypress).

Before submitting changes, manually verify:
1. `npm run lint` passes without errors.
2. `npm run build` completes successfully.
3. Both timer and countdown modes work in a browser.
4. Cookie persistence works across page reloads.
5. The canvas visualization renders correctly for each time unit.

---

## Deployment

The app is automatically deployed to **GitHub Pages** via GitHub Actions.

### CI/CD Pipeline (`.github/workflows/deploy.yml`)

- **Trigger**: Push to the `main` branch.
- **Steps**:
  1. Checkout code (`actions/checkout@v4`)
  2. Setup Node 18 with npm caching (`actions/setup-node@v4`)
  3. Install dependencies: `npm ci`
  4. Build: `npm run build`
  5. Configure Pages (`actions/configure-pages@v5`)
  6. Upload `dist/` artifact (`actions/upload-pages-artifact@v3`)
  7. Deploy to GitHub Pages (`actions/deploy-pages@v4`)

- **Environment**: `github-pages`
- **Permissions**: `contents: read`, `pages: write`, `id-token: write`

### Static Hosting Config

`static.json` exists at the project root and configures SPA routing for static hosts:

```json
{
  "root": "dist",
  "clean_urls": true,
  "routes": {
    "/**": "index.html"
  }
}
```

This ensures deep links fall back to `index.html` for client-side routing (even though the app currently has no router).

---

## Security Considerations

- **No server-side code**: This is a purely static client-side application. There is no backend API, database, or authentication.
- **Cookies**: The app uses `vue-cookie-next` to store two cookies (`countdown-date` and `countdown-unit`) with a 1-year expiration. These are used solely to restore the user's countdown/timer on return visits. No personal data is stored.
- **No ads or tracking**: The project intentionally avoids third-party scripts, analytics, or ad networks.
- **User Input**: Date and time inputs are passed to the native `Date` constructor. Be cautious when modifying input handling to avoid unexpected `NaN` or invalid date behaviors.

---

## Key Implementation Details

### Cookie Lifecycle
- On `mounted`, the component checks for existing cookies. If found, it restores the countdown state and starts the display/canvas timers immediately.
- On `cancel()`, the cookies are removed and all timers are cleared.
- Cookie expiration is set to 1 year via `VueCookieNext.config({ expire: '1y' })` in `main.js`.

### Canvas Visualization
- A 2000×1000 pixel canvas is rendered at `80vmin` × `40vmin` via CSS.
- The grid of squares is calculated dynamically based on the total number of units (seconds, minutes, hours, or days). Each square disappears at the rate of one unit.
- If the target time is too large to fit on the canvas, or if the time has already passed, `notVisualizable` is set to `true` and the canvas is hidden.

### Timer vs. Countdown Logic
- **Timer**: Adds the specified hours, minutes, and seconds to the current local time. Converts to an ISO-like string adjusted for the local timezone offset.
- **Countdown**: Combines a date string and time string into a single `Date` object. If no date is provided, it defaults to today.

---

## Agent Notes

- **Minimize footprint**: This is a tiny project. Avoid adding heavy dependencies, build tools, or frameworks. Prefer native browser APIs.
- **Preserve Vue 3 patterns**: The app uses `createApp()` and the Options API (not Composition API). Keep new components consistent with this style.
- **Mobile-first**: Any UI changes should remain usable on small screens.
- **Respect the philosophy**: The author created this as a clean, ad-free, distraction-free tool. Avoid adding analytics, trackers, or bloat.
