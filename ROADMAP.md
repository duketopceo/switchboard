# ROADMAP.md — switchboard

Phased delivery per `docs/BRIEF.md`. Each phase ends at a verifiable gate;
nothing ships on a build alone.

## Phase 0 — Execution core (plan v1, units U1–U5)

- Local service: job ownership, SQLite append-only event log, materialized
  state, scheduler, cancellation, budget ceilings
- OpenRouter runner: consult mode (read-only) + parallel panel batch mode
- Rune dashboard: job list, live delegate cards, event timeline, input
  manifest
- Portable MCP interface (submit/status/result)

**Gate:** three delegates launch, the host keeps working, dashboard
reconnects without resubmission, exact inputs inspectable, one run cancelled,
two results retrieved with history intact.

## Phase 1 — OpenCode integration (units U6–U7)

- OpenCode plugin: package selected context, submit jobs, surface results
  inside the host
- Model alias resolution to verified OpenRouter identifiers (no guessed slugs)
- Notification path back to the host on completion

**Gate:** "Have Argon plan this, get two other models to critique it" works
end-to-end from inside OpenCode while the user continues working.

## Phase 2 — Worker mode + fan-out (units U8–U9)

- Worker mode: tool-using agent runner with working directory, permission and
  command policy, artifact output
- Isolated worktrees per write-capable delegate; reviewable patches
- Task fan-out batch mode
- Usage/cost reconciliation and evaluation records (accepted/rejected,
  reviewer notes)

**Gate:** a worker delegate produces a patch in its own worktree; a second
delegate's critique can be requested without either touching the user's
checkout.

## Phase 3 — Pipeline + deferred batch (units U10–U11)

- Pipeline mode: one model's artifact becomes another's input
- Deferred batch queue for large evals / overnight runs
- Cross-harness compatibility matrix (tool access, async jobs, progress,
  notifications, artifact links, install requirements)

## Explicitly deferred

- Every-harness native integrations (matrix tracks honest capability tiers)
- Provider batch APIs assumed equivalent to foreground streaming
- Multi-user or hosted service — local-first single operator
