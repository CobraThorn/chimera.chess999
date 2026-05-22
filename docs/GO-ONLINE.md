# Go live — site + online play (bullet / blitz / rapid)

**Confused? Start here:** **[START-HERE-SIMPLE.md](START-HERE-SIMPLE.md)** (3 short paths, no jargon).  
**Detailed walkthrough:** [GO-ONLINE-80-STEPS.md](GO-ONLINE-80-STEPS.md) — only after the simple path works.

Online play needs **two** deployments:

| Part | Host | Why |
|------|------|-----|
| **Frontend** | Netlify | Static React app |
| **API + WebSocket** | Render | Matchmaking at `/api/chimera/ws` |

Netlify alone cannot run WebSockets. The game server must be the Node app (`npm start`).

---

## 1. Push code to GitHub

From the project folder:

```bash
git init
git add .
git commit -m "CHIMERA: app, API, online bullet/blitz/rapid"
```

Create a repo on [github.com/new](https://github.com/new) (name e.g. `chimerachess`), then:

```bash
git remote add origin https://github.com/YOUR_USER/chimerachess.git
git branch -M main
git push -u origin main
```

---

## 2. Deploy API (Render)

1. [render.com](https://render.com) → **New** → **Blueprint**
2. Connect the GitHub repo → apply **`render.yaml`**
3. Wait until **chimera-data-api** is **Live**
4. **Environment** → set:
   - `CHIMERA_CORS_ORIGIN` = `https://chimerachess.co.uk` (or your Netlify URL until DNS is ready)
5. Test:
   ```bash
   curl https://YOUR-SERVICE.onrender.com/api/chimera/health
   ```
   Response should include `"online": { "activeGames": ..., "queued": ... }`.

### Custom API domain (`api.chimerachess.co.uk`)

Render → service → **Settings** → **Custom Domains** → add `api.chimerachess.co.uk`.

At your domain registrar (e.g. Namecheap):

| Type | Host | Value |
|------|------|--------|
| CNAME | `api` | hostname Render shows (e.g. `….onrender.com`) |

Wait for DNS + Render SSL (often 15–60 minutes).

---

## 3. Deploy frontend (Netlify)

1. [netlify.com](https://netlify.com) → **Add new site** → **Import from Git**
2. Select the same repo
3. Build settings (from `netlify.toml`):
   - Build: `npm run build`
   - Publish: `dist`
4. **Site configuration** → **Environment variables** → add **before** first deploy:

   | Key | Value |
   |-----|--------|
   | `VITE_CHIMERA_API_URL` | `https://api.chimerachess.co.uk` |

   Use your Render URL (`https://….onrender.com`) until `api.chimerachess.co.uk` works, then change and **trigger redeploy**.

5. Deploy → open the `*.netlify.app` URL → **Play** → **Online** → pick Blitz → **Find game**

### Custom site domain (`chimerachess.co.uk`)

Netlify → **Domain management** → add `chimerachess.co.uk` and `www` → add the DNS records Netlify shows (usually apex **A** + **www** CNAME).

Update Render:

```
CHIMERA_CORS_ORIGIN=https://chimerachess.co.uk
```

Redeploy Render after changing CORS.

---

## 4. Verify online play

1. Open **https://chimerachess.co.uk** (or Netlify URL) → **Play** → **Online**
2. Status should show queue stats (not “Connection failed”)
3. Open a **second** tab (or phone) → same pool → both should **match**
4. Moves sync; clocks count down; resign/draw work

Nav shortcuts: `#play-bullet`, `#play-blitz`, `#play-rapid`

---

## 5. Share with testers

Send: **https://chimerachess.co.uk**  
Tell them: **Play → Online → Blitz** (or Bullet/Rapid). Two people in the same pool get paired.

---

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| **Connection failed** / offline | API not running; wrong `VITE_CHIMERA_API_URL`; rebuild Netlify after fixing env |
| Works on `netlify.app` but not custom domain | DNS not propagated; check Netlify domain panel |
| WS fails, REST works | Must use **https** API URL in env (→ `wss://` automatically) |
| Queue never pairs | Need **two** clients in the **same** pool (Bullet/Blitz/Rapid) |
| Render sleeps (free plan) | First request wakes API (~30s); upgrade or use paid plan for 24/7 |

---

## Local test before production

```bash
npm run dev:full
```

Open two tabs on http://localhost:5173 → Play → Online → Blitz → Find game.
