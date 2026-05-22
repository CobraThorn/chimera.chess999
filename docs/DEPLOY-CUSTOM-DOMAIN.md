# Custom domain — e.g. `chimerachess.co.uk`

You own **`chimerachess.co.uk`** — that works the same way (`.co.uk` is very common for UK sites).

**Want every click spelled out?** See **[DEPLOY-MYTHICALCHESS-UK-50-STEPS.md](./DEPLOY-MYTHICALCHESS-UK-50-STEPS.md)** (50 numbered steps from buying the domain to sharing the link).

Your public URLs can use a domain you own (for example **`https://chimerachess.co.uk`**). The app stays branded **CHIMERA** inside the product; the URL is what testers bookmark and share.

Buy the name at a registrar, then point DNS at Netlify (app) and Render (API).

---

## 1. Register `chimerachess.co.uk`

Check availability and buy at a **Nominet-accredited** registrar (Namecheap, 123-reg, Cloudflare, Google Domains UK, etc.).

**.uk notes**

- `.uk` is aimed at UK presence; registrars often ask for a UK address or identity (Nominet rules).
- You usually get **`chimerachess.co.uk`** (apex) and can add **`www.chimerachess.co.uk`**.
- Annual cost is typically modest (£5–£15/year depending on registrar).

**Recommended layout for this project**

| Service | Hostname | URL |
|---------|----------|-----|
| **Web app** (Netlify/Vercel) | apex + optional `www` | `https://chimerachess.co.uk` |
| **Data API** (Render) | `api` | `https://api.chimerachess.co.uk` |

Alternative: `play.chimerachess.co.uk` for the app if you want the apex for a marketing landing page later.

You must **own** the domain before Netlify/Render can attach it.

---

## 2. Deploy the app (frontend) → `chimerachess.co.uk`

### Netlify

1. Deploy the site (`dist` after `npm run build`).
2. **Domain management** → **Add custom domain** → enter `chimerachess.co.uk` (and `www.chimerachess.co.uk` if you want).
3. Netlify shows **DNS records** (e.g. `A` / `CNAME` / `ALIAS`).
4. At your registrar, add those records.
5. Wait for SSL (Netlify provisions HTTPS automatically).

### Vercel

1. Project → **Settings** → **Domains** → add `chimera.chess`.
2. Add the DNS records Vercel lists at your registrar.

### Build env (required for API sync)

In Netlify/Vercel **Environment variables** (set before build):

```env
VITE_CHIMERA_API_URL=https://api.chimerachess.co.uk
```

Then trigger a new deploy / rebuild.

---

## 3. Deploy the API → `api.chimerachess.co.uk`

### Render

1. Open service **chimera-data-api** → **Settings** → **Custom Domains**.
2. Add `api.chimerachess.co.uk`.
3. Render shows a **CNAME** target (e.g. `chimera-data-api.onrender.com`).
4. At your registrar, create:

   | Type | Name | Value |
   |------|------|--------|
   | CNAME | `api` | `….onrender.com` (from Render) |

5. **Environment** on Render:

   ```env
   CHIMERA_CORS_ORIGIN=https://chimerachess.co.uk
   ```

   Use your exact app URL (with `https`, no trailing slash). If you use both apex and `www`, set CORS to whichever URL users actually open, or add both if your host documents multiple origins. Use `*` only while testing.

6. Wait for **Verified** + SSL on Render.

### Railway

1. Service → **Settings** → **Networking** → **Custom Domain** → `api.chimerachess.co.uk`.
2. Add the CNAME Railway provides.
3. Set `CHIMERA_CORS_ORIGIN` and `CHIMERA_DATA_DIR` as in [DEPLOY-API.md](./DEPLOY-API.md).

---

## 4. DNS example (registrar panel)

If your registrar is Cloudflare / Namecheap and you own `chimerachess.co.uk`:

```
# App (Netlify example — use exact targets Netlify shows you)
@  (apex)          ALIAS or A     → Netlify
www                CNAME          → your-site.netlify.app

# API (Render example)
api                CNAME          → chimera-data-api.onrender.com
```

UK registrars sometimes use slightly different labels for the apex (`@` vs blank name) — copy what your panel shows.

Propagation can take 5 minutes–48 hours.

---

## 5. Verify

```bash
curl https://api.chimerachess.co.uk/api/chimera/health
```

Open `https://chimerachess.co.uk` → **Settings → Account** → register → **SERVER ONLINE**.

---

## 6. What we cannot do from code

- **Register** the domain for you — you purchase it at a registrar.
- Replace Render/Netlify default URLs until DNS is configured.

After you own `chimerachess.co.uk`, set `VITE_CHIMERA_API_URL` and `CHIMERA_CORS_ORIGIN` below and rebuild.

---

## Quick reference `.env` (production)

```env
# Build-time (Netlify/Vercel env vars)
VITE_CHIMERA_API_URL=https://api.chimerachess.co.uk

# Render/Railway server env
CHIMERA_CORS_ORIGIN=https://chimerachess.co.uk
```

Share with testers: **https://chimerachess.co.uk** (routes like `#train`, `#account` still work as hash links).
