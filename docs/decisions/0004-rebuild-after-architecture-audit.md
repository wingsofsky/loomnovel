# 0004 · After an architecture audit, rebuild in stages instead of patching or rewriting

Date: 11 October 2026

## Context

By October 2026 the product had grown for six months with features added quickly. Before adding more, the codebase was audited: eight read-only audits by coding agents, each covering one area and backing every finding with a file and line, plus two web-research passes, run with an AI research agent and checked against the sources it cited, on how small teams structure similar systems. The findings were checked by hand before they were used.

The audit found three root problems:

- **The same thing was done in several places.** The web page, the MCP server, other clients and the server each had their own version of the same capabilities.
- **No single place decided** which model a call used, how much reasoning it got, or which version of a prompt it ran.
- **The browser did server work**: it ran the agent loop, put together the context sent with model requests, and reported costs after each call. Prompt templates stayed on the server.

## Options

1. **Keep patching.** Cheapest per week, but every fix had to be made in several copies.
2. **Rewrite from scratch.** Clean, but months without releases, and no way to prove the new system behaves like the old one.
3. **Split into more services.** Both research passes found the same advice for a one-person team: it would produce a distributed monolith.
4. **Rebuild in stages inside the existing two services.** Add a platform layer (model resolution, prompt registry, admission checks, cost ledger, job queue), move each area behind it, and keep every stage releasable on its own.

## Decision

Option 4, in eight stages. Each stage ended with a regression run and a tag, so it could be released or rolled back alone. Behaviour changes were not allowed inside a migration step; where a step changed what a model would receive, that was taken out and decided separately.

## Cost

About one week of feature freeze (11 to 18 October 2026), and a large review load: most implementation was delegated to coding agents, and every unit had to be checked. How that was done is in [Refactoring without regressions](../engineering/refactor-without-regressions.md).
