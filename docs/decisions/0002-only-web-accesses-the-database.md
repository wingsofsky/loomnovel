# 0002 · Only the web service accesses the database

Date: April 2026; tightened 12 October 2026 (released 18 October 2026)

## Context

The model service needs book data: the chapter it is drafting, the worldbook, earlier chapters for recall. It also writes results back. Ownership checks for books live in the web service.

Until October 2026 the model service reached the web service's data API with a shared internal secret and a header naming the author. That works, but it ties every author's data to one secret.

## Options

1. **Give the model service its own database connection.** Fewer round trips. Two services would then need correct ownership checks, and a bug in either one could expose any author's books.
2. **Keep the shared secret plus an author header.** Works, but one leaked secret is enough to read every book.
3. **Keep the database behind the web service, and have the web service issue a short-lived credential for one author** with each forwarded request. The model service passes it back when it calls the data API and cannot mint one itself.

## Decision

Option 3. The web service is the only place that checks ownership and the only service that connects to the database. The data API accepts calls from the model service only with a valid per-author credential.

## Cost

- Extra round trips: the model service calls the data API instead of the database.

## Checked

Before release: a shared secret with an arbitrary author ID, a forged credential, and a malformed credential were all refused, and calls that skipped the web service were refused. The integration test also found a problem that unit tests could not see; it is described in [Working with AI agents](../engineering/working-with-ai-agents.md).
