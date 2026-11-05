# 0001 · Two services: a public web service and a private model service

Date: April 2026 (split), 11 July 2026 (model service taken off the internet)

## Context

The product does two kinds of work. Page requests and data reads are short and must be reliable. AI work is long: a chapter draft streams for one to several minutes, the agent can run for many steps, and provider APIs fail in their own ways.

Until July 2026 both services had public addresses, and the browser talked to the model service directly with its own authentication.

## Options

1. **One application** that serves pages and runs the AI work. Simplest to deploy. A slow or crashing AI task affects page requests, and everything runs in the internet-facing process.
2. **Two services, both public.** The state before July. Two authentication paths to keep in step, and the model service is open to direct probing.
3. **Two services, model service private.** The browser only talks to the web service; the web service forwards AI requests over the private network.
4. **More services** (separate services for drafting, revision, review, ingestion).

## Decision

Option 3. The web service forwards AI requests, including streams, without buffering them. The model service still makes its own admission checks (quota, concurrent requests) on forwarded calls.

Option 4 was rejected in the October 2026 audit (see [0004](0004-rebuild-after-architecture-audit.md)): for a one-person team, more services would turn function calls into network calls without making anything easier to change.

## Cost

- One extra hop for every AI request.
- Streaming through a proxy needs care: compression and buffering have to be switched off for event streams, and a cancelled browser request must cancel the upstream call so the author is not charged for text nobody will see.
