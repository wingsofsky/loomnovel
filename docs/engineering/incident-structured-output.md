# Incident: structured output silently ignored after a provider switch

July 2026. No manuscript text was lost or exposed. For four days one step returned nothing, and a few others returned partial results.

## Summary

Several background steps in Loomnovel ask a model for structured data rather than prose. In July 2026 some of these steps were moved to a cheaper provider reached through an OpenAI-compatible adapter. The adapter does not pass a JSON schema to the provider unless it is told the provider supports schemas. By default it falls back to plain JSON mode, without a warning. The provider also rejects strict schema mode outright.

So the model received no schema. It returned valid JSON, often in the wrong shape.

## Timeline

- **22 July 2026.** Several steps are switched to the cheaper provider to cut cost.
- **Four days later.** A writing-chain experiment comes back with an empty review step. The step that writes review notes had returned nothing since the switch, with no error: its four retries all failed validation, and the caller fell back to an empty result by design. Monitoring had shown nothing. First fix: the template for that step lists every field it must return, with a test that checks it.
- **28 July 2026.** An end-to-end test of the new MCP server shows the same symptom in another step. A search finds the pattern in nine places. The fix moves into the model gateway, which covers all nine at once.

## Impact

- The review step returned nothing for four days.
- The other affected steps returned nothing or partial results on some calls.

## Why nothing raised an alarm

- The provider accepted every request. No error was returned where the schema was lost.
- The output was valid JSON. The failure only appeared later, when the shape was checked.
- Failed and retried calls were not recorded in the cost ledger, so the failure rate did not show up in cost reports either.
- No check after the switch exercised every step that expects structured output.

## Root cause

Our model gateway used the adapter with its default setting for this provider, and nothing in the gateway checked that the schema was actually sent. The model did what it was asked; it was never asked for the schema.

## Fix

- The schema now reaches the model in a form this provider honours, without changing the cacheable part of the request.
- A response is accepted only if it passes schema validation. Anything else raises an error instead of passing through.
- Failed and retried calls are written to the cost ledger as their own entries.

## Verification

- After the gateway fix: 9 of 9 live calls returned valid output over three rounds (6 in Chinese, 3 in English), and prompt caching still worked.
- Worldbook sync on the default route passed 2 of 2 real calls at about $0.004 per call. The stopgap used until then, routing that step through a more expensive model, had cost about $0.10 per call.
- In a test with four failed attempts, the recorded usage matched the token counts exactly.

## What changed afterwards

- From now on, after switching a model or provider, every step on that route that expects structured output is run, not only the prose steps.
- Structured responses are validated at the boundary. A wrong shape fails loudly.
- The cost ledger includes failed calls, so a jump in failures shows up as a jump in cost.
