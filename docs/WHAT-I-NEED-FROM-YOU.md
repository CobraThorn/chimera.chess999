# What you’d need to give me (and what I can’t do)

## Honest answer

I **cannot** log into GitHub, Netlify, or Render for you. Nobody should send passwords to an AI.  
**You** click “Sign in with GitHub” on those sites — I guide **which** buttons.

Your code is **not** only “inside Cursor.” It is a normal folder on your PC:

```
C:\Users\js130\ai
```

Double-click **`OPEN-PROJECT-FOLDER.bat`** in that folder (in Cursor’s file list) to open it in File Explorer.

---

## What I need from you (minimum)

| You give | Why |
|----------|-----|
| **Nothing secret** | No passwords, no API keys in chat |
| **GitHub account** | Free — you create it at github.com |
| **10 minutes clicking** | Render + Netlify both use “Login with GitHub” |
| **Screenshots if stuck** | e.g. “Connection failed”, red build log |

Optional: your **GitHub username** so instructions can use your real repo URL.

I do **not** need: bank details, domain registrar login (until DNS step), or remote access to your PC.

---

## What I can do from here (in Cursor)

- Fix code, docs, deploy configs (`render.yaml`, `netlify.toml`)
- Tell you **exact** clicks and commands
- Help read **error messages** you paste

What I **cannot** do:

- Press Deploy on your Netlify account
- Own your domain or DNS
- Run `git push` as **you** (that uses **your** GitHub login on **your** PC)

---

## Easiest path if Git commands scare you

### 1) Open the folder

- In Cursor left sidebar, find **`OPEN-PROJECT-FOLDER.bat`** → right‑click → **Reveal in File Explorer**  
  OR Win+E → paste `C:\Users\js130\ai` in the address bar.

### 2) Put code on GitHub **without** Git Bash

1. [github.com/new](https://github.com/new) → name **`chimerachess`** → Create repository.  
2. On the empty repo page: **Add file** → **Upload files**.  
3. Drag **everything** from `C:\Users\js130\ai` **except**:
   - `node_modules` (huge — skip it)
   - `dist` (optional skip — Netlify rebuilds it)
4. Commit.

Netlify/Render will run `npm install` and build on their servers.

### 3) Render (game server / online play)

1. [render.com](https://render.com) → Sign up with **GitHub**  
2. **New +** → **Blueprint** → select **chimerachess** → **Apply**  
3. Wait **Live** → copy URL → test `…/api/chimera/health`

### 4) Netlify (website)

1. [netlify.com](https://netlify.com) → Sign up with **GitHub**  
2. **Import** → **chimerachess**  
3. Environment variable: `VITE_CHIMERA_API_URL` = your Render URL  
4. Deploy → share the `*.netlify.app` link

That’s the live site + online bullet/blitz/rapid.

---

## “Website only” shortcut (no online humans)

If you only want the **look** of the site, not real online pairing:

1. On a PC that has the folder, run: `npm install` then `npm run build`  
2. [app.netlify.com/drop](https://app.netlify.com/drop) → drag the **`dist`** folder  

Online play **will not work** without Render (step 3 above).

---

## If `C:\Users\js130\ai` really doesn’t exist on your PC

Then Cursor may be using a **remote** workspace. Do this:

1. Cursor menu **File** → **Open Folder** — note the path it shows.  
2. Or **File** → **Save Workspace As** to `Documents\chimerachess`.  
3. Tell me that path in chat — we’ll use it instead.

---

## One message that unblocks everything

Reply with:

1. Did `C:\Users\js130\ai` open in File Explorer? **yes / no**  
2. Do you have a **GitHub** account? **yes / no**  
3. What do you want first: **full site + online play** or **just see the site**?

I’ll reply with **only your next 3 clicks**, no 80-step doc.
