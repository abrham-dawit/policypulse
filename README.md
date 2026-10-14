# PolicyPulse

**See the law moving around you — then stress-test it on a digital twin of your own community, before anyone votes.**

PolicyPulse does two things that feed each other:

1. **The Pulse Map** — a live, tilted 3D map that finds your area and plots the real federal and state bills moving around you right now, next to a rail of local policy news. Every marker is a real bill (Congress.gov / OpenStates); every story is real local news.
2. **The Simulator** — pick any bill (or paste your own) and watch it play out over three simulated years on a population built from live U.S. Census ACS data for that state, down to exactly who gets hurt and who benefits.

## What it does

- **Live bills near you** — geolocation or ZIP/city search, reverse-geocoded with Mapbox; federal bills from the Congress.gov API and state bills from OpenStates, plotted on a 3D Mapbox + deck.gl map that flies to your state.
- **Local policy news rail** — real headlines for your area from GNews, streaming in next to the map.
- **Census-grounded populations** — ingestion pulls real ACS data (population, median income, rent, racial composition, tenure, industry mix) and spawns residents whose joint distribution matches the actual community.
- **Agent-run simulation** — a Mastra `PolicyAnalyst` agent turns free-text legislation into a structured impact model. Residents then live month by month through direct policy effects, market drift, and cascading second-order shocks — a landlord sells, tenants get displaced; a wage floor rises, a small business cuts hours — all streamed live to the dashboard over SSE.
- **Inequality Spotlight** — impacts broken down by segment, the Gini shift, materialized unintended consequences, and ranked who-gets-hurt / who-benefits lists.
- **Resident stories** — click any simulated resident and read a first-person account of how the policy changed their life, written by a Mastra `Resident` agent.
- **The Council** (`/council`) — a bill debated live by agents standing in for the people it touches, ending in a verdict on the evidence.
- **Ghost mode** (`/ghost`) — a crisis scenario where agents think, negotiate, and resolve it live.
- **The Lab** (`/lab`) — Monte Carlo runs for confidence bands, status-quo (no policy) comparisons, and sensitivity analysis.
- **Validation** (`/validate`) — real, studied policies (like San Francisco's 1994 rent-control expansion) run through the same engine, with the predicted direction of effects checked against published research.
- **Contact your rep** — find your representatives and draft an email about a bill without leaving the app.
- **Runs with zero API keys** — every live surface degrades honestly instead of inventing data: labeled fallback datasets, heuristic policy parsing, template resident stories, an in-memory event bus, and "connect this key" empty states. Add keys to light each surface up.

## Tech stack

| Layer | Tech |
| --- | --- |
| Framework | Next.js 16 (App Router, RSC, SSE streaming), React 19, TypeScript, Tailwind CSS v4 |
| Agents | Mastra — `PolicyAnalyst` + `Resident` agents and a simulation workflow, on Anthropic Claude Haiku |
| Live civic data | U.S. Census ACS, Congress.gov (federal bills), OpenStates (state bills), GNews (local news), Mapbox (3D map + geocoding), Browserbase (optional live ingestion) |
| State & streaming | Redis — Streams (event log), JSON (snapshots), TimeSeries (metric trends), Pub/Sub |
| Viz | Mapbox GL + deck.gl, Recharts, Framer Motion, Lucide |

## Run it

```bash
npm install
npm run dev
```

Then open the printed URL (usually http://localhost:3000). Copy `.env.example` to `.env.local` and add whatever keys you have — all of them are optional, and each one lights up one surface: Mapbox token → the 3D map, Census key → real population data, Congress/OpenStates keys → live bills, Anthropic key → the agents, Redis URL → the event backbone. Env vars load on boot, so restart after changing them.

## How it's put together

`src/app` holds the pages (Pulse Map, simulate, council, ghost, lab, validate, runs) and about 25 API routes. `src/lib` holds the engine: census ingestion and demographics, the simulation orchestrator, the council and ghost runtimes, metrics, and the Redis-backed event bus (`bus.ts`). Runs stream to the browser as server-sent events, so you watch the population change as the simulation runs instead of waiting for a final report.

## Credit

Built by a four-person team: me, [@lukeeskinner](https://github.com/lukeeskinner), [@Zablon-Tesfaye](https://github.com/Zablon-Tesfaye), and [@rathodkunj2005](https://github.com/rathodkunj2005). This is my copy of the project — the original repo is [lukeeskinner/PolicyPulse](https://github.com/lukeeskinner/PolicyPulse), and the full project brief with screenshots lives in `docs/presentation/`.
