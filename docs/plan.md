---
Datum: 2026-11-24
Status: Concept
---

# Plan

A founder writes a deck from typed slides, presents it live, and sends a personal link to each investor, customer or partner. One source serves both uses: the deck presents well and also reads well alone. With the viewer's consent, the founder later sees per slide what was read and where the reader stopped. There is no editor UI in v1: a deck is one file that a person or an AI agent writes, and publishing checks it. The rules for a good pitch are in [best practices](best-practices.md), and the background is in [research](research.md).

## 1. Deck

A deck is a folder in `DECKS_DIR`. It holds `deck.yml`, plus images and self-hosted fonts.

```yaml
title: Acme
language: en
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

**Fields on every slide.** The schema is strict: a field it does not know fails publish, which catches typos from people and agents alike.

| Field | Rule |
|---|---|
| `type` | Required; see the table below |
| `id` | Defaults to the type, and is required when a type repeats. It is the deep link (`#/problem`) and the tracking key, so a published id never changes |
| `headline` | Required. It states the claim as a sentence, so that the headlines read in a row tell the whole story |
| `detail` | At most about 80 words. It shows only when the deck is read alone, never while presenting |
| `sources` | Shown in small type, and may link to `https:` pages. Required on `market` |
| `seconds` | The planned speaking time for the slide |
| `notes` | For the team only |

**Blocks** that the types share:
- `points`: 1–3 short lines.
- `figures`: 1–4 of `{label, value, note?}`.
- `chart`, which has these fields:
  - `kind` (`bar` or `line`), `unit`, `period` and `source`;
  - `points`, each `{label, value, forecast?}`;
  - optional `cumulative`;
  - optional `highlight`, the label to emphasise.
- `image`: `{src, alt}`.
- `people`: `{name, role, proof, photo?}`.

**Slide types**, in the investor order: Sequoia's outline, with YC's traction and ask.

| Type | Purpose | Blocks |
|---|---|---|
| `title` | The company in one sentence | `image` (logo) |
| `problem` | Whose pain it is, and how they cope today | `points` |
| `solution` | The insight, shown as one visual | `image` or `points` |
| `why-now` | The change that makes it possible now | `points` |
| `product` | How it works, in three steps | `points` (3), `image` |
| `traction` | The proof, with the conclusion as the headline | `chart` or `figures` |
| `market` | Who buys, sized bottom-up | `figures` |
| `business-model` | How you earn money, and the unit economics | `figures` |
| `competition` | The alternatives, and the gap each one leaves | `points` |
| `team` | Why these people | `people` (1–6) |
| `financials` | Key metrics, the plan and the burn | `chart` or `figures` |
| `ask` | What you ask, what it buys, and a vision line | `amount`, `use_of_funds` (figures), `milestones` (points), `runway`; or `next_step` |
| `appendix` | Risks and details, after the ask | any |

**Outlines.**
- `investor` is the default. It requires `title`, `problem`, `solution`, `team` and `ask`, and its `ask` needs `amount`, `use_of_funds` and `milestones`.
- `sales` is for a customer or partner and follows Raskin's strategic narrative. It uses the same types in this order:
  1. `title`
  2. `why-now`: the change in their world
  3. `problem`: what is at stake for them
  4. `solution`: the future they can reach
  5. `product`: how you get them there
  6. `traction`: proof from customers like them
  7. `ask`: one next step

  It requires `why-now`, `problem`, `solution`, `traction` and `ask`, and its `ask` needs `next_step`. The five moves before the ask are Raskin's; the next step is this blueprint's own addition.

**Design.**
- Each type is one Vue component, so the design is fixed.
- Text is large: on the 1280×720 canvas, headlines are at least 48 px and body text at least 28 px.
- A chart shows one message. The `highlight` is in the theme colour and the rest is grey, and values carry labels as well as colour.
- Markdown in fields allows emphasis and links to the deck's own slides (`#/`), `https:` pages and `mailto:`. Raw HTML is never rendered.
- There are no animations or fragments, so a slide means the same presented, read and printed.

**Publish** is one command, `deck:publish <folder>`, and it is the quality gate.

**It fails on:**
- invalid YAML or an unknown field;
- an unknown type or a duplicate id;
- a type that the outline requires but the deck lacks;
- a chart without `unit`, `period` or `source`, or a `market` slide without `sources`;
- an investor `ask` without `amount`, `use_of_funds` or `milestones`, or a sales `ask` without `next_step`;
- an asset (image, font or video) from outside the folder, or a link that is not `#/`, `https:` or `mailto:`;
- an image that has no alt text or is missing from the folder;
- a theme colour below 4.5:1 contrast against the background.

**It warns on:**
- more than 20 slides;
- more than 3 points;
- more than 30 words presented on a slide, or more than 80 words in `detail`;
- a headline over 12 words, or one that is only a label (a single word, or the type name);
- a `cumulative` chart;
- an investor `ask` without `runway`;
- `seconds` that add up to more than `minutes`.

**It prints** the headlines in order, so the founder reads the story in a dozen lines before sharing it.

**It stores** an immutable version named by its content hash:
- `viewer.json`: everything except `notes`;
- `notes.json`: for the team only;
- the assets, listed in a manifest.

`deck.yml` never enters the version store. Publishing updates every link of the deck, and an open view keeps its version until the page reloads. Stats add up per slide id across versions.

## 2. Runtime and navigation

reveal.js (MIT) runs as a client-only component and renders the slide components into `<section id="<slide id>">`.

**Settings,** with the reveal.js version pinned:
- linear slides, with `hash: true`;
- a progress bar and the slide number;
- `controlsLayout: 'edges'`;
- the overview (`O`/`Esc`) and blackout (`B`);
- transition `none` when the viewer prefers reduced motion;
- `scrollActivationWidth: null`, because phones get the read layout instead of reveal's scroll view.

**This gives viewers:**
- Arrow keys, Space, Home and End; swipe on touch; click zones at the edges.
- A deep link per slide, a progress bar and a counter.
- On a wide screen, the link uses a 1280×900 canvas: the slide on top, with `detail` in a strip below it.
- Below 640 px wide, the link shows the **read layout** instead of reveal. It uses the same components as a responsive document, with body text of at least 16 px and `detail` under each slide. A deep link scrolls to its slide. reveal scales its canvas, so its own scroll view would shrink 28 px text to about 9 px on a phone.
- A screen-reader announcement of the current slide.

**Present.** The team presents from `/present/<deck>`, which needs a team session.
- **It shows** the current and the next slide, the speaker notes, the elapsed and remaining time, and per slide the planned time against the time spent.
- **Controls:**
  - a presenter clicker works, because it sends PageUp/PageDown or the arrow keys;
  - the timer can start, pause and reset;
  - picking a slide in the overview only previews it, and the screen follows on Enter, so the founder can find an appendix slide during questions;
  - after a run, a summary of planned against spent per slide makes every run a rehearsal.
- **Screen.** "Open screen" opens `/present/<deck>/screen`. That window shows only the slides, so on a video call the founder shares only that window. It follows the presenter through a `BroadcastChannel`:
  - one channel per run, with one pinned version;
  - the screen announces itself and receives the full state: the slide and blackout;
  - every change sends the full state again, so either window can reload and recover;
  - the presenter shows whether the screen is connected.
- **No `detail`.** Neither window shows `detail`.
- **Why not reveal's speaker view:** its notes plugin writes inline scripts into a popup, and the CSP blocks them.
- **Offline copy.** Before a pitch, the team saves two PDFs from `/present`, the presentation (no `detail`, no notes) and the read version (with `detail`), and opens both once offline, checking fonts and page breaks. ⇒ ALWAYS have an offline copy ready before the pitch.

**PDF.** When a link has `allow_pdf`, a button opens reveal's print view (`view: 'print'`). It opens only after the gate, once all assets have loaded. The PDF contains `detail` but never notes. ⇒ NEVER promise that a viewer cannot keep a copy: whatever the browser shows can be saved.

## 3. Share

**Link modes.**
- `open` is the default: a personal link whose only gate is the consent choice.
- `code` is for confidential material. A 6-digit code is mailed to the address on the link. The page shows a masked hint of that address, plus "Not you? Ask the sender for your own link".
- A link without a recipient is a broadcast, and is always `open`.

**Links.**
- A link has a random slug of at least 128 bits and optionally a recipient, which `code` requires.
- It also carries an `internal` flag, `allow_pdf` (off by default) and an optional expiry.
- A link can be revoked or archived, but never hard-deleted.
- Each recipient has one active link per deck, and a revoked link can be replaced.
- The recipient view groups links by normalised email.

**Gate.**
- The first open of a link in a browser shows two equal buttons: "Open with reading stats" and "Open without".
- The choice is stored in the grant, and the viewer can change it from the deck at any time.
- A valid grant skips the code step.
- Grants live in a sealed `__Host-` cookie: Secure, HttpOnly, SameSite=Lax, at most 20 grants, 30 days each.
- Opening a second link never logs out the first.

**Copy invite** gives the link plus one sentence in the deck language, and the founder sends it from their own mailbox. ⇒ ALWAYS a link generated by the system, NEVER one typed by a person or an AI.

## 4. Measure

Measuring happens only with consent. Without it, the server keeps only what access needs: the grant and the limit counters. There is no view, no slide time and no alert.

- **View.** With consent, each page load creates one view through `start.post`. A GET never counts, because mail scanners and link previews fetch links. The view id is stored in the sealed cookie, bound to its grant.
- **Tracker.** It loads only on a view with consent and counts visible time per slide id (Page Visibility API). In the read layout, the visible slide is the section most in view (IntersectionObserver). It pauses after 60 seconds without input, unless a video is playing.
  - It sends through `sendBeacon`, every 10 seconds, on each slide change and on `visibilitychange: hidden`.
  - Each beacon carries the **cumulative** milliseconds per slide, plus the furthest slide reached.
- **Server.** A beacon is accepted only when it carries the view's cookie, the link is active, the grant is valid and consent is current.
  - The server takes link, viewer and version from the view, never from the body.
  - It refuses unknown slide ids and values that are negative or not whole numbers.
  - It keeps the maximum per slide. The total increase in one beacon may not exceed the server time since the previous beacon plus 2 seconds, so a late, replayed or forged beacon cannot inflate the times.
  - Withdrawing consent closes the view, and later beacons are refused.
- **Excluded.** Views from known bot user agents are flagged, kept, and left out of totals and alerts. Team sessions and `internal` links never count.
- **Stats** per link and per recipient:
  - sessions: views more than 30 minutes apart count as separate sessions;
  - time per slide, and the furthest slide reached (the drop-off);
  - browsers, and device class.

  The admin labels every number as an estimate, and browsers as browsers, not people.
- **Alert** to the link's creator on a first open, on a return after more than 30 minutes, and on an open in a new browser. A new browser means a start without an earlier grant for that link: the link may have been forwarded, or the cookies cleared.

## 5. Security

**Gate every byte.** The server gates everything a viewer gets: deck data, images and video.
- `deck.get` returns the version's `viewer.json` and needs a grant. `notes.json` goes only to a team session.
- The file route serves only entries from the version's manifest, after the grant check. It resolves the path inside the version folder, refusing `..` and symlinks, and answers with `X-Accel-Redirect` to an nginx `location` marked `internal`. nginx handles Range requests.

⇒ NEVER put deck files in `public/`; NEVER use a client-side overlay as the gate.

**Recheck and headers.**
- Every request rechecks the link (revoked, expired) and the grant.
- Gated responses carry `Cache-Control: no-store`, `Vary: Cookie`, `Referrer-Policy: no-referrer` and `X-Robots-Tag: noindex`.
- Every page, the admin included, gets this CSP through `nuxt-security`:

  ```
  default-src 'none'; script-src 'self' 'nonce-{{nonce}}' 'strict-dynamic'; style-src 'self' 'unsafe-inline';
  img-src 'self'; font-src 'self'; media-src 'self'; connect-src 'self';
  frame-ancestors 'none'; base-uri 'none'; form-action 'self'
  ```
- The gate page of a `code` link uses a neutral `og:title`.

**Codes.**
- Codes come from a CSPRNG: 6 digits, valid 10 minutes, single use, stored hashed, and consumed atomically.
- Limits apply to both flows, the viewer code per link and the team code per account:
  - at most 5 wrong tries per link or account per 15 minutes, and a new code does not reset the count;
  - at most 3 sends per 15 minutes and 10 per day per recipient, and 20 per hour per IP.
- Counters live in the database, keyed by hash. They update atomically and expire with their window.

**Team.**
- Team login works by email code from an allow-list, bound to the purpose `team`.
- Every admin API and `/present` require an active team user, and a viewer grant never opens them.
- Escape everything the admin renders.

## 6. Mail

- The app sends three mails: the access code (only to the address on the link), the team login code, and the visit alert.
- Send from a subdomain with SPF, DKIM and DMARC, through a provider that keeps data in the EU. Examples are Mailcoach, Scaleway TEM, Lettermint, Mailgun EU, or the SMTP of a Dutch host such as TransIP.
- Turn click and open tracking off. Include a plain-text part, no remote images, and a monitored Reply-To.
- The mail carries a code, not a magic link, because scanners open links before people do.
- Missing mail configuration in production is an error, never a console log of the code. User text in a mail is escaped.

## 7. Privacy (NL)

- **Gate text.** The gate says:
  - who is responsible;
  - what is logged with stats and without;
  - why, and for how long;
  - the viewer's rights and the contact address.

  It links to the privacy page.
- **Consent.** Measuring per viewer needs consent (see Measure), and withdrawing it stops measuring at once. This is a conservative reading of EDPB 2/2023, not legal advice; there is no ruling on deck tracking.
- **No IP.** No IP address is stored, except as an expiring hash in a limit counter. The user agent becomes a device class and a bot flag on arrival. nginx keeps no access log for `/d/`.
- **Retention.**
  - Views and slide times are kept 12 months, then a daily job deletes them. The same job deletes used and expired codes and expired counters.
  - Backups rotate within 30 days.
  - The mail provider's log retention is set to its shortest option.
- **Erasure** deletes a recipient's views and blanks the recipient fields on their links. A soft delete is not an erasure.

## 8. Nuxt setup (example)

**Stack:**
- Nuxt 4 as a Node server behind nginx, on one EU VPS.
- SQLite through Drizzle and `better-sqlite3`, with WAL and a tested backup. Move to Postgres only from a second instance on.
- `nuxt-auth-utils`, with `runtimeConfig.session.name: '__Host-pitcher'`.
- `nuxt-security` for the headers.
- `nodemailer` over SMTP, with Mailpit locally.
- Zod for the deck schema.

```
app/components/slides/                one component per slide type
app/pages/d/[slug].vue                gate, then the deck (reveal.js, client-only)
app/pages/present/[deck]/index.vue    presenter: notes, next slide, timer
app/pages/present/[deck]/screen.vue   audience window, follows the presenter
app/pages/login.vue, admin/           team screens (the API decides, not the page)
server/api/d/[slug]/deck.get.ts       viewer.json of the version; needs a grant
server/api/d/[slug]/[...file].get.ts  manifest and grant check, then X-Accel-Redirect
server/api/d/[slug]/{code,verify,start}.post.ts
server/api/views/[id].post.ts         cumulative ms per slide
server/api/present/[deck].get.ts      viewer.json and notes.json; team only
server/api/admin/...
server/utils/                         db, gate, mail, limits, deck schema
scripts/deck-publish.ts               lint, headline outline and snapshot
examples/demo-deck/deck.yml           non-confidential deck for tests
```

Tables: `users`, `decks`, `deck_versions`, `links`, `codes`, `views`, `slide_times`, `attempts`.

## Proof

Run every test with a fixed clock. Browser tests use Playwright's own headless Chromium.

- **P1** Publish:
  - Fails on an unknown type, an unknown field, a duplicate id, a missing `ask`, a chart without a source, an external URL, missing alt text and a low-contrast theme colour.
  - Warns at 21 slides, on a label headline, and when `seconds` exceed `minutes`.
  - Prints the headline outline.
  - The version store holds no `deck.yml` and `viewer.json` holds no notes.
- **P2** A deep link `#/team` survives the gate and opens on the team slide.
- **P3** Navigation and layout:
  - Using only the keyboard, the deck can be paged, its overview opened and closed, blackout used, and its end reached.
  - At 320–430 px wide, the link shows the read layout: body text of at least 16 px, `detail` visible, and no horizontal scroll at 200% text zoom.
  - No slide of the demo deck overflows its canvas, in the presenter (1280×720) or in the link (1280×900).
- **P4** Gating:
  - Without a grant, the deck API and the assets return 403, also for a guessed path, `../` and a symlink.
  - With a valid grant, `deck.yml`, `notes.json` and a file outside the manifest still return 403.
  - A direct request to the internal nginx location fails.
  - A Range request on a video works.
- **P5** Links and grants:
  - A revoked or expired link with an old grant shows the gate.
  - A grant for link A does not open link B.
  - Two links in one browser both stay open.
- **P6** Codes, for both the viewer code and the team code:
  - 5 wrong codes block, and a new code does not reset the count.
  - A code expires after 10 minutes, and a used code fails.
  - The 11th send of a day is refused, and so is the 21st send in an hour from one IP.
- **P7** Views:
  - A GET creates no view.
  - A start with consent creates exactly one view.
  - A start without consent creates no view and no alert.
- **P8** Tracking:
  - Without consent, the tracker is not loaded and beacons are refused.
  - Browser B cannot write to browser A's view.
  - A replayed or inflated beacon does not raise a time past the elapsed server time, and an unknown slide id is refused.
  - After consent is withdrawn, later beacons are refused.
- **P9** Notes and presenting:
  - The deck API and the PDF never contain `notes`.
  - `/present` and its API return 403 without a team session.
  - The screen window follows the presenter, recovers after either window reloads, and the presenter shows when the screen is lost.
- **P10** Headers and CSP:
  - Gated responses carry the four headers and the CSP.
  - Presenting, the screen window, the read layout and the print view work under the enforced CSP.
  - An external script or fetch is blocked.
- **P11** Mail:
  - The code mail goes only to the link address.
  - Production without mail configuration fails loudly.
  - `<script>` in a name shows as text in the admin.
- **P12** Erasure removes the recipient's views. The daily job deletes data older than 12 months, and expired codes.
- **P13** Browser: open the demo deck with stats, go through 3 slides and hide the tab. The admin then shows 3 slide times and the furthest slide.

## Choices per startup

- **Domain:** `deck.<domain>`, because recipients trust it.
- **Mail provider and sending subdomain:** see Mail.
- **Retention:** 12 months is proposed.
- **Hosting:** an EU VPS, for example through Forge or Ploi.
- **Measuring without consent:** this blueprint records nothing per viewer without consent. A startup that wants to log opens on legitimate interest decides that with a privacy lawyer, and then changes only Measure and Privacy.
- **Existing decks:** rewrite them into `deck.yml`. An AI agent can do that from a PDF, with the founder checking each headline and number.
