# Loomnovel

Loomnovel is a platform for writing long-form fiction, built around an agent and the way working authors actually write. It supports Chinese and English.

This repository explains how Loomnovel works, how it protects your manuscript, and how we build and test it.

[中文](README.zh-CN.md)

## Our promises to authors

1. **Privacy.** Without your authorization and consent, we (Loomnovel) do not read or process your data, and we do not sell it or share it with anyone. When you use an AI feature, you know that the text that step needs is sent to the corresponding model provider for processing.
2. **What your manuscript is used for.** Your manuscript is used only to help you write this book. We (Loomnovel) do not use your manuscript to improve the services we provide. When you use AI features, each provider's API terms are listed under [Providers](docs/data-and-privacy.md#providers). You can export the whole book and delete your projects at any time.
3. **Security.** Only the author can read or write their books. Manuscripts are stored on servers in the EU, encrypted in transit, and backed up every day, encrypted, to off-site storage in the EU.
4. **Cost.** During the beta, we pay for all model usage. The cost of every AI call is shown on the spot, at the provider's list price, with no markup.

## Status

- In development since April 2026, live since July 2026.
- Invite-only. 69 registered accounts as of 30 October 2026.
- The founder writes a serialized novel with it.

## Compared with Codex + skills or a chat website

- **A workbench and workflow built for writing.** Planning, drafting, review and polishing all happen in one place. Every output is versioned, so you can always go back. Carefully designed memory records your writing habits and preferences and follows them next time. You can stop at any point and edit the text by hand, without switching between a dozen tabs and tools.
- **The right model for each job.** Different base models are good at different things: writing, editing, polishing. We call different models for different steps, to get the best quality at a sensible cost.
- **Context arranged for writing.** We call models directly through their APIs. That avoids the system prompts coding agents carry, full of tool instructions, and avoids writing fiction with models tuned for code; both lower the quality of the prose. Each model can attend effectively to a different amount of context, so we decide how much material to give it by its limit, and the writing and editing prompts are tuned for each model separately.

## What is in this repository

| Document | What it covers |
|---|---|
| [How it works](docs/how-it-works.md) | The core idea, the four ways in, and connecting over MCP |
| [Architecture](docs/architecture.md) | Two services, a database and shared packages: what each does, how they are deployed, and which one faces the internet |
| [Security](docs/security.md) | What we protect, from whom, the measures in place, red-team results |
| [Data and privacy](docs/data-and-privacy.md) | Where your text goes, how long it is kept, how to export and delete it |
| [Decisions](docs/decisions/README.md) | Architecture decisions, with the options we compared and what each choice costs |
| [Engineering](docs/engineering/working-with-ai-agents.md) | How we build with AI coding agents, how changes to prompts and models are tested, one large refactor, one incident review |
| [Changelog](CHANGELOG.md) | Dated changes an author would notice |

## On request

For a live demo (with an invite code, or connected over MCP), an architecture walkthrough, a screen-share of the private repository, or more incident reports and evaluation details, write to us: founder@loomnovel.com

## Links

- Website: https://loomnovel.com
- Privacy policy: https://loomnovel.com/en-US/privacy
- Reporting a vulnerability: [SECURITY.md](SECURITY.md)
- Longkeep (formerly OpenAgents), the open-source tool we use to run AI coding agents while building Loomnovel: https://github.com/wingsofsky/longkeep

## License

All rights reserved. See [LICENSE](LICENSE).
