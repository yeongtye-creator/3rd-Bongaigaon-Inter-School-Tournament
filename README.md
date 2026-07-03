# Chess Tournament Website

A static, no-build tournament website (Home, Standings, Pairings, Players, About) built with plain HTML, CSS and vanilla JavaScript. It reads live data from the **ChessPairings REST API v1** and is designed to be uploaded directly to **GitHub Pages** — no npm, no bundler, no framework.

If no API token is configured yet (or a request fails), the site automatically falls back to realistic **sample data** so every page still renders correctly, with a banner letting visitors know they're viewing demo content.

---

## 1. Project structure

```
/
├── index.html          Home page
├── standings.html       Standings page
├── pairings.html        Pairings page
├── players.html         Players page
├── about.html            About page
├── 404.html               Custom error / not-found page
├── README.md
└── assets/
    ├── css/
    │   └── style.css      All styles (design tokens, components, dark mode)
    ├── js/
    │   ├── config.js       ⚠️ Your API token & tournament ID live here
    │   ├── api.js           All network calls go through this file
    │   ├── demo-data.js     Sample data used as a fallback
    │   ├── main.js           Shared nav / dark mode / shortcuts / helpers
    │   ├── standings.js      Standings page logic
    │   ├── pairings.js       Pairings page logic
    │   └── players.js        Players page logic
    └── img/
        └── favicon.svg
```

---

## 2. How to upload to GitHub

1. Create a new repository on GitHub (e.g. `chess-tournament-site`).
2. Upload every file in this project, **keeping the folder structure intact** (the `assets/` folder must stay next to the HTML files).
   - Easiest way: on the repository page, click **Add file → Upload files**, drag the whole project folder contents in, and commit.
   - Or, from your computer:
     ```bash
     git init
     git add .
     git commit -m "Initial site"
     git branch -M main
     git remote add origin https://github.com/<your-username>/<your-repo>.git
     git push -u origin main
     ```

## 3. How to enable GitHub Pages

1. In your repository, go to **Settings → Pages**.
2. Under **Build and deployment → Source**, choose **Deploy from a branch**.
3. Select the `main` branch and the `/ (root)` folder, then **Save**.
4. GitHub will publish your site at `https://<your-username>.github.io/<your-repo>/` within a minute or two.
5. Come back to `assets/js/config.js` and update `SITE_URL` to match that address (used for canonical/OpenGraph tags).

## 4. How to edit the API token

Open `assets/js/config.js` and change:

```js
API_TOKEN: "YOUR_CHESSPAIRINGS_API_TOKEN",
```

to your real ChessPairings API bearer token. This is the **only** file that should ever contain the token — every request in `api.js` reads it from here.

> **Security note:** this is a static, client-side site, so anything in `config.js` is publicly visible in the page source. Only use a token you're comfortable exposing publicly, and confirm with ChessPairings that browser-side (CORS) requests are allowed for your account. If your token must stay private, put a small server-side proxy between this site and the ChessPairings API instead of calling it directly from the browser.

## 5. How to change the tournament ID

In the same file, update:

```js
TOURNAMENT_ID: "12345",
TOURNAMENT_TYPE: "individuale", // or "squadre" for team tournaments
```

with the numeric ID of your tournament (found in your ChessPairings dashboard/URL).

You can also customize the rest of `config.js`:

| Key | What it controls |
|---|---|
| `SITE_NAME` | Site title shown in the nav, footer, and browser tab |
| `ORGANIZER_NAME/EMAIL/PHONE` | Shown on the Home and About pages |
| `REFRESH_INTERVAL_MS` | How often Standings/Pairings/Players auto-refresh (default 30s) |
| `PAGE_SIZE` | Rows per page on Standings/Players |
| `DEMO_MODE_FALLBACK` | Set to `false` once your live API is confirmed working, to stop falling back to sample data on error |

---

## 6. Features

- Home, Standings, Pairings, Players and About pages, fully responsive
- Sticky navigation with mobile menu
- Dark mode toggle (remembered via `localStorage`)
- Search, sort, filter and pagination on Standings and Players
- Round selector, search, and result cards on Pairings
- Player profile popup modal
- Auto-refresh every 30 seconds on live data pages, with a floating countdown + manual refresh button
- Loading skeletons, empty states, and error states with a retry button
- Offline banner (detects `navigator.onLine`)
- Keyboard shortcuts: `/` focus search, `D` dark mode, `R` refresh, `1`–`5` jump to a page, `?` show shortcut list
- SEO: titles, meta descriptions, OpenGraph tags, canonical links, SVG favicon
- Custom `404.html` error page

## 7. Replacing the API later

All network calls live in `assets/js/api.js`, calling a small set of functions (`getTournament`, `getStandings`, `getPairings`, `getPlayers`, `getRounds`, `getAnnouncement`). If you ever need to point the site at a different backend, you only need to edit this one file — the page scripts (`standings.js`, `pairings.js`, `players.js`) only ever call these functions, never `fetch()` directly.

## 8. Local preview

Because this is a static site, you can preview it locally with any simple server, for example:

```bash
python3 -m http.server 8000
```

then open `http://localhost:8000` in your browser. (Opening `index.html` directly with `file://` also works for most content, but some browsers restrict `fetch()` on `file://` pages.)
