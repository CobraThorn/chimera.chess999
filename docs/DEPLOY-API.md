# Deploy CHIMERA Data API (Railway or Render)

The data API collects **email**, **phone**, **consents**, and **telemetry** from the web app.

**Want `https://chimerachess.co.uk`?** See **[DEPLOY-CUSTOM-DOMAIN.md](./DEPLOY-CUSTOM-DOMAIN.md)** (buy domain → DNS → `api.chimerachess.co.uk` + `chimerachess.co.uk`).

---

## Option A — Render (recommended, free tier)

### 1. Push code to GitHub

Ensure `render.yaml` is in the repo root.

### 2. Create Blueprint

1. Go to [render.com](https://render.com) → **New** → **Blueprint**
2. Connect your GitHub repo
3. Render reads `render.yaml` and creates **chimera-data-api**
4. Wait for deploy → copy the URL, e.g. `https://chimera-data-api.onrender.com`

### 3. Verify

```bash
curl https://YOUR-API.onrender.com/api/chimera/health
```

Expect `"ok": true` and `"online": { "pools": ["bullet","blitz","rapid"], ... }` — WebSocket matchmaking is live at `/api/chimera/ws`.

### 4. Point the frontend at the API

Create `.env` in the project root:

```env
VITE_CHIMERA_API_URL=https://YOUR-API.onrender.com
```

Build and deploy the app:

```bash
npm run build
```

Deploy the `dist` folder to [Netlify Drop](https://app.netlify.com/drop) or Vercel.

### 5. CORS (production)

In Render → **chimera-data-api** → **Environment**:

| Key | Value |
|-----|--------|
| `CHIMERA_CORS_ORIGIN` | `https://your-app.netlify.app` |

Use `*` only for testing.

**Note:** Free Render services spin down after inactivity; first request may take ~30s.

---

## Option B — Railway

### 1. New project

1. [railway.app](https://railway.app) → **New Project** → **Deploy from GitHub repo**
2. Select this repository

### 2. Volume (persistent data)

1. Project → **Add Volume**
2. Mount path: `/app/server/data`
3. Size: 1 GB+

### 3. Variables

| Key | Value |
|-----|--------|
| `CHIMERA_DATA_DIR` | `/app/server/data` |
| `CHIMERA_CORS_ORIGIN` | `https://your-frontend-url.app` or `*` |

Railway sets `PORT` automatically.

### 4. Public URL

1. Service → **Settings** → **Networking** → **Generate Domain**
2. Copy URL → use as `VITE_CHIMERA_API_URL`

### 5. Health check

Railway uses `railway.toml` → `GET /api/chimera/health`

---

## Frontend + API checklist

| Step | Done |
|------|------|
| API deployed, health returns `ok` | ☐ |
| `VITE_CHIMERA_API_URL` set before `npm run build` | ☐ |
| Frontend deployed (Netlify/Vercel) | ☐ |
| Register at `#account` on live site | ☐ |
| Settings shows **SERVER ONLINE** | ☐ |
| `server/data` or volume has new `.json` / `.jsonl` files | ☐ |

---

## API reference

| Method | Path | Body |
|--------|------|------|
| GET | `/api/chimera/health` | — |
| POST | `/api/chimera/sync` | `{ "account": {…}, "events": […] }` |

Data layout (on disk):

- `server/data/accounts/{userId}.json`
- `server/data/events/{userId}.jsonl`

---

## Local dev (both services)

```bash
npm run dev:full
```

- App: http://localhost:5173  
- API: http://localhost:8787 (proxied as `/api/chimera` in dev)

No `VITE_CHIMERA_API_URL` needed locally — Vite proxy handles it.
