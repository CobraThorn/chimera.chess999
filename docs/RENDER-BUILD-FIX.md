# Render build failed ("Exited with status 1")

## Quick fix on GitHub

Render needs these files in **CobraThorn/chimerachess**. If any are missing, upload them:

- `package.json`
- `package-lock.json` (if missing, that's OK after the render.yaml fix)
- `render.yaml` (updated — see below)
- `server/` (whole folder)
- `scripts/` (whole folder)

## Update `render.yaml` on GitHub

1. Open https://github.com/CobraThorn/chimerachess/blob/main/render.yaml  
2. Click the pencil (Edit)  
3. Find the line `buildCommand:` and set it to:

   ```
   npm install --omit=dev --ignore-scripts
   ```

4. **Commit changes**

Or upload the new `render.yaml` from your PC (`C:\Users\js130\ai\render.yaml`).

## Redeploy on Render

1. [dashboard.render.com](https://dashboard.render.com) → your service  
2. **Manual Deploy** → **Deploy latest commit**  
3. Wait until **Live**

## Test

Open: `https://chimerachess.onrender.com/api/chimera/health`  
(JSON with `"ok": true` — first load may be slow on free tier.)

## If it still fails

Render → your service → **Logs** → copy the **last 20 lines** of the **Build** log (not Runtime) and paste into Cursor chat.
