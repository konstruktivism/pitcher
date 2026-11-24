---
Date: 2026-11-24
Status: Draft
---

# Plan

A founder writes a deck as typed slides, presents it, and sends each recipient a personal link. One source serves presenting and reading. With consent: time per slide and drop-off. No editor in v1; publish checks the file. See [best practices](best-practices.md) and [research](research.md).

## 1. Deck

A folder in `DECKS_DIR`: `deck.yml`, images, videos, fonts.

```yaml
title: Acme
language: en                 # nl or en
outline: investor            # or sales
minutes: 10                  # talk length
theme: { color: "#1f6feb", font: fonts/Inter.woff2 }
slides:
  - type: title
    headline: Acme gets small shops paid the same day
  - type: problem
    headline: Small shops wait 30 days for their money
    points: [Cash flow stalls, Banks say no, Factoring costs 4%]
    detail: Survey of 120 shops in 2026; the median wait was 31 days.
    sources: [Acme shop survey 2026]
    seconds: 60
    notes: Open with the bakery story.
  - type: traction
    headline: Revenue grew 18% a month this year
    chart:
      kind: bar
      unit: EUR k
      period: month
      source: Billing export
      highlight: Mar
      points: [{ label: Jan, value: 12 }, { label: Feb, value: 14 }, { label: Mar, value: 17 }, { label: Apr, value: 20, forecast: true }]
```

### Schema

Strict: unknown fields fail. Text allows Markdown emphasis and links to `#/`, `https:` and `mailto:`. Never raw HTML.

| Field | Type | Rule |
|---|---|---|
| `type` | slide type | Required |
| `id` | kebab-case | Defaults to the type; required when a type repeats. Deep link and tracking key; never changes after publish |
| `headline` | text | Required. The claim, as a sentence |
| `detail` | text | Shown only when read alone |
| `sources` | list of text | Required on `market` |
| `seconds` | integer > 0 | Planned speaking time |
| `notes` | text | Team only |

| Block | Type |
|---|---|
| `points` | 1–5 lines |
| `figures` | 1–4 × `{label, value, note?}`, all text ("EUR 1.5m") |
| `chart` | `{kind: bar\|line, unit, period, source, points, cumulative?, highlight?}`; `points` is 2–24 × `{label, value: number, forecast?}` |
| `image` | `{src, alt}`; `src` is a file in the folder |
| `video` | `{src, poster, captions?}`: MP4, image, WebVTT |
| `people` | 1–6 × `{name, role, proof, photo?}` |

| Type | Purpose | Blocks |
|---|---|---|
| `title` | The company in one sentence | `image` |
| `problem` | Whose pain; how they cope now | `points` |
| `solution` | The insight, as one visual | `image` or `points` |
| `why-now` | What changed | `points` |
| `product` | How it works, in 3 steps | `points`, `image` or `video` |
| `traction` | Proof | `chart` or `figures` |
| `market` | Who buys, bottom-up | `figures` |
| `business-model` | Revenue and unit economics | `figures` |
| `competition` | Alternatives and their gaps | `points` |
| `team` | Why these people | `people` |
| `financials` | Metrics, plan, burn | `chart` or `figures` |
| `ask` | The ask, what it buys, a vision line | `amount`, `use_of_funds` (figures), `milestones` (points), `runway`, `next_step` |
| `appendix` | Risks, details | any |

**Outlines**
- `investor` (default, order above): requires `title`, `problem`, `solution`, `team`, and an `ask` with `amount`, `use_of_funds` and `milestones`.
- `sales` (Raskin's strategic narrative): `title`, `why-now` (the change), `problem` (the stakes), `solution` (the future), `product` (how), `traction` (proof), `ask` (`next_step`). Requires all but `title` and `product`.

### Design

- One Vue component per type; the design is fixed.
- On the 1280×720 canvas: headlines ≥ 48 px, body ≥ 28 px.
- Charts: `highlight` in the theme colour, the rest grey, values labelled.
- No animations, fragments or transitions.

### Publish

`deck:publish <folder>`. This list is normative.

Fails on:
- invalid YAML, an unknown field or type, a duplicate id;
- a type the outline requires but the deck lacks;
- a chart without `unit`, `period` or `source`; `market` without `sources`;
- an investor `ask` without `amount`, `use_of_funds` or `milestones`; a sales `ask` without `next_step`;
- an asset outside or missing from the folder;
- a link other than `#/`, `https:` or `mailto:`;
- an image without alt text;
- a theme colour below 4.5:1 contrast.

Warns on:
- more than 20 slides or 3 points;
- more than 30 words presented, or 80 in `detail`;
- a headline over 12 words, or a bare label;
- a `cumulative` chart, a `video` without captions, an investor `ask` without `runway`;
- `seconds` summing above `minutes` × 60.

Prints the headlines in order.

Stores an immutable `versions/<content hash>/`: `viewer.json` (no notes), `notes.json` (team), the assets and a manifest. Never `deck.yml`. One transaction then makes it the deck's current version, and every link shows it. Data, asset URLs and views carry the version id. An open view keeps its version until reload. Stats add up per slide id across versions.

## 2. Runtime

reveal.js (MIT, pinned version), client-only. Each slide is a `<section id="<slide id>">`.

Settings: `hash: true`, progress bar, slide number, `controlsLayout: 'edges'`, overview (`O`), blackout (`B`), `transition: 'none'`, `scrollActivationWidth: null`.

Viewers:
- Keys, swipe, edge click zones, deep links, progress, counter, screen-reader announcement.
- From 640 px: a 1280×900 canvas, with `detail` in a strip under the slide.
- Below 640 px: a read layout instead of reveal. Same components as a responsive document, body ≥ 16 px, `detail` under each slide. reveal's own scroll view would scale 28 px text down to about 9 px.

**Present** at `/present/<deck>` (team only):
- Current and next slide, notes, elapsed and remaining time, planned vs spent time per slide.
- Clicker (PageUp/PageDown, arrows). Timer start, pause, reset.
- The overview previews a slide; Enter puts it on screen.
- After a run: planned vs spent per slide.
- `/present/<deck>/screen` shows only the slides; share this window on a video call. It syncs over `BroadcastChannel`:
  - the run id is in both URLs, with one pinned version;
  - the screen announces itself and gets the full state (run, version, slide, blackout); every change resends it;
  - the presenter keeps its state in `sessionStorage` and restores it before broadcasting;
  - a heartbeat every 2 s gets an answer; after 5 s without one, the presenter shows the screen as lost.
- Neither window shows `detail`.
- reveal's speaker view is not used: its popup runs inline scripts that the CSP blocks.
- Before a pitch, save two PDFs (the presentation, and the read version with `detail`) and open both offline once. ⇒ ALWAYS have an offline copy.

**PDF:** with `allow_pdf`, a button opens `view: 'print'` after the gate, once assets have loaded. It includes `detail`, never notes. ⇒ NEVER promise copy protection.

## 3. Share

**Links**
- `open` (default): the only gate is the consent choice.
- `code`: a 6-digit code goes to the link's address. The page shows a masked hint and "Not you? Ask the sender for your own link".
- A link without a recipient is a broadcast, always `open`.
- Slug: ≥ 128 random bits. Fields: recipient, `internal`, `allow_pdf` (off), expiry.
- One active link per recipient per deck: a unique index on deck + normalised address (trimmed, lower case), over links not revoked or archived.
- Revoke, archive, replace; never hard-delete.
- A revoked, expired or archived link gives no code, no verification, no grant and no content. Page: "This link is no longer active. Ask the sender for a new one."

**Gate and grants**
- First open per browser: two equal buttons, "Open with reading stats" and "Open without".
- A grant is a database row. A sealed `__Host-` cookie holds only grant ids (Secure, HttpOnly, SameSite=Lax, ≤ 20 grants, 30 days). The server checks the row on every request, so revocation and withdrawn consent beat an old cookie.
- Consent lives on the grant; the viewer can change it from the deck.
- A valid grant skips the code. Links don't log each other out.
- Every POST checks `Origin`.

**Admin** (team; publishing stays a command)
- Decks: current version, publish date.
- Deck: versions, links, time and drop-off per slide.
- New link: recipient, mode, expiry, `internal`, `allow_pdf`.
- Link: copy invite, revoke, archive, replace.
- Recipient: links by address, sessions, slide times, erase.

**Copy invite:** the link plus one sentence in the deck language; the founder sends it. ⇒ ALWAYS a system-generated link.

## 4. Measure

Only with consent. Without it, only the grant and the limit counters exist: no view, no time, no alert.

- **View:** one per page load, so one per tab, created by `start.post` and bound to the grant. A GET never counts: scanners and link previews fetch links.
- **Tracker:** visible time per slide (Page Visibility API; IntersectionObserver in the read layout). Pauses after 60 s idle unless a video plays. `sendBeacon` every 10 s, on slide change and on hide, with the view id, cumulative ms per slide and the furthest slide.
- **Server:**
  - accepts a beacon only if the view's grant is in the cookie, the grant is active with consent, the link is active and the view is open;
  - takes link, viewer and version from the view, never from the body;
  - refuses unknown slide ids and negative or fractional values;
  - processes each beacon in one transaction and keeps the max per slide;
  - caps a view's total at the time since `started_at` + 2 s, and drops the excess.
- **Withdrawal:** consent off, open views closed. Consenting again starts a new view.
- **Not counted:** bot user agents (flagged and kept), team sessions, `internal` links.
- **Stats** per link and recipient: sessions (a grant's views until 30 min pass without a beacon), time per slide, furthest slide, browsers (grants with consent), device class. Labelled as estimates; browsers are not people.
- **Alert** to the link's creator, at most one per view: on the first view of the link, of a new session, or of a new browser (a grant's first view while an earlier grant has views: forwarded, or cookies cleared).

## 5. Security

- The server gates every byte: deck data, images, video.
- `deck.get` returns the current version id and `viewer.json`, and needs a grant. `notes.json` is team only.
- Files at `/api/d/<slug>/<version>/<file>`:
  - manifest entries only;
  - the version must belong to the link's deck;
  - the path resolves inside the version folder (no `..`, no symlinks);
  - then `X-Accel-Redirect` to an nginx `internal` location, which handles Range.
- ⇒ NEVER deck files in `public/`; NEVER a client-side overlay as the gate.
- Gated responses: `Cache-Control: no-store`, `Vary: Cookie`, `Referrer-Policy: no-referrer`, `X-Robots-Tag: noindex`.
- CSP on every page, via `nuxt-security`:

  ```
  default-src 'none'; script-src 'self' 'nonce-{{nonce}}' 'strict-dynamic'; style-src 'self' 'unsafe-inline';
  img-src 'self'; font-src 'self'; media-src 'self'; connect-src 'self';
  frame-ancestors 'none'; base-uri 'none'; form-action 'self'
  ```
- The gate page of a `code` link has a neutral `og:title`.
- Codes: CSPRNG, 6 digits, 10 minutes, single use, hashed, consumed atomically. Bound to their purpose and link or account; a new code replaces the old.
- Limits, for viewer and team codes alike:
  - 5 wrong tries per link or account per 15 min, not reset by a new code;
  - sends: 3 per 15 min and 10 per day per recipient, 20 per hour per IP.

  Counters are database rows keyed by hash, updated atomically, expiring with their window.
- Team login: an email code, an allow-list, purpose `team`. Admin APIs and `/present` need an active team user; a viewer grant never opens them. Escape all admin output.

## 6. Mail

- Three mails: the access code (only to the link's address), the team code, the visit alert.
- A sending subdomain with SPF, DKIM and DMARC. An EU provider, such as Mailcoach, Scaleway TEM, Lettermint, Mailgun EU or TransIP SMTP.
- No click or open tracking, no remote images. A plain-text part, and a monitored Reply-To.
- Codes, not magic links: scanners open links first.
- Missing mail config in production is an error. Escape user text.

## 7. Privacy (NL)

- The gate text names: the controller, what is logged with and without stats, the purpose, retention, rights and contact. It links to the privacy page.
- Measuring per viewer needs consent; withdrawal stops it. This is a conservative reading of EDPB 2/2023, not legal advice.
- No IP addresses, except an expiring hash in limit counters. The user agent becomes a device class and a bot flag. No IP, slug, email address or code in any log; no nginx access log for `/d/` and `/api/`.
- Retention:
  - views and slide times: 12 months;
  - a daily job deletes those, plus used or expired codes and counters;
  - logs and backups rotate within 30 days;
  - the mail provider keeps logs as briefly as it allows.
- Erasure: revoke the recipient's links and grants; delete their codes, views and slide times; blank the recipient fields. A soft delete is not an erasure.

## 8. Nuxt setup (example)

- Nuxt 4 (Node) behind nginx, on one EU VPS.
- SQLite via Drizzle and `better-sqlite3`, with WAL and tested backups. Postgres only once there is a second instance.
- `nuxt-auth-utils` (`runtimeConfig.session.name: '__Host-pitcher'`), `nuxt-security`, `nodemailer` (Mailpit locally), Zod.

```
app/components/slides/                  one component per slide type
app/pages/d/[slug].vue                  gate, then the deck or the read layout
app/pages/present/[deck]/index.vue      presenter
app/pages/present/[deck]/screen.vue     audience window
app/pages/login.vue, admin/             team screens; the API decides access
server/api/d/[slug]/deck.get.ts         version id and viewer.json; needs a grant
server/api/d/[slug]/[version]/[...file].get.ts   manifest and grant check, X-Accel-Redirect
server/api/d/[slug]/{code,verify,start,consent}.post.ts
server/api/views/[id].post.ts           beacon
server/api/present/[deck].get.ts        viewer.json and notes.json; team only
server/api/admin/...
server/utils/                           db, gate, mail, limits, deck schema
scripts/deck-publish.ts                 lint, headline outline, snapshot
examples/demo-deck/deck.yml             non-confidential demo deck
```

`→` marks a foreign key, and `?` a nullable column.

| Table | Columns |
|---|---|
| `users` | `id`, `email` (unique), `active`, `created_at` |
| `decks` | `id`, `folder` (unique), `title`, `current_version_id`? → deck_versions, `archived_at`? |
| `deck_versions` | `id` (content hash), `deck_id` → decks, `slide_ids` (JSON), `published_by` → users, `published_at` |
| `links` | `id`, `deck_id` → decks, `slug` (unique), `mode`, `recipient_email`?, `recipient_key`?, `internal`, `allow_pdf`, `expires_at`?, `revoked_at`?, `archived_at`?, `created_by` → users, `created_at`. Partial unique index on (`deck_id`, `recipient_key`) where `revoked_at` and `archived_at` are null |
| `grants` | `id` (random), `link_id` → links, `consent`, `created_at`, `expires_at`, `revoked_at`? |
| `codes` | `id`, `purpose`, `link_id`? → links, `user_id`? → users, `code_hash`, `expires_at`, `used_at`? |
| `views` | `id` (random), `grant_id` → grants, `link_id` → links, `version_id` → deck_versions, `started_at`, `last_beacon_at`, `closed_at`?, `furthest_index`, `device_class`, `is_bot`, `alert_kind`?, `alerted_at`? |
| `slide_times` | `view_id` → views, `slide_id`, `ms`; primary key (`view_id`, `slide_id`) |
| `attempts` | `key_hash`, `kind`, `window_start`, `count`; primary key (`key_hash`, `kind`, `window_start`) |

## Proof

Use a fixed clock (`page.clock` in the browser) and Playwright's own headless Chromium.

- **P1 Publish:** fails on an unknown type or field, a duplicate id, a missing `ask`, a chart without a source, an asset from outside the folder, a `javascript:` link, missing alt text, and a low-contrast colour. Warns at 21 slides, on a label headline, and when `seconds` exceed `minutes` × 60. Prints the outline. The version folder has no `deck.yml`; `viewer.json` has no notes.
- **P2 Deep link:** `#/team` survives the gate and opens the team slide, in the deck and in the read layout.
- **P3 Layout:**
  - by keyboard only, page through, open and close the overview, use blackout, reach the end;
  - at 320–430 px: the read layout, body ≥ 16 px, `detail` visible, no horizontal scroll at 200% zoom;
  - no demo slide overflows at 1280×720 or 1280×900.
- **P4 Gating:**
  - no grant: 403 on the deck API and assets, also for a guessed path, `../` and a symlink;
  - valid grant: still 403 on `deck.yml`, `notes.json`, files outside the manifest and another deck's version;
  - no notes in the deck API or the PDF;
  - a direct request to the nginx internal location fails;
  - Range on a video works.
- **P5 Links:**
  - revoked, expired and archived links refuse codes, verification, grants and content, also with an old grant;
  - a grant for A doesn't open B;
  - two links in one browser both stay open;
  - a cross-origin POST is refused.
- **P6 Codes** (viewer and team):
  - 5 wrong tries block; a new code doesn't reset the count, and replaces the old one;
  - a code expires after 10 minutes, and a used code fails;
  - the 11th send in a day is refused, and so is the 21st in an hour from one IP.
- **P7 Views:**
  - a GET creates none;
  - a start with consent creates exactly one, and two tabs create two;
  - without consent there is no view and no alert.
- **P8 Tracking:**
  - without consent, no tracker and beacons refused;
  - browser B can't write to A's view;
  - an unknown slide id is refused;
  - 100 rapid beacons can't push a total past the elapsed time + 2 s;
  - after withdrawal, beacons are refused, even with a restored old cookie.
- **P9 Present:**
  - 403 without a team session;
  - the screen follows, recovers after either window reloads, and loss is shown;
  - both team PDFs render every slide with its fonts.
- **P10 Headers:**
  - the four headers and the CSP are present;
  - present, screen, read layout and print all work under the CSP;
  - a script without a nonce, and a cross-origin fetch, are blocked, while Nuxt chunks load.
- **P11 Mail:**
  - the code goes only to the link's address;
  - production without mail config fails loudly;
  - `<script>` in a name shows as text in the admin.
- **P12 Erasure:** revokes links and grants, and deletes codes, views and slide times. The daily job deletes data older than 12 months, and expired codes.
- **P13 End to end:**
  1. Open the demo deck with stats.
  2. Spend 5, 3 and 2 s on slides 1–3, then hide the tab.

  The admin shows 5, 3 and 2 s (±1), with slide 3 as the furthest.

## Choices per startup

- Domain: `deck.<domain>`.
- Mail provider and sending subdomain.
- Retention: 12 months proposed.
- Hosting: an EU VPS, for example via Forge or Ploi.
- Logging opens without consent: only after advice from a privacy lawyer. It changes Measure and Privacy.
- Existing decks: convert them to `deck.yml`. An agent can do it from a PDF; the founder checks every headline and number.
