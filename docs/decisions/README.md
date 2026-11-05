# Decisions

The private repository keeps a full decision log, about 200 entries since April 2026. These are the five that shaped the system most, each with the options we compared at the time and what the choice costs.

| # | Decision | Date |
|---|---|---|
| [0001](0001-split-web-and-model-service.md) | Two services: a public web service and a private model service | Apr 2026, private since Jul 2026 |
| [0002](0002-only-web-accesses-the-database.md) | Only the web service accesses the database; the model service acts with per-author credentials | Apr 2026, tightened Oct 2026 |
| [0003](0003-run-the-agent-on-the-server.md) | Run the agent on the server, not in the browser | Oct 2026 |
| [0004](0004-rebuild-after-architecture-audit.md) | After an architecture audit, rebuild in stages instead of patching or rewriting | Oct 2026 |
| [0005](0005-how-models-are-chosen.md) | How models are chosen and switched | Ongoing since Jul 2026 |
