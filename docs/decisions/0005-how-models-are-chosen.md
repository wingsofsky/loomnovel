# 0005 · How models are chosen and switched

Date: ongoing since July 2026

## Context

Loomnovel uses models from several providers. Prices change, new models arrive every few weeks, and older ones are retired on fixed dates. Each switch can change what authors get, so it needs evidence and a way back. Every model change is decided by the five criteria below.

## Criteria

1. **Measured on the task, not on benchmarks.** A candidate runs the same fixed test cases as the current model, and the outputs are compared side by side. A switch that changes what authors receive is decided by the founder, not by whoever ran the test.
2. **Cost from the ledger.** Every model call is recorded with its token counts and priced at the provider's current list price. Comparisons use those numbers, not estimates.
3. **Caching that actually hits.** A cheaper model whose prompt cache misses for our request shape can cost more than an expensive one that hits. Cache hit rates are measured per model.
4. **Reliable structured output.** Several steps need structured data back. A model or adapter that silently ignores the requested format is not usable for them until that is fixed (see the [incident write-up](../engineering/incident-structured-output.md)).
5. **Resistance to injected instructions.** In our red-team rounds, the same defense held on one model and failed on another. A defense counts as tested only on the model it runs on.

## How a switch is made

- Model choice is configuration, resolved in one place. A switch is one change with a known rollback.
- Retired model IDs stay registered, so old cost records and conversations still resolve.
- Authors can choose among several models for drafting and editing. If a book's saved choice is no longer offered, it falls back to the default without an error.
