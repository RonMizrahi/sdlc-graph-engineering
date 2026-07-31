# SDLC Graph Engineering

Installs **graph engineering** into a project that has none.

Point it at whatever process you already have — a set of skills, a runbook, a CI config, or just a
description — and it produces a **guarded graph** for that process: typed nodes, total exit guards,
bounded retry loops, a durable run-state file, a ledger of every step that could not run, the
orchestrator that walks it, an auditor that checks it, and an eval suite that keeps it honest.

**Domain-agnostic and self-contained.** It needs no other plugin installed and assumes nothing about
software delivery. Its worked examples deliberately use a document-review pipeline.

## Why

A prose process fails in ways prose cannot describe:

| | |
|---|---|
| **No run state** | Nothing records where it got to. An interruption restarts from guesswork. |
| **Unevaluated conditions** | "retry if it's worth retrying" is a condition nothing checks. |
| **Unbounded loops** | A retry with no declared limit runs until something else stops it. |
| **No record of what was skipped** | "Never claim a step you didn't run" is unenforceable if nothing records the absence. |

## What it produces

```
<target>/
├── agents/<graph>-monitor.md      the auditor — read-only, reports, never gates
└── skills/<graph>/
    ├── SKILL.md                   the orchestrator: dispatch loop, rules, human stops
    ├── references/
    │   ├── nodes.md               node contracts + diagrams
    │   ├── edges.md               the transition table, bounds, terminal states
    │   └── state.md               run-state schema, write points, resume rules
    └── evals/                     the dry-traces, made executable — runs on every edit
```

## The method

Nine steps, and **step 4 carries the weight** — making the guards *total*. The rest is drawing boxes.

The trap it names: guard sets get written exhaustive over the **happy predicates** but not over their
**product**. A result with three independent properties has eight combinations; the three obvious
guards cover four. Reading a spec top-to-bottom never reveals the gap, because each guard looks
correct in isolation.

It finishes by **dry-tracing three scenarios** — happy path, a retry that recovers, a retry that
exhausts its bound — and then **emitting those traces as an executable suite**, because a paper trace
protects exactly one version of the graph.

## Skills

| Skill | Invocation |
|---|---|
| `sdlc-graph-engineering-install` | `sdlc-graph-engineering:sdlc-graph-engineering-install` |

## References it ships

- **`templates.md`** — skeletons for all five files, with the fields that matter already present
  rather than left to be remembered: `exits`, typed human stops, per-item attempt keys, the
  append-only ledger.
- **`evals.md`** — the eval suite in full: fixture format, how to write a check that reads as a
  diagnosis, what belongs in a negative control, and the scoping trap (a suite may only read files
  inside its own plugin, so the surfaces that *mirror* the graph stay out of its reach).
- **`failure-modes.md`** — the **16** ways these graphs break at runtime, each with its published name
  so a defect is searchable rather than folkloric: workflow-net **soundness**, **sequence
  manipulation** / **MAST FM-3.2**, ping-pong livelock, the re-execution hazard, silent structural
  skip, **MAST FM-1.4**, **OWASP ASI01**, the zombie-task stall, the gate that runs after the point of
  no return, the surface that lies about the graph, the stop that fires where the human said
  *continue*, and the obligation whose trigger is a judgement call. Plus a *what not to monitor* list,
  because a checker that fires constantly gets ignored.

## Example

See the example graph in the [repository README](../../README.md#example-output).
