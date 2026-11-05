# How changes to prompts and models are tested

These are the rules we follow when testing changes to prompts and models, with three examples.

## Rules

- **The highest-fidelity check available wins.** In order: a real run, an integration test, a unit test, a type check, reading the code. Green unit tests do not count as done.
- **Model changes get a real call.** Any change to what a model receives is run against a real test project with real model calls, and the cost is read from the ledger, not estimated.
- **Structure-only changes must prove nothing changed for the model.** When code is moved or reorganised, the requests it assembles are compared with the old ones, byte for byte. If they differ, that difference is taken out of the change and decided on its own.
- **Prompt and model changes are compared on fixed cases.** The current and the candidate version run the same inputs, and the outputs are read side by side. A change that alters what authors receive is decided by the founder.
- **Numbers carry their scope.** Every figure says what was measured, on how many cases, and when.

## Example 1: proving a refactor did not change model input

When the agent loop moved to the server in October 2026, one step extracted a whole agent turn into a separate function. Check: the requests built for 332 test scenarios were compared with the baseline and were identical byte for byte; the event stream sent to the browser was compared against stored snapshots and was unchanged; one real turn ran on the full stack.

## Example 2: prompt caching

In July 2026 the cost ledger showed that the model accounting for 51% of model spend up to then had a cache hit rate of zero. Two causes were found, both in how requests were laid out. Measured on repeated requests after the fix (10 July 2026):

- the same request sent again to that model had 98.1% of its input tokens served from cache;
- in the agent chat, the second turn of a conversation had 99.5% served from cache.

## Example 3: a fix for silent structured-output failures

Covered in detail in [the incident write-up](incident-structured-output.md). In short: nine places that expect structured data back were fixed at once. Checks: 9 of 9 live calls returned valid output over three rounds, in Chinese and English; prompt caching still worked after the change; and failed or retried calls, which had not been recorded before, now appear in the cost ledger.

## Red-team rounds

Prompt-injection testing follows the same pattern: fixed attack cases, benign cases to catch false positives, a fix, and a full re-run. Results are on the [security page](../security.md). One finding there changed the method: the same defense held on one model and failed on another, so a defense counts as tested only on the model it runs on.
