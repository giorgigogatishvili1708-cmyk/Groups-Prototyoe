# BoG Groups prototype (static site for Vercel)

| URL | File | What it is |
|---|---|---|
| `/` (also `/flows`) | `index.html` | Usability-test flows: one continuous session, tasks 1 → 5 |
| `/prototype` (also `/full`) | `prototype.html` | The full clickable prototype with the dev panel |

Both files are self-contained: all code, styles and images are inside the HTML. There's no build step and there are no dependencies.

## Put it on GitHub

1. On github.com, create a new repository (for example `groups-prototype`). **Private** is fine.
2. Click **uploading an existing file**, drag in **everything in this folder** (`index.html`, `prototype.html`, `vercel.json`, `robots.txt`, `.gitignore`, `README.md`), then commit.
   - Hidden files: in Finder, press Cmd+Shift+. to show `.gitignore`. It's optional.

## Make it live on Vercel

1. On vercel.com: **Add New… → Project → Import** the GitHub repository.
2. Settings:
   - **Framework Preset:** Other
   - **Build Command:** leave empty (or turn the override on and leave it blank)
   - **Output Directory:** leave empty (the repository root)
3. **Deploy.** You get a URL like `https://groups-prototype.vercel.app`.

Every new commit to the repository redeploys automatically.

## URL options (flows)

- `?mod=1`: moderator panel (task jump, restart, next task, log export)
- `?auto=0`: no automatic hand-off to the next task
- `?guide=off|soft|full`: guardrail level
- `?variant=beka`: Beka variant of task 4
- `#/s/3`: start at task 3

For example: `https://<your-app>.vercel.app/?mod=1#/s/2`

## Privacy

The site tells search engines not to index it (`robots.txt` + `X-Robots-Tag`), but anyone with the link can open it. To restrict it, turn on Vercel **Settings → Deployment Protection** (Vercel Authentication or password protection, depending on your plan).

## Updating

Replace `index.html` / `prototype.html` with the new `Groups-Flows.html` / `Groups-Prototype.html` from the prototype folder (renamed), then commit. Vercel redeploys.
