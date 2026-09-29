# testslides

A Slidev test deck for checking a presentation setup before a talk. Open the URL on the venue computer and go through the slides to see which of these work: fonts, Chinese (CJK) text, images, audio, video, and access to common web-font servers.

**Live:** https://omarcostahamido.github.io/testslides/

## What it tests

1. **Text and CJK characters**: English and Chinese drawn with system fonts (no downloads).
1b. **Web fonts**: the Lobster font loaded from this site (bundled in `public/fonts/`), plus attempts to reach external font servers: Google Fonts (`fonts.googleapis.com`), Google Fonts China (`fonts.googleapis.cn`), the Loli mirror (`fonts.loli.net`) and jsDelivr (`cdn.jsdelivr.net`), including a Chinese web font (ZCOOL KuaiLe). Each row shows ✓/✗ and the load time.
2. **Image**: an SVG served from this site.
3. **Audio**: a three-note Web Audio chime, played on click.
4. **Video**: an MP4 with a 440 Hz tone.

Everything except slide 1b is served from this site. Slide 1b is the only one that makes external requests, and it does so on purpose, to find out which font servers the network can reach.

## Deploy your own copy

1. Fork or copy this repo (it must be public for free GitHub Pages).
2. Repo → Settings → Pages → Source: **GitHub Actions**.
3. Push to `main`. The workflow (`.github/workflows/deploy.yml`) builds and deploys to `https://<user>.github.io/<repo>/`. The base path is taken from the repo name automatically.

## Local preview

```bash
npm install
npm run dev
```