# Publishing to GitHub

Two ways — pick whichever you prefer.

## Option A — GitHub website (no command line)

1. Go to https://github.com/new
2. Name it e.g. `audio-reactive-visualizer`, set Public, **don't** add a README/license (you already have them), click **Create repository**.
3. On the next page click **uploading an existing file**.
4. Drag in all four files: `index.html`, `README.md`, `LICENSE`, `.gitignore`.
5. Click **Commit changes**. Done.

## Option B — command line

From inside the folder containing these files:

```bash
git init
git add .
git commit -m "Audio reactive visualizer"
git branch -M main

# Create the repo first at https://github.com/new (empty, no README),
# then point this at it — replace YOUR-USERNAME and repo name:
git remote add origin https://github.com/YOUR-USERNAME/audio-reactive-visualizer.git
git push -u origin main
```

## Bonus — free live demo with GitHub Pages

Since this is a single static HTML file, GitHub Pages will host it for free:

1. In your repo: **Settings → Pages**
2. Under **Source**, pick branch `main`, folder `/ (root)`, **Save**
3. Wait ~1 min → your visualizer is live at
   `https://YOUR-USERNAME.github.io/audio-reactive-visualizer/`

Add that URL to the repo's **About** section (top right of the repo page) so visitors can try it instantly.

> Note: GitHub Pages serves over HTTPS, so the microphone works there — unlike some local setups.
