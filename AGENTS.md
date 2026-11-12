# AGENTS.md — switchboard

> This file is the agent entry point for this repo.
> Full agent context lives at: https://github.com/duketopceo/luke-agents

## What This Repo Does

switchboard is a persistent model-delegation workspace. A host (OpenCode first) submits jobs — consult or worker mode — to a local service that owns scheduling, credentials, an append-only event log, and cancellation. A live Rune dashboard shows each delegate's observable execution: streamed output, tool calls, artifacts, usage, and cost. Results survive the host conversation moving on.

## Key Files

- `Cargo.toml` — workspace manifest; `members` stays empty until plan units add crates
- `docs/BRIEF.md` — product brief: delegation modes, batch modes, event/data model, release gate
- `docs/plans/` — ce-plan implementation plans (Tallyline-imported)
- `ROADMAP.md` — phased delivery
- `DESIGN.md` — interaction contract (observable execution, honest states)

## Current Status

- [x] Repo initialized — scaffold + plan
- [ ] In development
- [ ] Deployed
- [ ] Production traffic

## Active Issues / Known State

- Plan: `docs/plans/2026-11-12-0000-feat-switchboard-v1-plan.md`
- Rune pinned at `anomalyco/rune@24bd7bbb` (dev rev, pre-release — re-pin deliberately, never float)
- OpenCode plugin surface and OpenRouter streaming fields need source-level verification — plan units U3–U4 are spikes for exactly that
- This is NOT an AgentFlow project and NOT an MCP wrapper: the service owns the job, not the plugin

## Agent Instructions (repo-specific)

- The service owns jobs. No host adapter, plugin process, or dashboard may be the durable owner of a run.
- Show observable execution only — streamed output, provider-exposed reasoning where available, tool calls/results. Never fabricate a "thinking transcript."
- Missing telemetry stays `unknown`; requested config and actual reported behavior are separate fields.
- Sanitized records by default; raw payload retention is opt-in. Credentials never enter model-readable workspaces.
- Write-capable delegates get isolated worktrees, never the user's checkout.
- Default: follow duketopceo/luke-agents for all standards.
