# 🗺 The Grant Map – Automated Dashboard

**Made by Harsha, Ramesh, Pramod, Jabi and Muthu**  
JSS College of Pharmacy / JSS AHER, Mysuru, Karnataka

---

## What This Does

Every morning at **7:00 AM IST**, GitHub automatically:
1. Scrapes **ICMR** (`icmr.gov.in/call-for-proposals`) for live grant calls
2. Scrapes **ANRF** schedule (`phdtalks.org` + direct fallback)
3. Merges with your manually maintained `static_grants.json`
4. Saves everything to `grants_live.json`
5. Your dashboard (`index.html`) reads the latest JSON on every page load

---

## File Structure

```
grant_map/
├── index.html              ← The dashboard (deploy this to GitHub Pages)
├── scraper.py              ← Python scraper (ICMR + ANRF + merge)
├── static_grants.json      ← Your manual grants (MRC, Merck, Fogarty, etc.)
├── grants_live.json        ← Auto-generated daily (do NOT edit manually)
├── requirements.txt        ← Python dependencies
├── .github/
│   └── workflows/
│       └── scrape.yml      ← GitHub Actions daily schedule
└── README.md               ← This file
```

---

## One-Time Setup (15 minutes)

### Step 1 — Create GitHub Account
Go to [github.com](https://github.com) → Sign Up (free)

### Step 2 — Create a New Repository
- Click **+** → **New repository**
- Name it: `grant-map` (or any name)
- Set to **Public** (required for free GitHub Pages)
- Click **Create repository**

### Step 3 — Upload All Files
Option A (drag & drop):
- Open your repository → click **Add file** → **Upload files**
- Drag all files from this folder
- Commit with message: `Initial upload`

Option B (GitHub Desktop app — easier):
- Download [GitHub Desktop](https://desktop.github.com)
- Clone your repo → copy files in → Commit → Push

### Step 4 — Enable GitHub Pages
- Go to your repo → **Settings** → **Pages**
- Source: **Deploy from a branch**
- Branch: `main` / folder: `/ (root)`
- Click **Save**
- Your URL will be: `https://YOUR_USERNAME.github.io/grant-map/`

### Step 5 — Enable GitHub Actions
- Go to your repo → **Actions** tab
- If prompted, click **Enable GitHub Actions**
- The workflow will run automatically at 7 AM IST daily
- To test immediately: **Actions** → **Daily Grant Scraper** → **Run workflow**

---

## Adding New Grants Manually

Open `static_grants.json` and add a new entry to the `"grants"` array:

```json
{
  "id": "static_016",
  "funder": "Funder Name",
  "cat": "🌍 International",
  "title": "Grant Title Here",
  "dl": "2026-09-30",
  "open_date": null,
  "dlLabel": "Sep 30, 2026",
  "funding": "Amount / norms",
  "cat2": "Grant Type",
  "tags": ["International", "Clinical", "Pharmacy"],
  "focus": "Description of what the grant funds.",
  "url": "https://official-funder-url.com/apply",
  "source": "funder.com (verified DD Mon YYYY)",
  "elig": ["Who qualifies – bullet 1", "Who qualifies – bullet 2"],
  "inelig": ["Who does NOT qualify"]
}
```

**For rolling grants** (no deadline), add `"rolling": true` and set `"dl": null`.

Commit the change → GitHub Actions runs tonight and picks it up automatically.

---

## How Status is Computed Automatically

The scraper recomputes status on every run:

| Condition | Status |
|-----------|--------|
| `rolling: true` OR `dl: null` | 🩵 Rolling |
| Today > deadline | ⚪ Closed |
| Today < open_date | 🔵 Upcoming |
| Deadline within 30 days | 🟡 Closing |
| Deadline > 30 days away | 🟢 Open |

You never need to manually update status — just set the `dl` date and the system handles the rest.

---

## Running Locally (Optional)

```bash
# Install dependencies
pip install -r requirements.txt
playwright install chromium

# Run scraper
python scraper.py

# Output: grants_live.json
```

Open `index.html` in your browser — it will load from `grants_live.json` automatically.

---

## Email Alerts (Optional Setup)

To receive email alerts when grants close within 7 days:

1. Go to your repo → **Settings** → **Secrets and variables** → **Actions**
2. Add secrets:
   - `SMTP_USER` → your Gmail address
   - `SMTP_PASSWORD` → Gmail App Password (not your normal password)  
     Get it: Google Account → Security → 2-Step Verification → App Passwords
   - `ALERT_EMAIL` → email to send alerts to
3. In `scraper.py`, uncomment the alert block at the bottom

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Scraper runs but `grants_live.json` unchanged | Check Actions logs for errors |
| Dashboard shows "cached data" warning | Scraper ran but fetch failed — reload page |
| ICMR scrape returns 0 grants | ICMR may have changed their HTML — check manually |
| GitHub Pages shows 404 | Wait 5 mins after enabling Pages, then hard-refresh |
| Actions workflow not running | Check Actions tab is enabled; verify cron syntax |

---

## Updating Grants

| What changed | What to do |
|---|---|
| New ICMR/ANRF call | Nothing — scraper finds it automatically |
| New international grant | Add to `static_grants.json`, commit |
| Grant deadline changed | Update `dl` in `static_grants.json`, commit |
| Grant closed | Leave it — status auto-updates to Closed |

---

*Deadlines verified from official sources. Always confirm on funder portals before applying.*
