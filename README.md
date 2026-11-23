# Pitcher

A blueprint for startups: share HTML pitch decks through a personal or a public link, and see per viewer and per slide what was read. A startup builds it as its own standalone app, with its own repository, database, mail account and secrets. Maintained by Konstruktiv.

Status: blueprint only; there is no code here.

## Read in this order

1. [`docs/plan.md`](docs/plan.md): what to build, the Nuxt setup and the rules.
2. [`docs/todo.md`](docs/todo.md): the build order, with what blocks what.
3. [`docs/reports/Pitchdecks delen en volgen.md`](docs/reports/Pitchdecks%20delen%20en%20volgen.md): the research behind the rules, with sources.

## Rules for every builder, human or agent

- Secrets only in `.env` (gitignored), never in code, tests, logs or screenshots.
- Decks are confidential and never enter the app repository; they live in `DECKS_DIR` on the server. The repo holds one demo deck for tests.
- Every byte of a deck goes through the server gate, so a deck file never sits in `public/`.
- Code and identifiers in English; text a viewer reads follows the deck language (`nl` or `en`).
- Each piece ships with the tests listed under "Proof" in the plan for what it touches.

## License

MIT, see [`LICENSE`](LICENSE): free to use, adapt and share.
