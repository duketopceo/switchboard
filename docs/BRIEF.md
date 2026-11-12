# SWITCHBOARD — product and architecture brief

Codename: **SWITCHBOARD** — visible model delegation.

Shared design principle across VEIN, SWITCHBOARD, and VIVA: **simple on the
surface, with every claim, result, and explanation clickable down to its
evidence.**

## The actual product

SWITCHBOARD is not merely "ask another model." It is a persistent delegation
workspace that happens to be accessible from the assistant you are already
using.

The intended interaction:

> "Have Argon plan this, get two other models to critique it independently,
> and keep them running while I work."

The host receives a job identifier immediately. The dashboard shows each
delegate progressing, and results remain available even if the original
conversation moves on.

Treat "Argon" as a user-facing alias that resolves to a verified model
identifier. Do not hard-code a guessed OpenRouter model slug.

## OpenCode first

Four deliverables:

| Deliverable | Responsibility |
|---|---|
| **Local service** | Own jobs, credentials, scheduling, persistence, cancellation, and provider connections |
| **OpenCode integration** | Package selected context, submit jobs, and expose results inside the host |
| **Live dashboard** | Show concurrent runs, outputs, tools, artifacts, usage, and errors |
| **Portable MCP interface** | Let other compatible hosts submit and inspect the same jobs |

The service must own the job — not the plugin process. Otherwise closing a
terminal, changing sessions, or reconnecting the dashboard can destroy the
experience you want.

A skill can explain delegation patterns, but a skill alone should not be
responsible for durable execution.

## Two different delegation modes

**Consult mode:** send selected context to a model for a plan, critique,
explanation, or document. Read-only by default.

**Worker mode:** run a tool-using agent with an explicit working directory,
permissions, command policy, and artifact output.

This distinction matters because calling a model does not automatically give
it a coding harness. For worker mode, an OpenRouter-backed agent runner must
supply the tool loop and execution environment.

Do not make every host adapter responsible for reinventing that runner.

## Define "batch models"

| Mode | Behavior | Useful for |
|---|---|---|
| **Parallel panel** | Same frozen context sent to several models independently | Alternative plans and independent reviews |
| **Task fan-out** | Different bounded tasks assigned concurrently | Research, test analysis, documentation |
| **Pipeline** | One model's artifact becomes another's input | Draft → critique → revision |
| **Deferred batch** | Queued execution without immediate interactivity | Large evaluations or overnight processing |

Build the first two first. Do not assume that a provider's deferred batch API
supports the same streaming or interaction as foreground requests.

For model comparisons, save the context snapshot and show whether models
received identical inputs. Separate independent reviews from reviews
influenced by another model's answer.

## Watching models work

The dashboard should show **observable execution**, not claim to expose
hidden internal thought.

Show streamed output, provider-exposed reasoning where available and
appropriate, tool calls, tool results, searches, file reads, proposed edits,
tests, artifacts, and state transitions. If no reasoning is exposed, display
that honestly; never generate a fake "thinking transcript."

Each delegate gets a live card:

> **Planner · selected model · running**
> Reading context → producing plan → artifact saved
> Elapsed time · usage · estimated/final cost · warnings

Clicking the card opens the full event timeline and input manifest. Keep the
dashboard useful in a narrow side panel so the user can continue working.

## Capture the useful data

| Data group | Store |
|---|---|
| **Identity** | Job, run, parent task, selected model, reported actual model/provider where available |
| **Inputs** | Prompt version, selected files, commit, content hashes, exclusions |
| **Configuration** | Requested reasoning settings, token limits, tools, permissions, routing preferences |
| **Execution** | Queue time, first output, completion, retries, cancellation, tool events |
| **Accounting** | Reported tokens, available cache/reasoning usage, estimated cost, reconciled cost |
| **Artifacts** | Plans, reviews, patches, test logs, structured findings |
| **Evaluation** | Accepted/rejected output, reviewer notes, validation results |

Missing fields should remain **unknown**. Requested configuration and actual
reported behavior should be separate fields, particularly when routing or
fallback occurs.

Retain sanitized records by default; make sensitive raw payload retention
opt-in. "All the data" should not mean silently storing secrets and private
code indefinitely.

## Runtime architecture

Append-only event log with a materialized job state:

```text
Host → submit job → scheduler → OpenRouter/model runner
                         ↓
                  persistent events
                         ↓
              dashboard + host notifications
```

The event stream needs sequence numbers, reconnect/replay, bounded buffers,
and independent states for each child run. A dashboard reconnect should not
resubmit the model request.

Budget controls need per-run and per-job ceilings, concurrency limits, and
approval before paid fallback. Cancellation should distinguish **requested**,
**locally stopped**, and **provider-confirmed**, rather than promising that
all billing stops instantly.

For write-capable delegates, use isolated worktrees or sandboxes and
reviewable patches. Do not let several models concurrently edit the same
checkout.

## Expanding beyond OpenCode

Build around a portable service interface, then add host-specific UX.
Cross-harness compatibility should be tracked as a matrix: tool access,
asynchronous jobs, progress display, notifications, artifact links, and
installation requirements.

Some hosts may offer only submit/status/result access. That is still useful;
it should not be marketed as equivalent to a native live integration.

**First release gate:** launch three delegates, continue using OpenCode,
reconnect the dashboard, inspect the exact inputs, cancel one run, and
retrieve the other two results without losing history.

## Shared engineering, limited coupling

Shared primitives across the three products: evidence reference, version
identity, execution events, inspectable derivation. Domain-specific records
stay separate — no giant shared platform.

A reasonable starting implementation is a local-first service,
SQLite-backed records, a filesystem artifact store, and a web interface —
here realized as a local service plus Rune dashboard. Keep credentials
outside model-readable workspaces and make external transmission of private
repository content explicit.
