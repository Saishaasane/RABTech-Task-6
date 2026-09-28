# Flowline — Project & Task Manager

A single-page task/project manager built as one self-contained `index.html` file (HTML + CSS + vanilla JS). No build step, no backend — it runs entirely in the browser.

**Live demo:** https://claude.ai/artifact/1XoaemCFuxYPkJBvBzJCRr

## Features

- **Simulated authentication** — sign up / sign in with email + password. Passwords are hashed client-side and stored in `localStorage`. This is a demo auth flow, not production-grade security (no server, no encryption at rest) — swap in a real auth provider (Supabase, Firebase Auth, Auth0) before handling real users.
- **Project catalog** — create and delete projects from the sidebar.
- **Task board (CRUD)** — add, move (To do / In progress / Done), and delete tasks per project.
- **Persistent state** — everything is saved to `localStorage` under one user account, and reloads on return visits.
- **Responsive layout** — sidebar collapses to a top bar on narrow screens; supports light/dark mode automatically.

## Architecture

```
┌─────────────────────────────┐
│         index.html          │
│  ┌────────────────────────┐ │
│  │   Auth screen (#auth)  │ │  simpleHash() + localStorage['flowline_session_v1']
│  └────────────────────────┘ │
│              │ session set  │
│              ▼              │
│  ┌────────────────────────┐ │
│  │   App shell (#app)     │ │
│  │  ┌──────┐ ┌──────────┐ │ │
│  │  │Sidebar│ │  Board   │ │ │
│  │  │Projects│ │ 3 columns│ │ │
│  │  └──────┘ └──────────┘ │ │
│  └────────────────────────┘ │
└─────────────────────────────┘
              │
              ▼
   localStorage['flowline_db_v1']
   { users: { email: { name, pass, projects: {
       id: { name, tasks: { id: { title, status } } } } } } }
```

One JSON object (`flowline_db_v1`) is the entire "database," scoped by user email. All reads/writes go through `loadDB()` / `saveDB()`.

## Local setup

No dependencies or build tools required.

1. Download `index.html`.
2. Open it directly in a browser, **or** serve it locally:
   ```bash
   npx serve .
   # or
   python3 -m http.server 8000
   ```
3. Visit the local URL and create an account.

## Deploying

**Vercel**
```bash
npm i -g vercel
vercel deploy --prod
```
(Point it at the folder containing `index.html`; no framework preset needed — choose "Other".)

**Netlify**
```bash
npm i -g netlify-cli
netlify deploy --prod --dir .
```

**GitHub Pages**
1. Push `index.html` to a repo's `main` branch (or a `docs/` folder).
2. Repo → Settings → Pages → set source to that branch/folder.
3. Your app is live at `https://<username>.github.io/<repo>/`.

## Known limitations (by design, for a demo build)

- Auth is client-side only — anyone with browser dev tools can read the local database. Do not use real passwords.
- Data is per-browser (`localStorage`), not synced across devices — a real deployment would need a backend (e.g. Supabase/Postgres) for shared, durable storage.
- No drag-and-drop between columns yet — status changes via the dropdown on each card.

## Suggested next steps

- Swap `localStorage` for a real backend (Supabase, Firebase, or a small Express + Postgres API) behind the same `loadDB`/`saveDB` interface.
- Add drag-and-drop reordering with the native HTML5 Drag and Drop API.
- Add per-project due dates and a simple activity log.
