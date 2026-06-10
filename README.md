# CPS Demand & Supply Dashboard

Live dashboard comparing current month supply vs historical averages across all models and brands.  
Hosted on GitHub Pages — **auto-refreshes every time you push updated Excel files.**

---

## 🔄 Daily refresh workflow (takes 2 minutes)

This is all you do every day:

### Option A — GitHub Desktop (no terminal needed)
1. Open GitHub Desktop
2. Replace the Excel file(s) in the repo folder on your computer
3. Click **Commit to main** → **Push origin**
4. Done — GitHub automatically regenerates the dashboard in ~1 minute

### Option B — Terminal
```bash
# Copy your updated Excel into the repo folder, then:
git add .
git commit -m "Data update Jun 10"
git push
```

GitHub Actions picks it up, runs the script, updates `index.html`, and GitHub Pages serves the new version — all automatically.

---

## 🚀 One-time GitHub setup

### 1. Create a new GitHub repository
- Go to https://github.com/new
- Name it: `cps-dashboard`
- Set visibility: **Private** (recommended — contains business data)
- Click **Create repository**

### 2. Push this folder to GitHub
```bash
cd cps_dashboard
git init
git add .
git commit -m "Initial setup"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/cps-dashboard.git
git push -u origin main
```

### 3. Enable GitHub Pages
- Go to your repo → **Settings** → **Pages**
- Source: **Deploy from a branch**
- Branch: `main` | Folder: `/ (root)`
- Click **Save**

Your live URL:
```
https://YOUR_USERNAME.github.io/cps-dashboard/
```

### 4. Verify the Action ran
- Go to your repo → **Actions** tab
- You should see a green ✓ "Refresh Dashboard" run
- The live site updates within ~60 seconds of your push

---

## 📁 Repo structure

```
cps_dashboard/
├── .github/
│   └── workflows/
│       └── refresh.yml          ← GitHub Action (runs automatically on push)
├── CPS_Demand_supply.xlsx        ← Replace daily with updated supply data
├── CPS_Apr_may_data.xlsx         ← Add new month's data here when month ends
├── generate_dashboard.py         ← The generator script (auto-run by GitHub)
├── index.html                    ← Live dashboard (auto-generated, don't edit)
└── README.md
```

---

## 🧠 What gets auto-detected (nothing to configure)

| What changes | How it's detected |
|---|---|
| **Current month** | Read from dates in the Supply sheet (e.g. Jun → July) |
| **Days of supply data** | Count of unique dates in Supply sheet (e.g. 9 today → 10 tomorrow) |
| **Historical months** | All month labels found in `CPS_Apr_may_data.xlsx` Date column |
| **Historical divisor** | `number_of_months × 26` — grows automatically as you add months |

### Example: adding June to historical file
When June ends, just append June's data rows to `CPS_Apr_may_data.xlsx` with `Jun'26` in the Date column.  
The script will automatically use `Mar'26 + Apr'26 + May'26 + Jun'26 ÷ 104` without any code changes.

---

## ⚙️ Config (rarely needs changing)

All in the top of `generate_dashboard.py`:

```python
WORKING_DAYS = 26   # assumed working days per month
```

---

## 📊 Dashboard logic

| Metric | Formula |
|---|---|
| Current month demand / day | Monthly demand ÷ 26 |
| Current supply / day | Sum of all supply data ÷ number of days in file |
| Historical avg / day | Total of all historical months ÷ (months × 26) |
| Coverage % | Current supply / day ÷ Historical avg / day × 100 |

| Status | Threshold |
|---|---|
| 🟢 Over Delivery | > 100% |
| 🔵 On Track | 90 – 100% |
| 🔴 Under Delivery | < 90% |
