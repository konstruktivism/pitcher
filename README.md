# Pitcher

A blueprint for a good pitch-deck builder: write a deck as typed slides that follow research-informed pitch guidelines, present it live with a presenter view, share it through a personal or public link that reads well alone, and, with the viewer's consent, see per slide what was read. A startup builds it as its own standalone app. Maintained by Konstruktiv.

Status: blueprint only; there is no code here.

## Read in this order

1. [`docs/plan.md`](docs/plan.md): the deck format, runtime, sharing, measuring, the Nuxt setup and the numbered proofs.
2. [`docs/todo.md`](docs/todo.md): the build order; each step names the proofs it must pass.
3. [`docs/best-practices.md`](docs/best-practices.md): the rules for a good pitch, with sources, and which of them publish checks. Also the brief for anyone, human or AI, who writes a deck.
4. [`docs/research.md`](docs/research.md): background and sources. Where it differs from the plan, the plan wins.

## Rules for every builder, human or agent

- Secrets only in `.env` (gitignored), never in code, tests, logs or screenshots.
- Decks are confidential and never enter the app repository; they live in `DECKS_DIR` on the server. The repo holds one demo deck for tests.
- A viewer never receives speaker notes, raw HTML from a deck, or a byte of a deck without passing the gate.
- Code and identifiers in English; text a viewer reads follows the deck language (`nl` or `en`).
- A step is done when its proofs pass.

## License

MIT, see [`LICENSE`](LICENSE): free to use, adapt and share.
