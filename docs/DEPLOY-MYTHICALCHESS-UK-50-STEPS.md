# 50 steps: `chimerachess.co.uk` live with CHIMERA

End state: testers open **https://chimerachess.co.uk**, register email/phone, play, and data syncs to **https://api.chimerachess.co.uk**.

Tools used: a domain registrar, GitHub, Render (API), Netlify (app).  
Time: roughly 2–4 hours the first time (mostly waiting on DNS/SSL).

---

## Part A — Buy the domain (steps 1–8)

1. Open a registrar that sells `.uk` domains (e.g. Namecheap, 123-reg, Cloudflare Registrar, Google Domains).
2. Search for **`chimerachess.co.uk`** and confirm it is available.
3. Add **`chimerachess.co.uk`** to your cart (1 year is fine to start).
4. Optionally add **`www.chimerachess.co.uk`** if offered as an alias or separate registration (many registrars include www free).
5. Complete checkout and pay.
6. Create a login at the registrar if you do not already have one.
7. Open the registrar’s **DNS** or **Domain management** page for `chimerachess.co.uk` — leave this tab open; you will return in Part E.
8. Write down your registrar login URL somewhere safe — you will need it twice more today.

---

## Part B — Put the code on GitHub (steps 9–14)

9. Install [Git](https://git-scm.com/downloads) if it is not installed (`git --version` in a terminal should work).
10. Create a free account at [github.com](https://github.com) if you do not have one.
11. On GitHub, click **New repository**, name it e.g. `mythicalchess` or `chimera-chess`, set **Private** or **Public**, click **Create repository**.
12. On your PC, open a terminal in the project folder: `c:\Users\js130\ai` (or wherever this code lives).
13. Run these commands (replace `YOUR_GITHUB_USER` and `REPO_NAME`):

    ```bash
    git init
    git add .
    git commit -m "Initial CHIMERA / Mythical Chess"
    git branch -M main
    git remote add origin https://github.com/YOUR_GITHUB_USER/REPO_NAME.git
    git push -u origin main
    ```

    If `git` is not recognized, install Git from step 9 and restart the terminal.

14. Refresh GitHub in the browser — you should see all project files in the repo.

---

## Part C — Deploy the data API on Render (steps 15–28)

15. Go to [render.com](https://render.com) and sign up (GitHub login is easiest).
16. Click **New +** → **Blueprint** (or **New** → **Web Service** if Blueprint is not shown).
17. **Connect** your GitHub account and **authorize** Render to read your repositories.
18. Select the repository you pushed in Part B.
19. Render should detect **`render.yaml`** in the repo root — confirm the service name **`chimera-data-api`** appears.
20. Click **Apply** / **Deploy Blueprint** and wait until the deploy status is **Live** (green). First deploy can take 5–15 minutes.
21. On the service page, copy the default URL (looks like `https://chimera-data-api-xxxx.onrender.com`) — paste it into Notepad; label it **API default URL**.
22. In the browser, open `https://YOUR-API-DEFAULT.onrender.com/api/chimera/health` — you should see JSON with `"ok": true`.
23. In Render, open **chimera-data-api** → **Environment** → add variable:  
    **Key:** `CHIMERA_CORS_ORIGIN` · **Value:** `https://chimerachess.co.uk` → **Save**.
24. Render will redeploy — wait until **Live** again.
25. Open **Settings** → **Custom Domains** → **Add custom domain** → type **`api.chimerachess.co.uk`** → continue.
26. Render shows a **DNS record** (usually **CNAME** `api` → something like `chimera-data-api.onrender.com`) — copy it exactly into Notepad; label it **API DNS**.
27. Do **not** close Render yet — leave the custom domain page open until Part E is done.
28. Optional: under **Environment**, confirm a **disk** is attached (Blueprint mounts `server/data` for saved emails/events). If missing, add a **Disk** in Settings, mount path `/opt/render/project/src/server/data`, 1 GB.

---

## Part D — Deploy the web app on Netlify (steps 29–40)

29. Go to [netlify.com](https://netlify.com) and sign up (GitHub login is easiest).
30. Click **Add new site** → **Import an existing project**.
31. Choose **GitHub** and authorize Netlify.
32. Select the **same repository** as Part B.
33. Set build settings (Netlify may auto-detect from `netlify.toml`):
    - **Build command:** `npm run build`
    - **Publish directory:** `dist`
34. Before deploying, open **Site configuration** → **Environment variables** → **Add a variable**:
    - **Key:** `VITE_CHIMERA_API_URL`
    - **Value:** `https://api.chimerachess.co.uk`  
    (Use the default Render URL from step 21 only until DNS works; then change to this and redeploy.)
35. Click **Deploy site** and wait until the deploy shows **Published** (first build 2–5 minutes).
36. Netlify gives a random URL like `https://random-name.netlify.app` — open it; the CHIMERA site should load.
37. In Netlify, go to **Domain management** → **Add a domain** → **Add existing domain** → enter **`chimerachess.co.uk`** → continue.
38. Netlify shows **DNS instructions** (records for apex and sometimes www) — copy every record into Notepad; label it **APP DNS**.
39. Also add **`www.chimerachess.co.uk`** in Domain management if you want www (Netlify will show extra records or recommend redirect).
40. Leave Netlify open on the domain page — verification stays **Pending** until Part E completes.

---

## Part E — DNS at your registrar (steps 41–46)

41. Log in to your **domain registrar** (Part A, step 7).
42. Open **DNS settings** / **Manage DNS** / **Advanced DNS** for **`chimerachess.co.uk`**.
43. Add the **API** record from Notepad (**API DNS**, step 26):
    - Type: **CNAME**
    - **Name / Host:** `api` (only the subdomain part, not the full domain)
    - **Value / Target:** the `….onrender.com` hostname Render gave you
    - TTL: automatic or 3600
44. Add the **APP** records from Notepad (**APP DNS**, step 38). Typical Netlify setup:
    - Apex `chimerachess.co.uk`: **ALIAS** or **ANAME** or **A** records exactly as Netlify lists (do not guess IPs).
    - `www`: **CNAME** to your `….netlify.app` site if Netlify instructs you to.
45. **Delete** any old conflicting records for `@` or `www` that point elsewhere (parking pages, old hosts).
46. Save DNS changes at the registrar. Propagation often takes 5–60 minutes; `.uk` can sometimes take up to 24 hours.

---

## Part F — Wait for SSL and verify (steps 47–50)

47. In **Netlify** → Domain management, refresh until **`chimerachess.co.uk`** shows **HTTPS enabled** / verified (green check).
48. In **Render** → Custom domains, refresh until **`api.chimerachess.co.uk`** shows **Verified** with a certificate.
49. Run these checks in your browser:
    - `https://chimerachess.co.uk` — site loads
    - `https://chimerachess.co.uk/#train` — Train section works
    - `https://chimerachess.co.uk/#account` or **Settings** — account form loads
    - `https://api.chimerachess.co.uk/api/chimera/health` — JSON `"ok": true`
50. On the live site: **Settings → Account** → **Register** with a test email → confirm **SERVER ONLINE** → play one move or finish a drill → in Render (optional) check volume/files under `server/data/accounts/` after sync. **Share `https://chimerachess.co.uk` with testers.**

---

## If something fails

| Problem | Fix |
|--------|-----|
| Site loads, **SERVER OFFLINE** | `VITE_CHIMERA_API_URL` wrong or missing — set to `https://api.chimerachess.co.uk`, **trigger new deploy** on Netlify. |
| API health works on Render URL but not `api.chimerachess.co.uk` | DNS step 43 not propagated yet — wait, or fix CNAME target typo. |
| CORS / sync errors in browser console | `CHIMERA_CORS_ORIGIN` on Render must be exactly `https://chimerachess.co.uk` (no trailing slash). |
| Blank page on custom domain | Netlify apex DNS wrong — use only the records Netlify displays. |
| `git` / `git push` fails | Install Git; or upload repo via GitHub web “Upload files”. |

---

## After step 50 (ongoing)

- **Change app code:** push to GitHub → Netlify auto-rebuilds.
- **Change API:** push to GitHub → Render auto-redeploys.
- **Rebuild env:** any change to `VITE_CHIMERA_API_URL` requires a **new Netlify build**, not just DNS.
- **Tester link:** always send **`https://chimerachess.co.uk`** (hash routes like `#play` work without extra DNS).

---

## Quick copy-paste values

```env
# Netlify → Environment variables (before build)
VITE_CHIMERA_API_URL=https://api.chimerachess.co.uk

# Render → Environment
CHIMERA_CORS_ORIGIN=https://chimerachess.co.uk
```

**Tester URL:** https://chimerachess.co.uk

**Online play:** Play → **Online** (or `#play-blitz`). Needs API + WebSocket on Render — see [GO-ONLINE.md](GO-ONLINE.md).
