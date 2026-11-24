# Todo

Build order. A step is done when its proofs from the plan pass.

## Now

- [ ] `decision` **Domain, mail provider and hosting**
  ~XS @startup `setup-choices`
  See "Choices per startup" in the plan.

- [ ] `feature` **Skeleton: Nuxt app, database, team login**
  ~M @builder `skeleton`
  Nuxt 4, the plan's tables through Drizzle, sealed session, nuxt-security with the CSP, team login by email code, Mailpit.
  Proof: P6 (team code), P11 (mail configuration fails loudly).

- [ ] `feature` **Deck format, slide components and publish**
  ~M @builder `deck`
  Zod schema, one Vue component per type, both outlines, `deck:publish` with lint, headline outline and snapshot, and the demo deck.
  Proof: P1.
  Blocked by: `skeleton`.

- [ ] `feature` **Runtime, navigation and presenter**
  ~M @builder `runtime`
  reveal.js client-only with the plan's settings, the read layout for phones, `/present` with controls and its screen window, and print view.
  Proof: P3, P9.
  Blocked by: `deck`.

- [ ] `feature` **Links, gate and consent**
  ~M @builder `gate`
  Links and their admin, grants as rows with consent, the code mail, limits, the Origin check, headers and the file route.
  Proof: P2, P4, P5, P6 (viewer code), P10, P11.
  Blocked by: `runtime`.

- [ ] `feature` **Tracker and views**
  ~M @builder `tracking`
  A view from the start POST with consent, bound beacons, cumulative ms per slide, the furthest slide, and the bot flag.
  Proof: P7, P8.
  Blocked by: `gate`.

- [ ] `feature` **Stats, alerts and invite text**
  ~M @builder `stats`
  Per link and recipient, sessions, drop-off, alerts on first open, return and new browser, and "Copy invite".
  Proof: P13.
  Blocked by: `tracking`.

- [ ] `feature` **Retention and erasure**
  ~S @builder `privacy`
  The daily delete job for views, codes and counters, erasure per recipient, and no access log for `/d/`.
  Proof: P12.
  Blocked by: `tracking`.

- [ ] `chore` **First release**
  ~S @builder `release`
  An EU VPS behind nginx, the domain and mail live, all proofs green, and a test mail to a real inbox.
  Blocked by: `setup-choices`, `stats`, `privacy`.

## Later / blocked

- [ ] `feature` **Editor UI**
  ~L @startup `editor`
  Edit `deck.yml` in the browser with a live preview; publish keeps the same checks.

- [ ] `feature` **Comments per slide**
  ~S @startup `comments`
  The notification goes to the link's creator, with the text escaped.

- [ ] `feature` **Phone remote**
  ~S @startup `remote`
  Page the deck from a phone without a clicker.
