# Refactoring without regressions

From 11 to 18 October 2026 the product was rebuilt in eight stages while it stayed in production. Why it was done is in [decision 0004](../decisions/0004-rebuild-after-architecture-audit.md). This page covers how behaviour was kept the same and how the release was checked.

## The stages

| Stage | What changed |
|---|---|
| 0 | Stop-gaps the audit found urgent |
| 1 | A platform layer: shared request contracts, a prompt registry with versions, one place that resolves models and settings, per-author service credentials, a cost ledger written by the server, quotas checked before calls |
| 2 | Drafting: model requests assembled on the server instead of in each client |
| 3 | Revision: one service instead of several similar pipelines |
| 4 | Review and worldbook: one write path for the worldbook, a background job queue for work that follows a save |
| 5 | The agent loop moved to the server ([decision 0003](../decisions/0003-run-the-agent-on-the-server.md)): 115 work units in 11 batches |
| 6 | Removing what was left unused |
| 7 | Full regression, release checks, release |

Every stage ended with a regression run and a tag, so it could be released or rolled back on its own.

## Keeping behaviour the same

- **Moves and changes are separate.** A unit that moves code must not change what a model receives or what an author sees. Assembled requests and the event stream to the browser were compared with the previous version byte for byte. Where a difference was found, it was taken out and decided on its own.
- **Small units, checked one at a time.** Stage 5 alone was 115 units. Each had a brief, a delivery, a review and its own commit (see [Working with AI agents](working-with-ai-agents.md)).
- **A fixed regression list.** Nine end-to-end scenarios covering the main ways an author works: the drafting workspace, the agent chat, the plain editor, the revision workshop, the MCP server, and others. They are run with real model calls on a Chinese and an English test book. The final run used 49 model calls and cost $0.10.
- **Interface changes were checked in a browser.** In the batches that split up the large interface files, the page structure was captured before a change and compared after it.

## Results

| | Before | After |
|---|---|---|
| Chat component | 5,630 lines | 1,473 lines |
| Proposal component | 5,889 lines | 119 lines |
| Server entry for the agent | 1,126 lines | 69 lines |
| Stage 6 cleanup | | 1,472 lines removed net; zero findings from the unused-code check; zero import cycles |

Line counts for the first three rows are from the start and end of stage 5.

## Release checks (18 October 2026)

- Database migrations replayed cleanly on an empty database.
- The production schema was compared with the expected one: no unplanned drift.
- Both service images built from the release commit.
- **Backup and restore drill before migrating.** A full dump of the production database was restored into a fresh scratch database of the same major version. Row counts for nine kinds of records, and full-row checksums of five core tables, matched production. Only then were the migrations applied.
- Release order: the model service first, then the web service, because the web service depends on new model-service capabilities.

## What the process caught

- A credential bug that only appeared on the full stack, not in unit tests (described in [Working with AI agents](working-with-ai-agents.md)).
- A delivery that made production code detect tests in order to pass one. Rejected; a rule was added to every later brief.
- Brief errors: early on, several units stopped with no changes because the brief described the code wrongly. Since then every statement in a brief is checked against the code before sending.
