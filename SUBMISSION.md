# HomeRelay — Amazon entry draft

Updated October 8, 2026. **Official competition submission is not confirmed.**

| Field | Current value |
| --- | --- |
| Project | HomeRelay |
| Creator | Hamood Al Durey |
| Tagline | When a household morning changes, make every handoff workable. |
| Primary track | Alexa+ — real MCP server with an explicit web simulation |
| Demo | https://homerelay-hamood.hamood-al-du-3093.chatgpt.site |
| Source repository | https://github.com/hamoodaldurey-create/homerelay |
| License | MIT |
| Demo video | https://www.youtube.com/watch?v=0URpycdpzP0 — public, 1:54, English captions |
| Optional mini challenge | Not claimed; a separate qualifying contribution has not been prepared |
| AWS Builder | Not entered; no AWS service integration is claimed |

## Inspiration

A late parent can disrupt several tasks at once. Asking someone else to drive does not establish that they have the time, that the bag will be ready, or that another obligation will not overlap. We wanted a household assistant that makes the constraints visible and leaves the final decision with people.

## What it does

HomeRelay turns an explicit availability update into a proposed morning schedule. In the sample, “Lena is 30 minutes late” triggers real MCP calls that find eligible owners and available intervals for the school run, packing, feeding the cat, and dropping off a parcel.

Each assignment has a time interval and an explanation. The planner checks availability windows, capabilities, deadlines, durations, and overlaps. It explores alternatives when preserving the original owner would prevent another task from fitting. Essential tasks take priority. Missing capacity remains an unresolved task instead of becoming a reassuring but impossible schedule.

Approval is a separate action. The server recalculates the proposal against the current saved household and refuses an edited or stale draft. An approved schedule preserves times, owners, availability, and unresolved work across reloads. Users can record demo completion for resolved tasks and inspect the activity history. Unresolved tasks cannot be recorded complete through the app's action endpoint.

Handoff requests remain drafts inside the demonstration. No external messages are sent and no devices are controlled.

## How the required technology is used

HomeRelay implements MCP 2025-11-25 over stateless Streamable HTTP at `/mcp`. Its runtime tools are `get_demo_household`, `replan_household`, and `draft_handoffs`. The interface performs the initialization/readiness sequence and calls the tools to generate its results; MCP is part of the working path rather than a README-only claim.

Tool discovery includes explicit field schemas, workload bounds, and read-only annotations. Tools accept caller-supplied context and never expose saved private household records or approve changes. The MCP connection tab runs initialization and sample planning calls on demand.

The web interface is labeled as an Alexa+ simulation. It uses explicit scenario parsing, not an LLM, and has not been connected to a live Alexa+ account or device. The entry should present this boundary clearly in both its description and video.

## Technical implementation

React 19 and TypeScript form the interface. Vinext/Vite produce the Cloudflare Worker. Zod validates household, disruption, and proposal data. A bounded search retains up to 48 candidate schedules and examines eligible owners and time-interval alternatives. It favors essential/routine/flexible task coverage and fewer handoffs without claiming global optimality.

Cloudflare D1 stores the demo household, full approved schedule, and activity record. Generated schema-only migrations and prepared statements handle the database. Anonymous workspace cookies isolate fictional demo records; authenticated requests use the trusted Site-scoped identity. Same-origin and optimistic revision checks protect household actions.

This is a hackathon prototype using fictional data, rather than a production household service or verification of another person's identity.

## Changes completed during the event

The first implementation was created October 7, 2026. The October 8 upgrade:

- explores alternative owners/times instead of keeping only the first greedy assignment;
- saves exact approved schedule times and availability across reloads;
- validates approvals on the server and rejects stale or edited drafts;
- keeps unresolved tasks open and prevents completion acknowledgments for them;
- rejects ambiguous names, unspecified delays, and invalid scenario numbers;
- provides explicit MCP input schemas and a connection verification action;
- improves readable text, mobile layouts, draft discard, and visible plan results.

## Validation and evidence

17 focused automated checks pass, alongside TypeScript checks. They cover the identified planning, approval, persistence, input, and HTTP transport cases. A saved-plan JSON round trip verifies that the schedule is retained. A second check against the actual built Worker and a fresh local D1 database passed page rendering, MCP initialization/discovery/tool calls, approval/reload, stale-write rejection, partial planning, unresolved completion rejection, valid acknowledgment, separate workspace state, and origin checks. Publication status is tracked separately from these local runtime checks.

Automated checks do not establish live Alexa+ compatibility, public video availability, or an official competition submission. Browser visual verification remains pending in this session because the available preview could not be reached.

## Demonstration — target 2 minutes 40 seconds

| Time | Show | English narration |
| --- | --- | --- |
| 0:00–0:15 | Sample household and four tasks | “One late parent can disrupt several jobs. HomeRelay checks whether a replacement plan can actually fit.” |
| 0:15–0:45 | Enter Lena's 30-minute delay and run planning | “The web simulation calls our real MCP planner. It checks time, capability, deadlines, and overlaps.” |
| 0:45–1:10 | Four feasible tasks, two handoffs, exact intervals and explanation | “Omar can drive; Ivy can pack the bag. These are proposed assignments, with the reasons visible.” |
| 1:10–1:35 | Approve, reload, and acknowledge a resolved task | “Approval is explicit and checked again on the server. Times and availability survive a reload. The activity log records our demo acknowledgment.” |
| 1:35–2:00 | Report Omar unavailable after that first approval | “A second disruption leaves the school run unresolved. We show the gap and prevent a false completion.” |
| 2:00–2:25 | MCP connection verification and three tools | “This is MCP 2025-11-25 over Streamable HTTP. The tools calculate drafts and never send messages or approve changes.” |
| 2:25–2:40 | Brief close on the working app | “The data is fictional. This is an Alexa+ web simulation with a real MCP server; no live device connection or LLM is claimed.” |

Published video: https://www.youtube.com/watch?v=0URpycdpzP0. Duration 1:54, with English captions. It explicitly labels October 7 core workflow footage and a summary of verified October 8 code improvements; fresh footage of the improved interface remains pending. The new title/end cards do not claim to be new UI footage.

## Potential impact

The target user is a household coordinating a small number of time-sensitive tasks. The proposed benefit is fewer manual checks when one person becomes unavailable and clearer handoff decisions. No user study, adoption figures, or time-saving measurements are claimed. A next evaluation would compare successful completion of the same disruption scenarios with and without HomeRelay.

## Product feedback

- **MCP specification:** initialization, tool schemas, read-only annotations, and the JSON-response form of Streamable HTTP support a small edge-hosted tool server. Fully explicit household schemas make client use more predictable. A concise worked example combining protocol negotiation, dual Accept values, JSON tool results, and edge deployment would reduce implementation effort. Would use again: yes.
- **Alexa+:** the hackathon's MCP/simulation path is the integration target. No live Alexa+ API, account, or device was used, so no experience with live Alexa onboarding or device behavior is claimed.
- **Vinext, React, TypeScript, Vite:** a shared typed model and server routes kept the product compact. Response parsing and runtime-specific build/preview behavior need explicit handling. The supervised preview could report running while requests were unreachable in this session; that was an environment limitation, not an Amazon service issue. Would use again: yes, with runtime verification.
- **Cloudflare Workers, D1, Drizzle:** durable JSON records and prepared statements fit this prototype. Local schema setup is separate from production migration application. Revision conflicts are exposed to the user rather than silently overwriting a newer plan. Would use again: yes.
- **Zod:** validation bounds the search and rejects ambiguous/invalid data. Legacy state defaults preserve older records. Would use again: yes.
- **Tailwind and Lucide:** used for readable responsive styling and functional icons. No package-specific failures were observed. Would use again: yes.
- **Node, pnpm, esbuild:** the locked dependency setup and bundled Node test runner make the regression suite reproducible. Would use again: yes.

No AWS, Bee, Fire TV, or Ring integration is claimed. Any friction entry must describe an observed event and identify the actual tool involved; no Amazon-platform failure or judging bonus is asserted.

## Before official entry completion

- Verify final deployed demo and build evidence.
- Public MIT repository verified: https://github.com/hamoodaldurey-create/homerelay. Complete source, assets, tests and run instructions are in source.zip.
- Public English-captioned demonstration published: https://www.youtube.com/watch?v=0URpycdpzP0.
- Register for the Amazon hackathon, then fill the actual Devpost entry, feedback, and Alexa+ track fields. Do not claim the optional Open Source challenge without a separate qualifying contribution.
- Complete eligibility/terms and final submission, then retain Devpost's receipt. A prepared draft is not a submitted entry.
