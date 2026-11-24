# Todo

A step is done when its proofs pass.

## Now

- [ ] `decision` **Domain, mail provider, hosting**
  ~XS @startup `setup-choices`
  See "Choices per startup" in the plan.

- [ ] `feature` **Skeleton**
  ~M @builder `skeleton`
  Nuxt 4, the tables, sealed session, CSP, team login by email code, Mailpit.
  Proof: P6 (team), P11.

- [ ] `feature` **Deck and publish**
  ~M @builder `deck`
  Zod schema, a component per type, both outlines, `deck:publish`, demo deck.
  Proof: P1.
  Blocked by: `skeleton`.

- [ ] `feature` **Runtime and presenter**
  ~M @builder `runtime`
  reveal.js, the read layout, `/present` with its screen window, print view.
  Proof: P3, P9.
  Blocked by: `deck`.

- [ ] `feature` **Links and gate**
  ~M @builder `gate`
  Links and their admin, grants with consent, the code mail, limits, the Origin check, headers, the file route.
  Proof: P2, P4, P5, P6 (viewer), P10, P11.
  Blocked by: `runtime`.

- [ ] `feature` **Tracking**
  ~M @builder `tracking`
  Views, bound beacons, time per slide, furthest slide, bot flag.
  Proof: P7, P8.
  Blocked by: `gate`.

- [ ] `feature` **Stats and alerts**
  ~M @builder `stats`
  Stats per link and recipient, alerts, "Copy invite".
  Proof: P13.
  Blocked by: `tracking`.

- [ ] `feature` **Retention and erasure**
  ~S @builder `privacy`
  The daily delete job, erasure per recipient, no access log for `/d/`.
  Proof: P12.
  Blocked by: `tracking`.

- [ ] `chore` **First release**
  ~S @builder `release`
  An EU VPS, domain and mail live, all proofs green, a test mail to a real inbox.
  Blocked by: `setup-choices`, `stats`, `privacy`.

## Later / blocked

- [ ] `feature` **Editor**
  ~L @startup `editor`
  Edit `deck.yml` in the browser with a preview; the same checks apply.

- [ ] `feature` **Comments per slide**
  ~S @startup `comments`
  Notify the link's creator; escape the text.

- [ ] `feature` **Phone remote**
  ~S @startup `remote`
  Page the deck from a phone.
