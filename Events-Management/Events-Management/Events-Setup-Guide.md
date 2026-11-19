# Event Management — Local Setup Guide (Windows)

A full-stack MERN app (MongoDB, Express, React/Next.js, Node). Follow these steps in order — don't skip any.

---

## 1. Install Node.js

Download and install Node.js 20+ from: https://nodejs.org (choose the **LTS** version)

Verify it installed by opening PowerShell and running:
```
node -v
```
You should see a version number like `v20.x.x`.

---

## 2. Install pnpm

In PowerShell, run:
```
npm install -g pnpm
```

---

## 3. Install MongoDB Community Server

1. Download the installer: https://www.mongodb.com/try/download/community
2. Run the installer.
3. When prompted, **keep the default option checked** to "Install MongoDB as a Service." This means MongoDB will run automatically in the background — you never need to manually start it.
4. Finish the install.

### Verify MongoDB is running
Open PowerShell and run:
```
Get-Service MongoDB
```
It should show `Status: Running`. If it says `Stopped`, run:
```
Start-Service MongoDB
```
(Run PowerShell "as Administrator" if that gives a permission error.)

---

## 4. Get the project folder

1. Download/unzip the project.
2. **Important**: unzipping sometimes creates a folder *inside* another folder with the same name (e.g. `shatha-events-master\shatha-events-master`). Open the folder and check — if there's a subfolder with the same name that directly contains `apps`, `packages`, `package.json`, etc., that inner folder is the **real project root**. Always work from that inner folder.

To find and open it in PowerShell:
```
cd path\to\shatha-events-master
dir
```
If you see another `shatha-events-master` folder listed, go one level deeper:
```
cd shatha-events-master
dir
```
Keep checking `dir` until you see `apps`, `packages`, `package.json`, `pnpm-workspace.yaml`, `README.md` directly listed. **Stay in this exact folder for every command below.**

---

## 5. Install dependencies

From the project root folder:
```
pnpm install
```
This downloads all required packages — takes a minute or two.

---

## 6. Set up environment files

### 6a. Copy the example files
```
copy apps\api\.env.example apps\api\.env
copy apps\web\.env.local.example apps\web\.env.local
```

### 6b. Generate two JWT secrets
Run this command **twice** — each run gives you a different random string:
```
node -e "console.log(require('crypto').randomBytes(48).toString('hex'))"
```
Copy both outputs somewhere safe (e.g. Notepad) before continuing.

### 6c. Edit `apps\api\.env`
Open it:
```
notepad apps\api\.env
```
Delete everything in the file and replace it with the following, pasting your two generated strings into the `JWT_ACCESS_SECRET` and `JWT_REFRESH_SECRET` lines:

```
MONGODB_URI=mongodb://127.0.0.1:27017/shatha_events
JWT_ACCESS_SECRET=<paste your first generated string here>
JWT_REFRESH_SECRET=<paste your second generated string here>
ACCESS_TOKEN_TTL=15m
REFRESH_TOKEN_TTL_DAYS=7
PORT=4000
NODE_ENV=development
CLIENT_URL=http://localhost:3000
STORAGE_DRIVER=local
CLOUDINARY_CLOUD_NAME=
CLOUDINARY_API_KEY=
CLOUDINARY_API_SECRET=
EMAIL_DRIVER=console
RESEND_API_KEY=
GMAIL_APP_PASSWORD=
```

**Important**: do NOT include `GMAIL_USER=` or `CONTACT_EMAIL=` lines at all (not even empty) — the app rejects an empty value for those fields. Just leave them out entirely; the app falls back to safe defaults.

Save the file (Ctrl+S) and close Notepad.

### 6d. Verify
```
type apps\api\.env
```
Check that:
- `MONGODB_URI`, `JWT_ACCESS_SECRET`, `JWT_REFRESH_SECRET` are filled in (no `<...>` placeholders left)
- There is no `GMAIL_USER` or `CONTACT_EMAIL` line at all

`apps\web\.env.local` needs no editing — its defaults are already correct for local use.

---

## 7. Seed demo data (optional but recommended)

```
pnpm --filter @app/api seed
```
This creates ready-to-use demo accounts. If it fails, re-check the `.env` file from step 6.

---

## 8. Run the app

```
pnpm dev
```
This starts both the backend (port 4000) and frontend (port 3000) together. **Leave this terminal window open** — closing it stops the app.

---

## 9. Open it in your browser

Go to: **http://localhost:3000**

Log in with any of these seeded accounts (password for all: `Password123!`):

| Role | Email |
|---|---|
| Admin | admin@eventmanagement.com |
| Vendor | amelia@decorco.example |
| Vendor | noah@lensandlight.example |
| Client | elena@example.com |
| Client | james@example.com |

---

## Common mistakes to avoid

- **Wrong folder**: Always run commands from the inner project folder (the one directly containing `apps`, `package.json`). Check with `dir` before running anything if unsure.
- **New terminal window**: If you close and reopen PowerShell, you must `cd` back into the project folder every time before running `pnpm` commands.
- **Empty env values**: Lines like `GMAIL_USER=` with nothing after the `=` will cause errors — delete the whole line instead of leaving it blank.
- **Editing the wrong `.env`**: There are two — `apps\api\.env` (backend, most settings) and `apps\web\.env.local` (frontend, rarely needs changes).

---

## Quick command reference

| Command | What it does |
|---|---|
| `pnpm install` | Install all dependencies |
| `pnpm --filter @app/api seed` | Add demo data |
| `pnpm dev` | Run the app (API + web together) |
| `Get-Service MongoDB` | Check if MongoDB is running |
| `Start-Service MongoDB` | Start MongoDB if stopped |
