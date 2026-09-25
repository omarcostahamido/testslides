# Hangzhou 2026 — connection test

Slidev test deck to check the venue can reach a GitHub Pages URL (text, CJK fonts, image, Web Audio, video). No external requests: fonts are system fonts, no CDN.

## Deploy
1. Create a public GitHub repo (e.g. `hangzhou-test`) and push this folder to `main`.
2. Repo → Settings → Pages → Source: **GitHub Actions**.
3. The workflow builds and deploys to `https://<user>.github.io/<repo>/`.

Local preview: `npm install && npm run dev`
