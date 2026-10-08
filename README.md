# HomeRelay

Turn a disrupted household morning into a workable schedule with clear handoffs and explicit human approval.

[Try the public demo](https://homerelay-hamood.hamood-al-du-3093.chatgpt.site)

Built during the **Build, Ship, Shape: Amazon Developer Hackathon**, starting October 7, 2026. Proposed primary track: **Alexa+**. Official submission is not confirmed. Public source: https://github.com/hamoodaldurey-create/homerelay. An updated public English demo video is still required.

## The problem and the working flow

If a parent is delayed, changing the school-run owner is only part of the problem: packing the bag, other driving tasks, available time, and conflicting assignments must also fit.

1. Enter “Lena is 30 minutes late”, or use the availability controls.
2. The web simulation calls a real MCP server to search owner and timing alternatives.
3. Inspect each assignment, its exact interval, and the explanation. Tasks without a workable slot remain unresolved.
4. Approve the workable schedule. The server recomputes the draft against the current household, then saves times, owners, availability, and approval history in D1.
5. Reload the page: the approved schedule remains visible. Acknowledge a resolved task and inspect Activity.
6. After that first approval, report “Omar is unavailable” to see how a second disruption can leave the school run unresolved. It cannot be acknowledged complete until resolved.

The sample household is fictional. The demonstration does not send messages, operate devices, or verify the identities of other household members.

## MCP implementation

The app exposes `POST /mcp`, using **MCP 2025-11-25** and the JSON-response form of stateless Streamable HTTP. `GET /mcp` returns 405 because this server does not offer an SSE stream.

| Tool | Behavior |
| --- | --- |
| `get_demo_household` | Returns a fictional household, never saved private data. |
| `replan_household` | Calculates a draft from caller-supplied household context and one availability update. |
| `draft_handoffs` | Produces draft requests with changed owners and exact intervals. |

Initialization, readiness notifications, discovery, and tool calls are implemented. Input schemas describe each member/task field and bounds, so a client can form valid calls. Tools are read-only and cannot approve schedules, mark tasks complete, or send messages. The interface's **MCP connection** tab can initialize the endpoint and run sample tools.

A client must send `Accept: application/json, text/event-stream`, `Content-Type: application/json`, and `MCP-Protocol-Version: 2025-11-25`. The web interface is explicitly labeled as an Alexa+ simulation. No live Alexa+ account/device connection or LLM is claimed.

## Planner and storage

The bounded planner retains up to 48 candidate schedules, considering different eligible owners and the ends of available time intervals. It favors essential tasks, then routine/flexible tasks, then fewer handoffs. It is not a globally optimal scheduling solver. Supported bounds: 8 members, 30 tasks, 120-minute task durations, and 240-minute additional delays.

Approved times and unresolved tasks are stored with the household JSON, so this upgrade needs no new database migration. Availability updates are incremental; reset the sample to begin a new demo morning. Previously saved households without an approved schedule are normalized without dropping their task/history records.

Server-side actions are `approve`, `acknowledge`, and `reset`. Approval recalculates a draft. Revision checks reject stale writes from another tab. Anonymous demo state uses an HttpOnly random capability cookie; authenticated Sites requests use the trusted Site-scoped user ID. Same-origin checks protect writes. Public MCP tools never read this saved data.

## Run and validate

Use Node 22.13 or later and the existing pnpm lockfile.

```sh
pnpm install --frozen-lockfile
pnpm test
pnpm exec tsc --noEmit
pnpm build
node scripts/check-runtime.mjs
```

Apply the included schema migration to a local database when using the standalone Worker preview:

```sh
node --import ./scripts/sites-env.mjs ./node_modules/wrangler/bin/wrangler.js d1 execute DB --local --config dist/server/wrangler.json --persist-to .wrangler/state --file drizzle/0000_woozy_tusk.sql
pnpm start
```

The managed Sites environment uses its supervised preview and publishing workflow. Deploy through Sites, or adapt the logical D1 binding for your own Cloudflare account. Generate a new migration only after changing `db/schema.ts`.

The 17 regression tests cover feasible handoffs, capability shortages, alternative owners, immutable inputs, rejected stale/edited drafts, persisted schedules, unresolved completion, legacy records, input ambiguity, and MCP HTTP behavior. `scripts/check-runtime.mjs` also passed against the built Worker and a fresh local D1 database: page rendering, MCP lifecycle/discovery/tools, approval/reload, stale-write handling, partial plans, completion rules, workspace isolation, and origin checks. `SUBMISSION.md` contains the entry draft, evidence limits, product feedback, and demo sequence.

## Limits and submission work remaining

- No live Alexa+/device connection or LLM interpretation; this is a real MCP server plus an explicit-scenario web simulation.
- No external messages or independently verified household acknowledgments.
- No task dependencies, travel-time estimation, or claim of globally optimal schedules.
- An updated public English video under three minutes remains required before official entry completion.
- No AWS Builder, Bee, Fire TV, or Ring claims. The public MIT-licensed repository is available for an Open Source mini-challenge entry.

## License

MIT. Bundled dependencies retain their own licenses.

## Source snapshot

The full tested source is in `source.zip`. Extract it into a new directory before running the commands above. The snapshot includes app routes, MCP tools, planner, tests, migration, assets, and the locked dependency setup.
