# Benchmark Data Release

Companion data for the paper *"How far do they get?"*. This release lets a
reader inspect every scenario an agent faces, the full vulnerability/technique
catalogue behind them, a recap of the headline results, and a single worked run
end to end.

The benchmark evaluates LLM pentest agents on **10 reproducible scenarios**
deployed with URSID, split
into two benchmarks:

- **Benchmark A** — seven small/medium scenarios (`small_1`–`small_4`,
  `medium_1`–`medium_3`) for broad model coverage.
- **Benchmark B** — three larger scenarios (`big_1`, `big_2`, `casinolimit`)
  that stress multi-step chaining and decoys.

## Contents

| Path | What it is |
|---|---|
| [`scenarios/`](scenarios/) | The raw URSID scenario specification files — one directory per scenario. These are the source of truth: URSID deploys the cyber range directly from them. |
| [`SCENARIOS.md`](SCENARIOS.md) | Human-readable details for every scenario: attack path, decoys, goals, run caps, and the techniques used. |
| [`TECHNIQUES.md`](TECHNIQUES.md) | The full technique catalogue (paper Appendix F): CVE, affected software, and a one-line description for each of the 30 techniques used on an attack path. |
| [`RESULTS.md`](RESULTS.md) | A recap of the headline evaluation results. |
| [`RUNNING.md`](RUNNING.md) | How to deploy and run a scenario with URSID, end to end. |
| [`example-run/`](example-run/) | One complete run report and [`EXAMPLE-RUN.md`](EXAMPLE-RUN.md) walking through it. |

## Scenario specification files

Each `scenarios/<name>/` directory holds the YAML that URSID consumes:

| File | Role |
|---|---|
| `info.yml` | Title, description, and a prose walk-through of the intended attack (where present). |
| `machines.yml` | The machines: OS, accounts, installed software, and the vulnerable/technique components placed on each. |
| `networks.yml` | Subnets and which machine sits on which network (including private/internal networks). |
| `attack_paths.yml` | The **intended** attack path: an ordered list of `(from) → (to)` transitions, each tagged with a MITRE-style `technique` and the concrete `procedure` (technique implementation). |
| `supervision.yml` | The Elastic supervision/detection stack attached to the range (used for the stealth metric). |
| `README.md`, `gen_topology.py` | Present for the larger scenarios that ship their own notes or a topology generator (`big_2`). |

> **Note.** Logos, prebuilt exploit binaries, and test harnesses are omitted from
> this release to keep it a clean specification bundle; they are not needed to
> read or redeploy a scenario.

## Reproducibility

The scenario files here are exactly what URSID deploys, so the scenario tables in
the paper cannot drift from what an agent actually faces. See
[`RUNNING.md`](RUNNING.md) to redeploy any of them.

## Provenance

The scenarios are deployed with URSID, an open-source cyber-range refinement and
deployment tool (see the paper for the citation). The `casinolimit` scenario is
adapted from a publicly played CTF offensive dataset.
