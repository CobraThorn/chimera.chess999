# 80 steps: CHIMERA online (site + bullet / blitz / rapid)

Four parts × **20 steps** each. End state: **https://chimerachess.co.uk** loads the app; **Play → Online** pairs real players via WebSocket on Render.

| Part | Steps | What |
|------|-------|------|
| **A** | 1–20 | GitHub — get code in a repo |
| **B** | 21–40 | Render — API + WebSocket matchmaking |
| **C** | 41–60 | Netlify — frontend + env var |
| **D** | 61–80 | DNS, verify online play, share with testers |

**Tools:** Git, GitHub, Render, Netlify, domain registrar (for `chimerachess.co.uk`).  
**Time:** ~2–4 hours first time (DNS/SSL waiting).

Keep a Notepad file with labels: **API URL**, **API DNS**, **APP DNS**, **Netlify URL**.

---

## Part A — GitHub (steps 1–20)

1. On your PC, confirm the project folder exists: `c:\Users\js130\ai` (or your clone path).
2. Install [Git for Windows](https://git-scm.com/downloads) if needed.
3. Open **PowerShell** or **Terminal**.
4. Run `git --version` — you should see a version number, not “not recognized”.
5. `cd` into the project folder, e.g. `cd c:\Users\js130\ai`.
6. Run `npm install` once so `package-lock.json` is current (optional but recommended before commit).
7. Run `npm run build` once locally to confirm the app builds (fixes surprises on Netlify later).
8. Create a free [GitHub](https://github.com) account if you do not have one.
9. In the browser, go to [github.com/new](https://github.com/new).
10. Repository name: e.g. **`chimerachess`** (lowercase, no spaces).
11. Choose **Private** or **Public** (Private is fine for testers you invite).
12. Leave “Add README” **unchecked** — you already have code locally.
13. Click **Create repository**.
14. Copy the HTTPS URL GitHub shows, e.g. `https://github.com/YOUR_USER/chimerachess.git` — paste into Notepad as **Git remote URL**.
15. In the terminal (still in the project folder), run: `git init`
16. Run: `git add .` (`.gitignore` excludes `node_modules`, `dist`, `.env`, `server/data`).
17. Run: `git commit -m "CHIMERA: app, API, online bullet blitz rapid"`
18. Run: `git branch -M main`
19. Run: `git remote add origin` followed by your **Git remote URL** from step 14.
20. Run: `git push -u origin main` — sign in to GitHub if prompted; refresh the repo page and confirm files appear.

---

## Part B — Render API + WebSocket (steps 21–40)

21. Open [render.com](https://render.com) and sign up (use **Sign in with GitHub**).
22. Authorize Render to access your GitHub account when asked.
23. Click **New +** → **Blueprint** (if missing, use **New** → **Web Service** and connect repo manually).
24. Select the **`chimerachess`** (or your) repository from the list.
25. Confirm Render detects **`render.yaml`** at the repo root and shows service **`chimera-data-api`**.
26. Click **Apply** / **Deploy Blueprint** — wait until status is **Live** (first time often 5–15 minutes).
27. On the service dashboard, copy the default URL, e.g. `https://chimera-data-api-xxxx.onrender.com` — save as **API default URL** in Notepad.
28. In a browser tab, open **`https://YOUR-API-DEFAULT-URL/api/chimera/health`** (replace with step 27 URL).
29. Confirm JSON contains `"ok": true` and `"online"` with `"pools": ["bullet","blitz","rapid"]` — that means matchmaking WebSocket is running.
30. In Render, open **chimera-data-api** → **Environment** (left sidebar).
31. Find or add **`CHIMERA_CORS_ORIGIN`** — set value to **`https://chimerachess.co.uk`** (or your Netlify `*.netlify.app` URL until DNS works).
32. Click **Save Changes** — Render will **redeploy**; wait until **Live** again.
33. Open **Settings** → scroll to **Health Check Path** — should be `/api/chimera/health` (from blueprint).
34. Open **Settings** → **Disks** — confirm disk **`chimera-data`** mounted at `/opt/render/project/src/server/data` (saves accounts/events); add 1 GB disk if missing.
35. Still in **Settings**, open **Custom Domains** → **Add custom domain**.
36. Type **`api.chimerachess.co.uk`** → continue / verify.
37. Render shows a **CNAME** target (e.g. `chimera-data-api.onrender.com`) — copy **Host** `api` and **Target** hostname into Notepad as **API DNS**.
38. Leave the Render custom domain page open — status may say **Pending** until Part D DNS is done.
39. Optional: open **Logs** tab — you should see `Online: WS /api/chimera/ws` when the server starts.
40. Re-open the health URL from step 28 after redeploy — still `"ok": true`; bookmark it for later debugging.

---

## Part C — Netlify frontend (steps 41–60)

41. Open [netlify.com](https://netlify.com) and sign up (**Sign in with GitHub**).
42. Authorize Netlify to read your repositories.
43. Click **Add new site** → **Import an existing project**.
44. Choose **GitHub** as the Git provider.
45. Select the **same repository** you pushed in Part A.
46. Netlify should read **`netlify.toml`**: build command **`npm run build`**, publish directory **`dist`** — confirm both match.
47. **Before** clicking Deploy, go to **Site configuration** → **Environment variables** (or **Build environment variables** during setup).
48. Click **Add a variable** / **New variable**:
    - **Key:** `VITE_CHIMERA_API_URL`
    - **Value:** `https://api.chimerachess.co.uk`  
    (If API DNS is not live yet, use **API default URL** from step 27, then change to `https://api.chimerachess.co.uk` later and redeploy.)
49. Ensure the variable is set for **Production** (and **Deploy previews** if you want).
50. Click **Deploy site** (or **Save and deploy**).
51. Wait until deploy status is **Published** (build often 2–5 minutes); open **Deploy log** if build fails and fix errors shown there.
52. Copy the site URL Netlify assigns, e.g. `https://something-random.netlify.app` — save as **Netlify URL** in Notepad.
53. Open **Netlify URL** in the browser — CHIMERA landing page should load (scroll to **Play**).
54. Click **Play** → tab **Online** — you should **not** see “Connection failed” if step 48 URL is correct (API may be waking on free tier ~30s first time).
55. In Netlify, go to **Site configuration** → **Domain management** → **Add a domain** → **Add existing domain**.
56. Enter **`chimerachess.co.uk`** → continue.
57. Netlify shows required **DNS records** for apex and often **www** — copy every record into Notepad as **APP DNS**.
58. Add **`www.chimerachess.co.uk`** in domain management if prompted (or enable **www redirect** to apex).
59. Domain status will show **Pending DNS** until Part D — that is normal.
60. Note: any change to **`VITE_CHIMERA_API_URL`** requires **Deploys** → **Trigger deploy** → **Deploy site** (rebuild), not just saving the variable.

---

## Part D — DNS, verify online, go live (steps 61–80)

61. Log in to your **domain registrar** (where you bought `chimerachess.co.uk`).
62. Open **DNS** / **Advanced DNS** / **Manage DNS** for **`chimerachess.co.uk`**.
63. Add the **API** record from Notepad (**API DNS**, step 37):
    - **Type:** CNAME  
    - **Host / Name:** `api`  
    - **Value / Target:** Render’s `….onrender.com` hostname (no `https://`)  
    - **TTL:** automatic or 3600
64. Add **APP** records from Notepad (**APP DNS**, step 57) exactly as Netlify lists (apex often **A** or **ALIAS**; **www** often **CNAME** to `….netlify.app`).
65. Remove old conflicting **@** or **www** records (parking page, old host).
66. Save DNS at the registrar.
67. Wait 15–60 minutes (sometimes up to 24h); check propagation with [dnschecker.org](https://dnschecker.org) for `api.chimerachess.co.uk` and `chimerachess.co.uk`.
68. In **Render** → custom domain **`api.chimerachess.co.uk`** — wait until status is **Verified** / certificate issued.
69. In **Netlify** → **Domain management** — wait until **`chimerachess.co.uk`** shows **Netlify DNS** verified / HTTPS active.
70. Open `https://api.chimerachess.co.uk/api/chimera/health` — must return `"ok": true` and `"online"`.
71. If you used Render’s default URL in Netlify earlier, set **`VITE_CHIMERA_API_URL`** to **`https://api.chimerachess.co.uk`** and **trigger a new Netlify deploy** (step 60).
72. Open **`https://chimerachess.co.uk`** — site loads with padlock (HTTPS).
73. Go to **Play** → **Online** — confirm small text shows queue stats (e.g. `0 live · 0 in queue`), not permanent “Connection failed”.
74. Open a **second browser tab** (or phone on mobile data) → same site → **Online** → **Blitz** → **Find game** on both.
75. Both tabs should show **matched** within a few seconds and see the same board.
76. Play one move on one side — the other side updates; clocks count down.
77. Test **`#play-bullet`** and **`#play-rapid`** in the URL bar — should open Online and auto-queue that pool.
78. In Render **Environment**, confirm **`CHIMERA_CORS_ORIGIN`** is **`https://chimerachess.co.uk`** (not `*` in production if you locked it down).
79. Send testers: **https://chimerachess.co.uk** — instructions: **Play → Online → Blitz** (or Bullet/Rapid); **two players** needed in the **same** pool to pair.
80. Optional smoke test: **Settings → Account** register email → play one AI game (**You vs CHIMERA**) → confirm data API still syncs (`/api/chimera/health` stats increase over time).

---

## Quick reference (copy-paste)

```env
# Netlify → Environment variables (before / between builds)
VITE_CHIMERA_API_URL=https://api.chimerachess.co.uk

# Render → Environment
CHIMERA_CORS_ORIGIN=https://chimerachess.co.uk
```

| URL | Purpose |
|-----|---------|
| https://chimerachess.co.uk | Testers open this |
| https://api.chimerachess.co.uk/api/chimera/health | API + online status |
| wss://api.chimerachess.co.uk/api/chimera/ws | WebSocket (automatic from env var) |

---

## Troubleshooting (short)

| Problem | Step to re-check |
|---------|------------------|
| `git push` fails | A14–A20 auth; install Git A2–A4 |
| Health URL 404 | B26–B28 service not Live |
| No `"online"` in health | B21–B40 wrong service or old deploy |
| Connection failed in app | C48, C71, D70 API URL + HTTPS |
| Never pairs | D74–D75 two clients, **same** pool |
| DNS stuck | D61–D67 records exact; wait longer |

---

## Local test (before Part A)

```bash
npm run dev:full
```

Two tabs → http://localhost:5173 → **Play → Online → Blitz → Find game**.

---

**Shorter guides:** [GO-ONLINE.md](GO-ONLINE.md) · [50-step full deploy](DEPLOY-MYTHICALCHESS-UK-50-STEPS.md)
