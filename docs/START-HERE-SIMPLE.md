# Start here (simple version)

**“It’s all in Cursor / I have nothing on my PC”** → read **[WHAT-I-NEED-FROM-YOU.md](WHAT-I-NEED-FROM-YOU.md)**.  
Your files are in `C:\Users\js130\ai` — double‑click **`OPEN-PROJECT-FOLDER.bat`** in the project to open it.

You only need to understand **three things**:

1. **The website** (what testers open in Chrome) → Netlify
2. **The game server** (online bullet/blitz/rapid) → Render
3. **Your code** has to live on **GitHub** so Netlify and Render can pull it

You do **not** need to read the 80-step doc yet. Do **Path A** or **Path B** below.

---

## Path A — “I just want friends to try it today” (no domain, ~30 min)

No `chimerachess.co.uk` yet. Two free links is fine.

### A1. Put code on GitHub (10 min)

1. Install Git: [https://git-scm.com/download/win](https://git-scm.com/download/win)
2. Open **Git Bash** (blue icon).
3. **Go to the project folder** (prompt must end with `/ai`, not just `~`).

   Try **one** of these (first one that works):

```
cd ~/ai
```

```
cd C:/Users/js130/ai
```

```
cd "$USERPROFILE/ai"
```

   **Not in Cursor only?** The code is on your PC at `C:\Users\js130\ai`. In File Explorer, open that folder → click the address bar → copy path → in Git Bash: `cd` then paste.

   **Find it:** File Explorer → `This PC` → `Local Disk (C:)` → `Users` → `js130` → `ai`.

4. If you already ran `git init` at `~` (home) and saw AppData errors, run this once:

```
cd ~
rm -rf .git
cd /c/Users/js130/ai
```

5. Paste these lines **one at a time**:

```
git init
git add .
git commit -m "first upload"
```

6. Browser: [https://github.com/new](https://github.com/new) → name `chimerachess` → Create (empty repo)
7. Back in Git Bash (still in `/ai`), change YOUR_NAME:

```
git remote add origin https://github.com/YOUR_NAME/chimerachess.git
git branch -M main
git push -u origin main
```

GitHub will ask you to log in. When the page shows your files, **A1 is done**.

### A2. Game server on Render (10 min)

1. [https://render.com](https://render.com) → Sign up with **GitHub**
2. **New +** → **Blueprint** → pick **chimerachess** repo → **Apply**
3. Wait until it says **Live** (green)
4. Copy the URL (like `https://chimera-data-api-xxxx.onrender.com`)
5. Open that URL + `/api/chimera/health` in the browser — you should see `"ok": true`

**A2 done.** Leave Render open.

### A3. Website on Netlify (10 min)

1. [https://netlify.com](https://netlify.com) → Sign up with **GitHub**
2. **Add site** → **Import** → pick **chimerachess**
3. **Before** Deploy, click **Environment variables** → Add:
  - Name: `VITE_CHIMERA_API_URL`  
  - Value: paste your Render URL from A2 (starts with `https://`)
4. **Deploy** → wait → open the `something.netlify.app` link
5. **Play → Online → Blitz → Find game**
6. Open that **same link on your phone** → Blitz → Find game → you should get a match

**Share the Netlify link with friends.** That’s your tester URL for now.

---

## Path B — “I want chimerachess.co.uk”

Do **Path A first**. When A works, add DNS (the hard part is waiting, not thinking):

- Netlify tells you which DNS records to add  
- Render tells you one CNAME for `api`  
- Registrar = where you bought the domain (Namecheap, etc.)

Only then follow [GO-ONLINE-80-STEPS.md](GO-ONLINE-80-STEPS.md) **Part D (steps 61–80)**.

---

## Path C — “I’m not ready to deploy”

Play on your PC only:

```
npm run dev:full
```

Open [http://localhost:5173](http://localhost:5173) → two tabs → Play → Online → Blitz.

---

## When you’re stuck, answer these four questions

Copy this and fill in the blanks — that tells us the exact next click:

1. GitHub: Did `git push` work? **yes / no / didn’t try**
2. Render: Is the service **Live**? **yes / no / didn’t try**
3. Netlify: Did the build turn **Published**? **yes / no / didn’t try**
4. What do you see when you click **Play → Online**? **connection failed / searching / blank / other**

---

## Ignore for now

- 80-step doc (until Path A works)  
- Custom domain  
- Vercel, Railway, OpenAI keys  
- Commits, branches, CI

One path. One Netlify link. One Render URL. That’s enough for online play.