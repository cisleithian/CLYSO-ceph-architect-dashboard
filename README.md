# Ceph Cluster Architect Dashboard - standalone

A single self-contained `index.html` that runs the whole dashboard in the browser.
No build step, no server, no Claude. Everything runs client-side.

by Alex Weijlands - version 0.1 Draft

## What's in here

- **`index.html`** - the only file you need. The React app is pre-transpiled and
  inlined; React and Recharts load from the esm.sh CDN via an import map.

(`app.mjs` is the intermediate module that is already inlined into `index.html`. You can ignore or delete it.)

## Run it locally

Just open `index.html` in a modern browser. If your browser blocks module
imports from `file://`, serve the folder over HTTP instead:

```bash
# from this folder
python3 -m http.server 8000
# then open http://localhost:8000
```

## Publish on GitHub Pages

1. Create a repository (for example `ceph-architect-dashboard`).
2. Add `index.html` to the repository root and push.
3. In the repo, go to **Settings -> Pages**.
4. Under **Build and deployment**, set **Source** to *Deploy from a branch*,
   pick branch `main` and folder `/ (root)`, then Save.
5. After a minute your dashboard is live at
   `https://<your-username>.github.io/<repo-name>/`.

To host it under an existing site, drop `index.html` into a `/docs` folder (or any
path) and point GitHub Pages at that folder, or rename it to fit your page.

## Notes

- **Internet is required at load time**: the page pulls React 18 and Recharts 2
  from `https://esm.sh`. After that it runs entirely in your browser.
- **Nothing leaves your machine**: sizing, cost and config data stay local.
  Save config (JSON), Load config, CSV export and the printable summary all work
  in a real browser (they were limited inside the Claude preview).
- **Modern browser needed** (Chrome, Edge, Firefox, Safari) for import maps and
  ES modules. All current versions support this.

## Updating the dashboard

The source is `CephSizingDashboard.jsx` (one folder up). To rebuild `index.html`
after changing it, re-transpile with esbuild and re-inline:

```bash
esbuild CephSizingDashboard.jsx --bundle --format=esm --jsx=transform \
  --external:react --external:react-dom --external:recharts --outfile=app.mjs
# then wrap app.mjs in the index.html shell (import map + createRoot bootstrap)
```

## Fully offline version (optional)

If you want zero CDN dependency, download the React and Recharts UMD/ESM builds,
put them next to `index.html`, and change the import-map URLs to those local
files. Ask and this can be prepared for you.
