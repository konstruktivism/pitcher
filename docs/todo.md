# Todo

## Now

- [ ] `decision` **Domain, mail provider and hosting**
  ~XS @startup `setup-choices`
  See "Choices per startup" in the plan: `deck.<domain>`, an EU mail provider with DKIM, an EU VPS.

- [ ] `feature` **Skeleton: Nuxt app, database, team login**
  ~M @builder `skeleton`
  Nuxt 4, SQLite through Drizzle, sealed session, login by email code from an allow-list, Mailpit locally.

- [ ] `feature` **Decks from DECKS_DIR with versions**
  ~S @builder `decks`
  Scan `deck.json`, copy each version (HTML and assets) under its hash, and add a demo deck in `examples/`.
  Geblokkeerd door: `skeleton`.

- [ ] `feature` **Links and server gate**
  ~M @builder `gate`
  Public and email links, code mail, grants in the cookie, headers, Range for video, attempt and send limits.
  Geblokkeerd door: `decks`.

- [ ] `feature` **Tracker and visits**
  ~M @builder `tracking`
  Inject `public/tracker.js`; open by POST, slide time, bot flag, version pinning.
  Geblokkeerd door: `gate`.

- [ ] `feature` **Admin stats and invite text**
  ~M @builder `stats`
  Per link and per recipient, numbers labelled as estimates, a "Copy invite" button, and the first-open alert.
  Geblokkeerd door: `tracking`.

- [ ] `feature` **Consent, retention and erasure**
  ~S @builder `privacy`
  Store and withdraw the gate choice, delete after 12 months in a daily job, and erase per recipient.
  Geblokkeerd door: `tracking`.

- [ ] `chore` **Convert the startup's decks**
  ~S @builder `deck-import`
  Strip their own gate and tracking code, make paths relative, self-host fonts, add `data-slide-id`.
  Geblokkeerd door: `decks`.

- [ ] `chore` **First release**
  ~S @builder `release`
  An EU VPS, the domain and mail live, the full Proof list green, and a test mail to a real inbox.
  Geblokkeerd door: `setup-choices`, `stats`, `privacy`, `deck-import`.

## Later / blocked

- [ ] `feature` **Comments per slide and notification**
  ~S @startup `comments`
  The notification goes to the link's creator, with the text escaped.

- [ ] `feature` **Contacts with pipeline stages**
  ~M @startup `contacts`
  Only if the funnel does not live elsewhere; otherwise there are two truths.
