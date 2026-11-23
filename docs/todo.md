# Todo

Build order. A step is done when its proofs from the plan pass.

## Now

- [ ] `decision` **Domain, mail provider and hosting**
  ~XS @startup `setup-choices`
  See "Choices per startup" in the plan.

- [ ] `feature` **Skeleton: Nuxt app, database, team login**
  ~M @builder `skeleton`
  Nuxt 4, SQLite through Drizzle, sealed session, login by email code from an allow-list, Mailpit locally.
  Proof: P11 (mail configuration fails loudly).

- [ ] `feature` **Deck format, slide components and publish**
  ~M @builder `deck`
  Zod schema, one Vue component per type, `deck:publish` with lint and a hash snapshot, and the demo deck.
  Proof: P1.
  Blocked by: `skeleton`.

- [ ] `feature` **Runtime, navigation and presenter view**
  ~M @builder `runtime`
  reveal.js as a client-only component with the plan's settings, `/present` for the team, and print view.
  Proof: P2, P3, P9.
  Blocked by: `deck`.

- [ ] `feature` **Links, gate and consent**
  ~M @builder `gate`
  Open and code links, consent buttons, grants, the code mail, limits, headers and X-Accel-Redirect.
  Proof: P4, P5, P6, P10, P11.
  Blocked by: `runtime`.

- [ ] `feature` **Tracker and visits**
  ~M @builder `tracking`
  A visit from the gate POST, the tracker only with consent, cumulative ms per slide, and the bot flag.
  Proof: P7, P8.
  Blocked by: `gate`.

- [ ] `feature` **Stats, alerts and invite text**
  ~M @builder `stats`
  Per link and recipient, drop-off, visit alerts, and "Copy invite".
  Proof: P13.
  Blocked by: `tracking`.

- [ ] `feature` **Retention and erasure**
  ~S @builder `privacy`
  The daily delete job, and erasure per recipient.
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
