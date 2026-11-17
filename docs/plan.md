---
Datum: 2026-11-17
Status: Concept
---

# Plan

A startup founder sends a deck to one person, or posts one public link, and later sees: did they open it, which slides, for how long, and did they come back. v1 does that and nothing more. Background and sources: [research report](reports/Pitchdecks%20delen%20en%20volgen.md).

## v1

1. **Decks.** One folder per deck in `DECKS_DIR`, outside the repo: `index.html`, assets, and `deck.json` with `title` and `language` (`nl`/`en`). Every change becomes a new immutable version: HTML and assets are copied to a folder named by content hash, and a visit stays on the version it started on. A slide is a `.slide` element; the visible one has `.active`; each slide carries a `data-slide-id` that survives edits. A page without `.slide` (an A4 sheet) is one slide.
2. **Links.** Random slug of at least 128 bits. `mode`: `public` or `email`. An optional recipient (name and email; required for `email`), and an `internal` flag for test links. Expiry is optional; revoke and archive, never hard-delete. One active link per recipient per deck: asking for a second returns the first, because two links split the stats. A revoked link may be replaced, and the recipient view adds up both.
3. **Gate, on the server.** Public: a gate page with the notice and an "Open deck" button. Email: "Send code" goes to the address on the link (the page shows only a masked hint), and the recipient types a 6-digit code. A passed gate adds a grant to the session; one session holds grants for several links.
4. **Viewer.** The server injects one shared tracker script into the deck HTML. Decks carry no tracking or gate code of their own.
5. **Team.** Own accounts from an allow-list of addresses, login by email code (bound to the purpose `team`, with the same limits). Every admin API requires an active allow-listed user; a viewer grant in the same session never opens the admin.
6. **Admin.** Decks and versions; links (create, copy invite text, revoke, extend); per link the visits, slides reached, time per slide and last seen; per recipient all of their links.
7. **Mail.** The access code, and a first-open alert to whoever created the link.

**Never:** shared passwords or PINs, tracking pixels in mail, provider click tracking, AI typing links or sending invites, third-party scripts or fonts on a deck, or "screenshot protection".

## Nuxt setup (example)

Nuxt 4 with Nitro as a Node server on one small EU VPS. SQLite through Drizzle and `better-sqlite3` on persistent disk, in WAL mode, with a tested backup (one instance; move to Postgres only when there is a second one). A sealed session cookie via `nuxt-auth-utils`, and `nodemailer` to an EU SMTP provider. Locally, a mail catcher such as Mailpit.

```
app/pages/
  open/[slug].vue                   gate: notice, consent choice, code form
  login.vue                         team login by code
  admin/...                         team screens (UX only; the API decides)
server/
  routes/d/[slug]/[...path].get.ts  gated deck files; injects the tracker into index.html
  api/open/[slug]/code.post.ts      send code
  api/open/[slug]/verify.post.ts    check code, add grant
  api/open/[slug]/start.post.ts     "Open deck": creates the visit (this is the open)
  api/visits/[id]/events.post.ts    tracker beacons
  api/admin/...                     team CRUD and stats
  utils/                            db, gate, mail, limits
  db/schema.ts, db/migrations/
public/tracker.js                   the only tracking script on a deck (not secret)
examples/demo-deck/                 non-confidential deck for tests
```

Tables: `users`, `decks`, `deck_versions`, `links`, `codes`, `visits`, `slide_events`, `attempts`.

`/d/{slug}` without a grant redirects to `/open/{slug}`. The deck is served at `/d/{slug}/` with a trailing slash, so relative asset paths resolve under the gate. Grants sit in the sealed cookie (`runtimeConfig.session.name: '__Host-pitcher'`), with at most 20 kept to stay under the 4 KB cookie limit. Nitro serves `public/` before any middleware, and H3 does not handle video Range requests for you: the gated route answers 206 and 416 itself.

## Rules

### Access

- The server gates every byte: HTML, images, and video including Range requests. ⇒ NEVER a deck file in `public/`, NEVER a client-side overlay as the gate.
- Every request rechecks the link (revoked, expired) and the grant. No exceptions for a Host header or a "preview" route; the team previews through its own session.
- Every gated response carries `Cache-Control: no-store`, `Vary: Cookie`, `Referrer-Policy: no-referrer` and `X-Robots-Tag: noindex`. The gate page of an email link uses a neutral `og:title`, because chat apps unfurl links.
- The session cookie is `__Host-`, Secure, HttpOnly and SameSite=Lax, and holds a list of grants, so opening a second link never logs out the first.
- Codes come from a CSPRNG: 6 digits, valid 10 minutes, single use and atomically consumed, stored hashed. 5 wrong tries per link per window, and a new code does not reset the count. Sends are limited per recipient (3 per 15 minutes) and per IP. Counters live in the database, keyed by a hash, and expire with their window.
- Escape everything the admin renders; the tracker sends typed, size-capped fields.

### Tracking

- A visit starts only with a POST: the "Open deck" button, a correct code, or, for a returning viewer with a grant, the tracker's first call. A GET of the link never counts, because mail scanners and link previews fetch links. Within 30 minutes of inactivity it is the same visit; after that, a new one.
- The tracker watches `.slide.active` and counts visible time only (Page Visibility API). It pauses after 60 seconds without input, unless a video is playing. It flushes every 10 seconds, on each slide change and on `visibilitychange: hidden`, using `sendBeacon`. Never `unload` or `beforeunload`.
- The server takes link, viewer and version from the visit, never from the request body. It deduplicates by event id and caps credited time at the elapsed server time.
- Visits from known bot user agents, bursts and visits under 3 seconds without input are flagged, kept, and left out of totals and alerts.
- Each visit is pinned to a deck version; stats are per version and per slide id, never by position alone.
- Team sessions and `internal` links never count. The admin labels the numbers as estimates.
- The first-open alert fires only on a qualified open: a clicked button or a passed code, not from a bot user agent. "Open without stats" still logs that open, but no slide time.

### Mail

- v1 sends two mails: the access code (only to the address on the link) and the first-open alert (to the link's creator). Invitations go out from the founder's own mailbox: "Copy invite" gives the link plus one sentence in the deck language. ⇒ ALWAYS a link generated by the system, NEVER one typed by a person or an AI.
- Send from a dedicated subdomain with SPF, DKIM and DMARC, through a provider that signs with DKIM on your domain and keeps data in the EU, for example Mailcoach, Scaleway TEM, Lettermint, Mailgun EU, or the SMTP of a Dutch host such as TransIP. Turn click and open tracking off. Include a plain-text part, no remote images, and a monitored Reply-To.
- The mail carries a code, not a magic link, because scanners open links before people do.
- Missing mail configuration in production is an error, never a console log of the code. User text in a mail is escaped. The mail language follows the deck.

### Privacy (NL)

- The gate says who is responsible, what is logged and why, how long it is kept, and the viewer's rights and contact address, and links to the privacy page.
- The viewer decides: time per slide is recorded only after an active choice at the gate, through two equal buttons, "Open with reading stats" and "Open without". Without it, only the open is logged. The choice is stored with the visit, and the viewer can withdraw it from the deck. This is a conservative reading of EDPB 2/2023; there is no ruling on deck tracking.
- No IP address is stored, except as a keyed hash in a limit counter that expires with its window. The user agent becomes a device class and a bot flag on arrival.
- Retention: visits and events 12 months, codes until used or expired, removed by a daily job. Erasure deletes that recipient's visits and events and blanks the recipient fields; the link stays archived. A soft delete is not an erasure, so here privacy law wins over any soft-delete policy.
- Fonts, scripts and video on decks are self-hosted.

## Proof

Tests per piece, with a fixed clock:

- A deck file or asset without a grant redirects to the gate or returns 403, also for a guessed path and `../`.
- A revoked or expired link with an old grant shows the gate.
- A grant for link A does not open link B; two links in one browser both stay open.
- 5 wrong codes block; a new code does not reset the count; a code expires after 10 minutes.
- A GET of the link creates no visit, Open creates exactly one, and a reload within 30 minutes stays the same visit.
- A tracker event for another visit or link is refused; a replayed event counts once; durations are capped.
- Without consent, no slide event is accepted.
- Gated responses carry the four headers.
- The code mail goes only to the link address; production without mail configuration fails loudly.
- A `<script>` in a name or user agent shows as text in the admin.
- Browser (Playwright, headless Chromium): open the demo deck, go through 3 slides, hide the tab, and see 3 slide rows with time.

## Choices per startup

- **Domain:** a subdomain of the startup's own domain (`deck.<domain>`), because recipients trust it.
- **Mail provider and sending subdomain:** see Mail.
- **Retention** (12 months proposed) and **hosting** (an EU VPS, for example through Forge or Ploi).
- **Existing decks:** strip their own gate and tracking code, make asset paths relative, self-host the fonts, and add `data-slide-id`.
