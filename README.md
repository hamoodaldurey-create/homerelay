# HomeRelay

A household disruption becomes a feasible draft schedule, with explainable task handoffs and explicit approval.

Entry being prepared for **Amazon Developer Hackathon · Alexa+ track**. Created October 7, 2026. Not submitted yet.

## Working implementation

The app runs a real stateless MCP server implementing protocol 2025-11-25. Its three tools read fictional sample data, replan caller-supplied context, and draft handoffs. The UI calls those tools at runtime. It saves approved assignments and demo acknowledgments to D1.

## Run locally

Use Node 22.13 or later. Install the locked dependencies with `pnpm install --frozen-lockfile`. Run `pnpm db:generate` only after editing db/schema.ts. Run `pnpm build` to produce the Worker and D1 configuration. Apply the included migration to the local DB:

```sh
node --import ./scripts/sites-env.mjs ./node_modules/wrangler/bin/wrangler.js d1 execute DB --local --config dist/server/wrangler.json --persist-to .wrangler/state --file drizzle/0000_woozy_tusk.sql
pnpm dev
```

The dev command uses the configured execution profile. The standalone Worker preview uses `pnpm start`. The managed Sites environment uses its preview supervisor. Deploy through Sites or adapt the logical D1 binding for your own Cloudflare account.

## Tests

`pnpm test` bundles and runs tests/planner.test.ts with Node’s test runner. 10 focused tests currently pass. `pnpm exec tsc --noEmit` checks source types.

## Data and access

The demo uses synthetic sample data. Anonymous demo state is isolated using an HttpOnly random capability cookie; authenticated Sites requests use the trusted Site-scoped user ID. No client-supplied owner ID is trusted. Same-origin checks protect write requests. Revision checks prevent lost updates in the household and quote workspaces. This is a hackathon MVP, not a production security certification.

## Current limitations

The conversation interface is an Alexa+ web simulation using explicit scenario routing. It has no LLM or live Alexa/device connection. The planner is a priority-first heuristic, not a globally optimal scheduler. Demo acknowledgment does not verify another household member’s identity.

## Submission status

Public demo: https://homerelay-hamood.hamood-al-du-3093.chatgpt.site

The complete source snapshot is included in `source.zip`. Extract it into a new directory before running the commands above:

```sh
mkdir source
cd source
unzip ../source.zip
```

The source snapshot matches the verified deployed implementation. Public video upload and official Devpost submission remain outstanding. This is not yet an officially submitted entry.

## License

MIT. Bundled dependencies retain their own licenses.
