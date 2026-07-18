# Caliber Leave & PTO Portal — Full Setup Guide

Single-file static app on GitHub Pages · data in Supabase · ~20 minutes end to end.

## Part 1 — Supabase (~7 min)
1. supabase.com → New project (free). Region: Singapore. The database password here is never used by the portal.
2. **SQL Editor → New query** → paste ALL of `supabase_setup.sql` → **Run**. The output ends with a policy list: **11 rows = correct**. The script is safe to re-run any time.
3. **Authentication → Users → Add user**: e.g. `wfm-portal@leangroup.com` + strong password, and **tick "Auto Confirm User"** (required). This one shared account powers admin saves for the whole team — RTAs never see or type it.
4. **Settings → API**: copy the **Project URL** and the **anon public** key.

## Part 2 — Configure `index.html` (~3 min)
Edit two blocks near the top of the file:

    const SUPABASE_CONFIG = {
      url: "https://xxxx.supabase.co",
      anonKey: "eyJhbGciOi...",
      table: "leave_requests"
    };

    const ADMIN_ACCESS = {
      portalPassword: "wfm2026",              // team login password (changeable in Settings)
      cloudEmail: "wfm-portal@leangroup.com", // the Part-1 step-3 account
      cloudPassword: "its-password"
    };

The anon key is designed to be public — row-level security (from the SQL) protects the data.

## Part 3 — GitHub Pages (~4 min)
5. github.com → New repository → Public → Create.
6. Upload `index.html` (+ this README) → Commit.
7. **Settings → Pages** → Deploy from a branch → `main` / `/ (root)` → Save.
8. Live in ~1 min at `https://<username>.github.io/<repo>/`.

## Part 4 — First login & data (~5 min)
9. Open the URL → badge shows **CLOUD CONNECTED** → **RTA Login** → default password `wfm2026`.
   Expect the toast **"Admin unlocked — cloud sync active"** and auth chip **RTA Admin**.
   If it says *(local)*: ADMIN_ACCESS credentials are wrong, or Auto Confirm was not ticked.
10. **Settings → Admin password** → change it off the default immediately.
11. Seed data: from the internal data build, Settings → **Download backup (.json)**; on the live portal, Settings → **Restore from backup** → choose the file → confirm **"Also replace the CLOUD data"**.
12. Verify: approve any test request → Supabase **Table Editor → leave_requests** → `status` / `decided_by` updated = full write path confirmed.

## Part 5 — Rollout
- SharePoint: Embed web part → paste the Pages URL.
- Team: share only the portal password. Everyone auto-connects.
- Updates: push a new `index.html` to `main` — Pages redeploys, data untouched.
- Optional email alerts: Settings → Send test email (EmailJS; free tier 200/mo).

## Troubleshooting
| Symptom | Cause → Fix |
|---|---|
| Filing PTO doesn't reach Table Editor | SQL never ran → run `supabase_setup.sql` |
| Admin actions don't save; chip says "RTA Admin (local)" | Shared account missing/wrong in ADMIN_ACCESS, or Auto Confirm unticked |
| "Connection failed" on load | URL/anon key typo in SUPABASE_CONFIG |
| Ran SQL twice, got errors before v2 | v2 script is re-runnable — run it once, it repairs state |
| Wrong password at login | Password may have been changed in Settings (cloud-synced) — check with the team |
