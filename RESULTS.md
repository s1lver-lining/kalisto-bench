# Results Recap

A condensed recap of the headline numbers. The paper has the full per-model
tables, the efficiency frontier, and the trap/injection analysis; this page is
the summary a reader needs to interpret the data in this release.

## Scale of the evaluation

| Quantity | Value |
|---|---|
| Scenarios | 10 (7 in Benchmark A, 3 in Benchmark B) |
| Distinct models evaluated | 55, across 12 vendors |
| Total runs | 831 |
| Techniques in the exploit database | 40 (30 used on an attack path) |
| Total spend across priced runs | \$878.45 (723 priced runs; median \$0.30/run) |

## Metrics

- **Coverage (Cov.)** — fraction of a scenario's goal positions the agent
  compromised. `1.00` means every goal `(user, host)` was reached.
- **Attempts / position (Att./pos.)** — tool calls divided by unique positions
  reached; lower is more efficient.
- **Tokens**, **USD/run** — mean cost of a run.

## Benchmark A — leaderboard (top models by coverage)

| Model | Cov. | Att./pos. | Tokens | USD/run |
|---|---|---|---|---|
| Claude Opus 4.6 | 1.00 | 17.0 | 1.2 M | \$1.15 |
| GPT-5.5          | 0.93 | 16.9 | 2.1 M | \$2.70 |
| Claude Opus 4.7 | 0.88 | 13.8 | 1.5 M | \$1.25 |
| Kimi K3         | 0.88 | 17.8 | 1.1 M | \$3.43 |
| Qwen3.8 Max     | 0.85 | 13.9 | 0.9 M | \$1.89 |
| Grok 4.6        | 0.83 | 19.3 | 1.0 M | \$0.73 |
| Claude Sonnet 5 | 0.82 | 21.1 | 2.4 M | \$0.78 |

Across all 46 Benchmark-A models the **mean coverage is 0.45**, with a mean of
**47.9 iterations** used per run. Only **one** model reached full coverage on a
scenario often enough to top the board.

## Difficulty scales with scenario size

| | Small scenarios | Large scenarios |
|---|---|---|
| Goal positions (typical) | 2 | 6 |
| Positions reached (mean) | 1.26 | 3.05 |
| Runs completing the scenario | 50% | 28% |

Agents chain the first one or two steps reliably but stall on the deeper pivots
that larger scenarios require.

## Benchmark B and CasinoLimit

| Benchmark | Models | Mean cov. | Best model | Best cov. |
|---|---|---|---|---|
| B (`big_1`, `big_2`) | 15 | 0.47 | Claude Opus 4.6 | 0.78 |
| `casinolimit`        | 15 | 0.43 | Claude Opus 4.6 | 0.75 |

## Deception (traps) and prompt injection

- **23 of 55 models refused** at least one run (100 refusals total), typically
  hitting a provider content filter mid-engagement.
- On the injection experiment (41 models), placing a deceptive lure in the
  environment dropped mean coverage from **0.82** (baseline) to **0.41**
  (trapped): 19 models lost ground, 14 were wiped out, and **none gained**.

See [`EXAMPLE-RUN.md`](EXAMPLE-RUN.md) for what a single full-coverage run looks
like, and [`example-run/`](example-run/) for the raw report.
