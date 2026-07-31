# Jar of Friendship

A single-page app where you write "open when…" notes for a friend, tie up the jar,
and send them one link. Notes are stored in Firebase Realtime Database; there is no
sign-up, no accounts, and no server code.

```
index.html           the entire app (HTML + CSS + JS, no build step)
og.png               1200×630 link-preview image
icon-180.png         home-screen icon
_headers             Cloudflare Pages caching + security headers
_redirects           serves index.html for any path
robots.txt           keeps jar links out of search results
database.rules.json  Firebase security rules  ← must be deployed separately
firebase.json        so `firebase deploy --only database` picks up the rules
```

---

## 1. Deploy the database rules first

**This matters more than the hosting.** Until you do this, anyone can read, overwrite,
or delete every jar in the database.

The rules allow exactly one thing: creating a jar at an ID that doesn't exist yet, and
reading a jar if you know its 8-character ID. Nobody can list all jars, edit an existing
jar, or delete one.

**Console (fastest):** Firebase Console → your `jar-of-letters` project → Realtime
Database → **Rules** tab → paste the contents of `database.rules.json` → **Publish**.

**CLI:**

```bash
npm i -g firebase-tools
firebase login
firebase deploy --only database
```

Verify with the **Rules Playground** in the console:

| Simulated operation | Location | Expected |
|---|---|---|
| read | `/jars` | **denied** (no listing) |
| read | `/jars/abcd1234` | allowed |
| write | `/jars/abcd1234` (new, valid data) | allowed |
| write | `/jars/abcd1234` (existing) | **denied** (write-once) |

---

## 2. Deploy the site to Cloudflare Pages via GitHub

1. **Push these files to your repo.** Put everything in this folder at the repo root
   (or in a folder like `public/` and note the name for step 4).

   ```bash
   git add .
   git commit -m "Friendship Day edition, launch ready"
   git push
   ```

2. **Cloudflare dashboard** → **Workers & Pages** → **Create** → **Pages** →
   **Connect to Git**.

3. Authorise GitHub and pick the repository. Production branch: `main`.

4. **Build settings** — there is no build step, so leave the framework preset as
   **None** and the build command **empty**:

   | Field | Value |
   |---|---|
   | Framework preset | None |
   | Build command | *(leave blank)* |
   | Build output directory | `/` &nbsp; *(or `public` if you nested the files)* |

5. **Save and Deploy.** You'll get `https://<project-name>.pages.dev` in about a minute.
   Every push to `main` redeploys automatically.

### If your project name isn't `jar-of-friendship`

The link-preview tags in `index.html` contain absolute URLs, and social apps need them
to match your real domain. Find-and-replace this one string:

```
https://jar-of-friendship.pages.dev
```

…with your actual domain, in `index.html` (4 places), `robots.txt`, and `sitemap.txt`.
Then push again.

### Custom domain (optional)

Pages → your project → **Custom domains** → add the domain. If the domain is already on
Cloudflare, DNS is set up for you; SSL takes a few minutes. Re-run the find-and-replace
above with the new domain.

---

## 3. Before you announce it

Run through `LAUNCH-CHECKLIST.md`.

---

## How it works

- **Storage** — one write per jar at `jars/<8-char-id>`. IDs use a 54-character alphabet,
  so there are ~7×10¹³ possibilities; they are unguessable in practice but not secret.
  Treat a jar link like a shared Google Doc link.
- **Nothing is editable.** Once a jar is tied, it's immutable. That's a deliberate product
  decision *and* what makes the security rules simple.
- **Drafts** live in `localStorage` for 14 days, so closing the tab mid-writing doesn't
  lose anything. They're cleared once the jar saves successfully.
- **Which notes have been opened** is also `localStorage`, per jar — so it's per device,
  and the sender can't see what's been read. That's intentional.
- **If Firebase is unreachable**, the app shows an error with a retry instead of handing
  out a link that would never load. The one exception is opening `index.html` directly
  from disk (`file://`), where it falls back to `localStorage` so you can click through
  the flow offline.

## Costs

Firebase's free Spark plan covers 1 GB stored and 10 GB/month transferred. A jar with
12 notes is roughly 3 KB, so the free tier holds hundreds of thousands of jars.
Cloudflare Pages is free for unlimited static requests.
