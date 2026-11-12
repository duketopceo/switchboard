# switchboard

> Delegate work to other models and watch it run — durable background jobs, parallel model runs, inspectable context, costs, tools, and artifacts, accessible from the assistant you are already using.

## Stack

- Rust workspace — local service owns jobs, credentials, scheduling, persistence, cancellation
- [Rune](https://github.com/anomalyco/rune) (GPU-rendered UI, pinned dev rev) + `rune-kit` components — live run dashboard
- SQLite append-only event log + materialized job state; filesystem artifact store
- OpenRouter for model calls; OpenCode plugin for host integration; portable MCP interface for other hosts

## Local Setup

```bash
# clone
git clone https://github.com/duketopceo/switchboard
cd switchboard

# toolchain: Rust 1.99.1
rustup show

# env vars
cp .env.example .env
# fill in .env (OPENROUTER_API_KEY at minimum)

# run — no crates yet; first runnable slice lands with plan unit U1
cargo build --workspace
```

## Deploy

Local-first service + desktop dashboard. The service is the durable owner of jobs; hosts and dashboards are clients that can disconnect and reconnect without losing runs.

## Docs

- `docs/BRIEF.md` — product and architecture brief (source of truth for scope)
- `docs/plans/` — ce-plan implementation plans
- `ROADMAP.md` — phased delivery and release gates
- `DESIGN.md` — interaction contract (observable execution, honest states)
- See `AGENTS.md` for agent context.
