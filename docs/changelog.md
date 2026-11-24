# Changelog

## 24-11-2026

- Three review rounds toward the best pitch tool for presenting: a read layout for phones instead of reveal's scaled scroll view, presenter controls with a recoverable screen window and offline PDFs, typed ask fields, grants as rows so withdrawn consent holds, table columns, admin screens, and versions bound to the link's deck. Proof: `docs/plan.md` P2–P13. ⇒ ALWAYS test reading at phone width; NEVER let a grant open another deck's version.
- Best practices for a pitch added from deep research (investor guidance, famous decks, presentation research, the best tools); the plan now has one source for presenting and reading (`detail`), an investor and a sales outline, a strict schema, a presenter with a screen window, and lint rules from those practices. Proof: `docs/best-practices.md`, `docs/plan.md`. ⇒ ALWAYS state the claim in the headline; NEVER let an agent invent a number for a deck.
- Second-model review applied: `deck.yml` never in the version store and `notes.json` only through the team API, beacons bound to the view's cookie, limits for team codes, a written CSP, nothing logged per viewer without consent. Proof: `docs/plan.md` P4, P6, P8, P10. ⇒ ALWAYS test gating also with a valid grant.

## 23-11-2026

- Blueprint widened to a pitch-deck builder: typed slides in `deck.yml`, reveal.js runtime with navigation and presenter view, publish lint, simpler tracking (cumulative time per slide, tracker only with consent), numbered proofs; from a second-model review and deck research. Proof: `docs/plan.md`, `docs/research.md`. ⇒ NEVER send speaker notes or raw deck HTML to a viewer.
- MIT license added so any startup may reuse the blueprint; proof: `LICENSE`. ⇒ ALWAYS keep everything in this repo fit for public reuse.

## 17-11-2026

- Plan written from four research notes and an independent review by a second AI model; proof: `docs/research.md`. ⇒ ALWAYS build from `docs/plan.md`; NEVER reuse code, data or secrets from another system.
- Made generic for any startup; mail providers are examples, and the viewer chooses whether reading time is measured. ⇒ NEVER name a specific startup or its content in this blueprint.
