# 0003 · Run the agent on the server, not in the browser

Date: 14 to 17 October 2026 (released 18 October 2026)

## Context

Until October 2026 the agent loop was driven by the browser. The page sent each step to the model service, applied the agent's proposals, kept track of the budget and decided when to continue. That had three consequences:

- Closing the tab or losing the connection stopped a run halfway.
- The same capability (drafting a chapter, reviewing it, updating the worldbook) was implemented separately for the web page, the MCP server and other clients, and the copies drifted apart.
- The two biggest interface files had grown to between 5,600 and 5,900 lines, and they were among the files most often touched by bug fixes.

## Options

1. **Keep the browser in charge** and make it resume after a reload. A smaller change, but it fixes the first problem only.
2. **Move the loop to the server.** Runs and their steps become database records; a server-side worker advances them; the page only shows progress and sends controls (stop, send a message now, queue it for the next step).

## Decision

Option 2. Each capability now has one implementation on the server, called by the agent, the MCP server and the web page alike. Proposals are applied by the server, so the approval rules live in one place. The page became a viewer and a remote control.

## Cost

- More state on the server: runs, steps and background jobs, with migrations for each.
- Deploys have to let running work finish before the model service stops.
- The change was done in small units over several batches, each one checked for unchanged behaviour before the next (see [the refactor write-up](../engineering/refactor-without-regressions.md)).

## Result

Closing the page no longer stops a run (tested 17 October 2026). The two large interface files went from 5,630 and 5,889 lines to 1,473 and 119.
