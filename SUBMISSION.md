# HomeRelay — submission draft

## Inspiration

A household disruption becomes a feasible draft schedule, with explainable task handoffs and explicit approval.

## What it does

The app runs a real stateless MCP server implementing protocol 2025-11-25. Its three tools read fictional sample data, replan caller-supplied context, and draft handoffs. The UI calls those tools at runtime. It saves approved assignments and demo acknowledgments to D1.

## What is implemented and what remains

The conversation interface is an Alexa+ web simulation using explicit scenario routing. It has no LLM or live Alexa/device connection. The planner is a priority-first heuristic, not a globally optimal scheduler. Demo acknowledgment does not verify another household member’s identity.

## Architecture

React 19 client UI → same-origin Vinext Worker routes → Cloudflare D1. Tailwind CSS and Lucide icons. MIT licensed. Test source, database schema and migration are included.

## Validation

10 focused unit tests pass. Runtime and submission verification are tracked separately; unit tests do not establish live PayPal integration or official submission.

## Demo sequence

Show the initial workflow, execute the primary action, show its result and persistence, then demonstrate an invalid or unsupported case. Explain current limitations in English. All displayed sample data is fictional.

## Required before submission

- Public source repository accepted by this competition.
- Working demo access for judges.
- A real recorded demonstration, uploaded to the required public video provider.
- User confirmation of the specific competition’s official terms at the final submission step.

## Product feedback

- MCP 2025-11-25: the official transport and tools specification made a stateless Worker implementation possible. Initialization, readiness notification, discovery, calls, Accept validation, and protocol-header handling are tested. More complete edge-runtime examples of the minimal JSON-only Streamable HTTP flow would make onboarding easier. Would use again: yes, for portable agent tools.
- Vinext/React/TypeScript: full-stack routing and typed UI state were useful. Cloudflare's Response.json() types require explicit response parsing/casts. The managed HTTP preview lacks crypto.randomUUID(), so the browser client uses crypto.getRandomValues() for non-secret request IDs. These were observed implementation issues, not Alexa API failures. Would use again: yes, with those runtime boundaries documented.
- Cloudflare D1/Drizzle: generated schema-only migrations and prepared statements provide durable state. Local migrations must be applied separately from deployment migrations. Optimistic revision checks made simultaneous updates explicit. Would use again: yes.
- Zod: validates planner input and bounds workload; invalid-input tests pass. Would use again: yes.
- Tailwind/Lucide: used for responsive styling and icons. No package-specific failures were observed. Would use again: yes.

No AWS, Bee, Fire TV, Ring, or live Alexa+ service was used. Only the Alexa+ MCP/web-simulation track is proposed. No unsupported mini-challenge claims are made.
