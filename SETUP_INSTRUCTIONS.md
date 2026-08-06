# Stream Radar — Setup Guide

A small public status page for your Twitch profile that says whether it's currently
raining where you are — without ever exposing your actual location to viewers,
to me, or in any public file/log. Your coordinates only ever live inside GitHub's
encrypted Secrets store, which even the repo owner can't view again after saving.

## What's in this folder

- `index.html` — the public status page (styled to match your channel)
- `status.json` — the data file the page reads (starts empty, gets overwritten automatically)
- `.github/workflows/check-weather.yml` — a scheduled job that checks the weather
  every ~30 minutes and updates `status.json`

## One-time setup (~5 minutes)

1. **Create a new repository** on GitHub. Public repo is fine — Secrets stay encrypted
   even in public repos. Suggested name: `stream-radar`.

2. **Upload these three files/folders**, keeping the folder structure exactly as-is
   (the `.github/workflows/check-weather.yml` path matters — GitHub only picks up
   workflows from that exact location). Easiest way: on the repo page, use
   "Add file → Upload files" and drag the whole folder in, or use `git push` if
   you're comfortable with git.

3. **Add your coordinates as encrypted secrets** — this is the only place your
   location is ever entered, and it's never shown to me or to viewers:
   - Go to **Settings → Secrets and variables → Actions → New repository secret**
   - Add a secret named `WEATHER_LAT` with your latitude (e.g. `43.6532`)
   - Add a secret named `WEATHER_LON` with your longitude (e.g. `-79.3832`)
   - You can get your coordinates privately from Google Maps: right-click your
     location on the map → click the lat/long numbers to copy them. Do this on
     your own — no need to share the actual numbers with anyone.

4. **Allow the workflow to commit updates**:
   - Go to **Settings → Actions → General → Workflow permissions**
   - Select **"Read and write permissions"** → Save

5. **Turn on GitHub Pages**:
   - Go to **Settings → Pages**
   - Under "Build and deployment", set Source to **"Deploy from a branch"**
   - Branch: `main`, folder: `/ (root)` → Save
   - GitHub will give you a URL like `https://yourusername.github.io/stream-radar/`

6. **Run the check once manually** so the page isn't blank while waiting for the
   first scheduled run:
   - Go to the **Actions** tab → click **"Check Weather"** in the sidebar →
     **"Run workflow"** → Run it
   - Wait ~30 seconds, then refresh your GitHub Pages URL — you should see a live
     raining/clear status.

## Add it to your Twitch profile

Same place we added your Discord and YouTube links:
- Go to your [Creator Dashboard → Settings → Channel](https://dashboard.twitch.tv/u/northcorr/settings/channel/about)
- Under **Social Links**, add:
  - **Link Title**: `Stream Radar` (or whatever you like — "Rain Check", "Weather", etc.)
  - **Link URL**: your GitHub Pages URL from step 5

That's it — it'll now show up as a clickable link on your About page and auto-update
itself every ~30 minutes with no further action from you.

## Privacy notes

- Your coordinates are stored only as GitHub Action Secrets — encrypted at rest,
  never shown in logs, never written to any file in the repo.
- The workflow only ever writes `raining`, `condition` (e.g. "Rain", "Clear sky"),
  and a timestamp to `status.json` — never the coordinates themselves.
- If you ever want to stop this, just delete the repository or disable the
  workflow under the Actions tab.
