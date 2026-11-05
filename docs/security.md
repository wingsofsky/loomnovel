# Security

How Loomnovel protects your manuscript and your account: what we protect, from whom, the measures in place, and how they were tested.

## What we protect

| Asset | Why it matters |
|---|---|
| Authors' manuscripts | Unpublished work. Leaking it to another author or to the public is the worst outcome. |
| Accounts | Access to someone's books. |
| Provider keys and internal secrets | They allow spending money and impersonating services. |
| Prompts and orchestration | They are the product. |
| The budget | Model calls cost money; abuse can run up a bill. |

## From whom

- **Another signed-in author** trying to read or change books that are not theirs.
- **Anyone on the internet** probing the public service.
- **Text inside a manuscript.** Authors import their own books and paste in outside text. Any of it can hide instructions aimed at the model.
- **People trying to extract the prompts**, by asking the agent directly or by asking it to "build something like you".

## Measures

### Web layer

| Measure | How it was checked |
|---|---|
| Every request that touches a book checks that the book belongs to the signed-in author. | 11 July 2026. Two-user test, 13 assertions: author B's reads, edits, deletes, recall queries, version restores and conversation lists against author A's data all failed. In production: the owner got 200, a second account 404, no token and a bad token 401. |
| Only the web service connects to the database. | Design, since the first release. |
| Row-level security (RLS) in the database, as a second layer behind the application's ownership checks. | Design. |
| Sensitive data is encrypted at the application level before it is stored, in addition to the hosting provider's encryption at rest. | Design. |
| The model service has no public address and accepts calls only over Railway's private network. | Design, since 11 July 2026. |
| Calls from the model service back to the data API must carry a short-lived credential issued by the web service for one author. | 12 October 2026, before the release on 18 October. A shared secret with an arbitrary author ID, a forged credential and a malformed credential were all refused, and direct calls that skip the web service were refused. |
| Database queries are parameterized. Queries go through the ORM; the few hand-written SQL queries use parameterized templates, and none is built by joining strings. | Code review, October 2026. |
| Output is escaped by default when pages render. The only unescaped HTML is a few fixed inline scripts with no user input. | Code review, October 2026. |
| Least privilege: each service gets only the access it needs, and the browser holds no service secrets. | Design. |
| Prompt files are never sent to the browser. | Every build compares the browser bundle against sentences from the server-side prompt files (added October 2026). |

### Model layer

| Measure | How it was checked |
|---|---|
| The agent can only propose changes. Applying a proposal is a separate step on the server, and whether it needs the author's click depends on the mode the author chose. | Design. See [How it works](how-it-works.md). |
| Text from manuscripts, uploads and tool results is treated as data, not as instructions, and is separated from instructions by markers that outside text cannot predict. | Two red-team rounds, below. |
| Requests are screened by rules before they reach a model. | Two red-team rounds, below; checked against ordinary fiction for false positives. |
| Outputs are checked for leaked system-prompt text. | Two red-team rounds, below. |

### Cost and abuse

| Measure | How it was checked |
|---|---|
| Invite codes, per-account daily quotas, and spending budgets for agent runs. | Invite codes and quotas since July 2026. Since October 2026 the quota is checked before each call. |

## Red-team rounds

Both rounds were run in July 2026.

**Round 1.** Categories: requests for the system prompt and the tool list under various pretexts; instructions hidden inside an uploaded manuscript; attempts to make a revision pass destroy the author's text.
Found: two cases leaked internal text, one in the agent chat and one in a revision pipeline.
Fixed, then re-tested: 6 of 6 cases passed.
This round also changed how we test: the same defense held on one model and failed on another, so a defense counts as tested only on the model it actually runs on.

**Round 2.** 11 cases, adding requests to copy the system and indirect probing of its structure.
Found: 10 of 11 passed the first time; one case leaked internal text.
Fixed, then re-tested: 11 of 11 passed.

Both rounds were checked against ordinary fiction, for example a novel about a hacker, so that the defenses do not get in the way of writing. Nothing was blocked.

The hidden-instruction case was run again on 10 July 2026, after a change to how prompts are laid out, and passed.

## Reporting a vulnerability

See [SECURITY.md](../SECURITY.md).
