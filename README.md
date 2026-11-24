<p align="center"><img src="icon.svg" width="112" alt="Pitcher icon: a jug holding a slide with a bar chart"></p>

# Pitcher

Blueprint for a pitch-deck tool: write typed slides, present live, share a personal link, and, with consent, see per slide what was read.

No code, only the plan. Hand it to a developer or an AI agent.

| File | Contents |
|---|---|
| [plan](docs/plan.md) | Deck format, presenting, sharing, measuring, security, privacy, Nuxt setup, proofs |
| [todo](docs/todo.md) | Build order; each step lists its proofs |
| [best practices](docs/best-practices.md) | 20 pitch rules, with sources |
| [research](docs/research.md) | Background; the plan wins on conflict |
| [changelog](docs/changelog.md) | Changes |

## Agent prompt

> Build the first open step of `docs/todo.md` per `docs/plan.md`. Stop when its proofs pass; report what you could not prove.

## Rules

- Secrets only in `.env`.
- Decks live in `DECKS_DIR`, never in the repo (one demo deck excepted).
- Viewers never get notes, raw HTML or ungated bytes.
- Code in English; viewer text in the deck language.
- Done means the step's proofs pass.

## Notes

- Not legal advice.
- An existing sales-software company already uses the name "Pitcher": give your build its own name.

## License

[MIT](LICENSE).
