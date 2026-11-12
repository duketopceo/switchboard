# DESIGN.md — switchboard

Design contract for the switchboard interface. The shared principle across
VEIN, SWITCHBOARD, and VIVA: **simple on the surface, with every claim,
result, and explanation clickable down to its evidence.**

## What the dashboard shows

Observable execution — never claimed internal thought:

- Streamed output and provider-exposed reasoning where available and
  appropriate
- Tool calls, tool results, searches, file reads, proposed edits, tests
- Artifacts (plans, reviews, patches, test logs, structured findings)
- State transitions: queued → running → completed / failed / cancelled

If a provider exposes no reasoning, the UI says so. No fake "thinking"
transcript, ever.

## Delegate card

Each run gets a live card:

> **Planner · <model alias> · running**
> Reading context → producing plan → artifact saved
> Elapsed · usage · est/final cost · warnings

Clicking the card opens the full event timeline and the input manifest —
exactly what the model was shown (prompt version, files, commit, hashes,
exclusions).

The dashboard must stay useful in a narrow side panel so the user keeps
working. Host conversation continuity is never required: closing the
terminal or switching sessions must not destroy a run.

## Honest accounting

| Shown | Rule |
|---|---|
| Cost | estimated vs reconciled reported separately; `unknown` when unreported |
| Cancellation | requested → locally stopped → provider-confirmed are distinct states |
| Config | requested settings vs actual reported behavior are separate fields |
| Context | identical-input comparisons save the context snapshot hash |

## Batch honesty

Parallel panel and task fan-out show whether delegates received identical
inputs. Independent reviews are visually separated from reviews influenced by
another model's output. Deferred/queued batches are labeled as such — no
implied streaming or interactivity.

## UI stack

Rune (`anomalyco/rune`, pinned rev in `Cargo.toml`) + `rune-kit` components.
`rune-kit` was built with OpenCode's component vocabulary — prefer its
primitives for host-adjacent UI. Headless rendering (PNG + element-tree dump)
is the UI verification path.
