<p align="center"><img src="icon.svg" width="112" alt="Pitcher icon: a jug holding a slide with a rising bar chart"></p>

# Pitcher

An open blueprint for a good pitch-deck tool. It covers how to:
- write a deck as typed slides that follow research-informed pitch guidelines;
- present it live with a presenter view;
- share it through a personal link that reads well, even on a phone;
- with the viewer's consent, see per slide what was read.

This repository holds no code, only the plan. A developer or an AI agent can build the tool from it step by step, as a standalone app for your own startup. Maintained by Konstruktiv.

## What's inside

| File | What it gives you |
|---|---|
| [`docs/plan.md`](docs/plan.md) | The deck format, presenting, sharing, measuring, security, privacy, an example Nuxt setup and 13 numbered proofs |
| [`docs/todo.md`](docs/todo.md) | The build order; each step names the proofs it must pass |
| [`docs/best-practices.md`](docs/best-practices.md) | Twenty rules for a good pitch, with sources, and which of them publishing checks. Also the brief for anyone, human or AI, who writes a deck |
| [`docs/research.md`](docs/research.md) | Background and sources: existing tools, mail scanners, mail delivery, privacy and link security. Where it differs from the plan, the plan wins |
| [`docs/changelog.md`](docs/changelog.md) | What changed, and why |

## Build it with an AI agent

Give the agent this repository and a prompt such as:

> Build the first open step of `docs/todo.md` according to `docs/plan.md`. Stop when its proofs pass, and report what you could not prove.

## Rules for every builder, human or agent

- Secrets go only in `.env` (gitignored), never in code, tests, logs or screenshots.
- Decks are confidential and never enter the app repository; they live in `DECKS_DIR` on the server. The repo holds one demo deck for tests.
- A viewer never receives speaker notes, raw HTML from a deck, or any byte of a deck without passing the gate.
- Code and identifiers are in English. Text that a viewer reads follows the deck language (`nl` or `en`).
- A step is done when its proofs pass.

## Good to know

- **Not legal advice.** The privacy and consent choices follow a conservative reading of EU and Dutch rules. Check them for your own situation before you go live.
- **Pick your own product name.** "Pitcher" is the name of this blueprint. An existing sales-software company already uses it, so give the app you build its own name.

## License

MIT, see [`LICENSE`](LICENSE). You may use, change and share everything here, also commercially. The only condition is that copies keep the license notice.
