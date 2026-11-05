# Working with AI coding agents

Most of Loomnovel's code was written by AI coding agents. This page describes who decides what, how work is handed to an agent, how it is checked, and one feature from requirement to release, including what went wrong.

## Who does what

- **The founder** sets requirements, makes architecture and product decisions, approves every plan before work starts, checks the results in the running product, and decides what is released. Changes that alter what authors see, what they pay, or the quality trade-offs of a model are never decided by an agent.
- **A lead agent** (Claude Code) turns approved plans into written briefs, checks every delivery against its diff and tests, runs the full stack, and commits only with the founder's approval.
- **Implementation agents** (other coding-agent CLIs) write code from the briefs. They do not commit and do not see the conversation that led to the brief.
- Small tasks are done by the lead agent itself; the review rules below apply to its own work too.

Literary and stylistic judgment is never delegated.

## The project's memory lives in files

An agent session starts with no memory. Everything it must know is in the repository:

- a short contract file with the rules every session follows (read first, every time);
- a decision log, append-only, about 200 entries since April 2026, each saying who decided and why;
- plan documents with a progress table, so a session can pick up where the last one stopped.

The tooling for handing tasks between agents and keeping a record of each is open source: [Longkeep](https://github.com/wingsofsky/longkeep).

## What a brief contains

One brief, one checkable goal. Each brief states:

- the working directory, branch and commit it starts from;
- the files it may change, and nothing else;
- the facts it relies on, each with a file and line;
- what not to do;
- what counts as done;
- the form of the answer: what changed, the output of its own checks, open questions, risks.

Before a brief is sent, every statement in it about the current code is checked again against that commit. This rule came from experience. Agents follow briefs literally: a wrong statement makes them stop with no changes, or worse, work around it. Existing tests that the change will break are listed one by one with what to do about each.

## How a delivery is checked

1. The diff touches only the allowed files.
2. Type checks and tests are run again by the lead agent, not taken from the agent's report.
3. Changes to the web product are run on the full local stack, with real model calls where models are involved.
4. Questions and assumptions the agent raised are checked by reading the code.

Claims are reported at three levels: *verified* (seen running), *implemented* (written, not run), *hypothesis*. When unsure, the lower level is used.

In the October 2026 refactor, roughly one delivery in six had a problem that was found in this review and had to be fixed before commit.

## Example: per-author credentials between services

Background in [decision 0002](../decisions/0002-only-web-accesses-the-database.md).

**Requirement.** The internal data API accepted a shared internal secret plus an author ID, which tied every author's data to one secret. Replace that with a short-lived credential that the web service issues for one author, which the model service can pass on but cannot create.

**Brief (summary).** Issue the credential in the web service. The data API accepts only the author named in the credential. The model service forwards it unchanged. The local MCP server gets the same treatment. Done means: the full stack works through the normal path, and three kinds of forged access are refused.

**First delivery: stopped with no changes.** The agent found that one end-to-end test script also read and wrote data with the shared secret and an author ID, which the brief had not mentioned. It stopped instead of guessing. The lead agent decided the scripts would use personal access tokens like any other client, and re-sent the brief.

**Second delivery: unit tests green, full stack broken.** On the local stack every data request from the model service was refused, 10 out of 10. Cause: the development server loaded the credential module twice, and each copy set itself up independently, so the side that issued credentials and the side that checked them disagreed. Unit tests load the module once and could not see this. Fix: both copies share one setup per process.

**Checks after the fix.** Through the normal path, the agent's read tools all worked (8 of 8). A shared secret with an arbitrary author ID, a forged credential and a malformed credential were all refused. A call that skipped the web service was refused. Released on 18 October 2026.

## Example of a rejected delivery

During the move of the agent loop to the server, a brief said one existing test must not be changed. The agent's first version made the production code detect the test framework's mocks so that test would pass. It was rejected. Every later brief carried a new rule: production code must not know whether it is under test.
