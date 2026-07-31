# Launch checklist — Friendship Day, Sunday 2 August 2026

Work top to bottom. The first section is the one that can actually hurt you.

## Must do before sharing the link

- [ ] **Publish `database.rules.json`** to Firebase. Until this is done, anyone can
      overwrite or delete any jar. Check the Rules Playground cases in `README.md`.
- [ ] Confirm the Realtime Database is in the **same project** as the config in
      `index.html` (`jar-of-letters`, `asia-southeast1`-style URL must match
      `databaseURL`).
- [ ] **Create one real jar end to end** on the live URL: write 3 notes, tie it, copy the
      link, open it in a **private window on a different device**, untie a note.
      This is the only test that proves hosting + database + rules all agree.
- [ ] Replace `https://jar-of-friendship.pages.dev` everywhere if your domain differs
      (`index.html` ×4, `robots.txt`, `sitemap.txt`).
- [ ] Paste your live URL into a WhatsApp chat with yourself and confirm the preview card
      shows the jar image and title. If it's blank, the `og:image` URL doesn't match your
      real domain.

## Worth doing

- [ ] Open on a real phone, not just a resized browser — check the writing screen with the
      keyboard up, and that the note text is comfortable to read.
- [ ] Test on Safari/iOS specifically: the share sheet uses `navigator.share`, which
      behaves differently there than on Android Chrome.
- [ ] Try a very long note (near the 1200-character limit) and confirm the opened-note card
      scrolls rather than overflowing.
- [ ] Try a name with an emoji or an apostrophe — everything is escaped, but see it once.
- [ ] Load the page with the network throttled to slow 3G. The fonts are the heaviest thing;
      text stays readable while they load.

## Known limits, by design

- **A jar can't be edited or deleted after tying.** If someone makes a mistake, they make a
  new jar. There is no delete path and no admin screen.
- **You can't see who opened what.** Open state is stored on the recipient's device only.
- **Anyone with the link can read the jar.** The 8-character ID is unguessable, not secret.
- **No abuse controls beyond the length limits.** If it gets popular enough to attract
  spam, the next step is Firebase App Check.

## If something breaks on the day

- **"Couldn't tie the jar"** — the database rejected the write. Check the Rules tab is
  published, and look at the browser console for the exact Firebase error.
- **Recipients see "This jar isn't here"** — the jar never saved (rules), or the link got
  truncated by the messaging app. Test the full link length in WhatsApp.
- **Link preview is blank** — `og:image` doesn't resolve. It must be an absolute URL on the
  same domain, and the image must be publicly reachable.
- **Rolling back** — Cloudflare Pages keeps every deployment. Pages → Deployments →
  find the last good one → **Rollback**.
