---
name: algorithmic-problem-solving
description: >-
  Solve, implement, test, debug, and improve concrete bounded algorithmic or
  combinatorial problems. Use whenever the user supplies a contest statement,
  checker/scorer/interactor, partial solver, or WA/TLE/MLE/low-score result and
  wants an exact, constructive, interactive, reactive, scored, randomized,
  heuristic, or hybrid solution, including concrete AHC, ALE-Bench,
  Frontier-CS, ICPC, and OI tasks. Audit the real contract, research established
  methods for mature benchmark families, test exact feasibility, build an
  independent local checker when needed, implement, and verify the strongest
  justified approach. Also use for a contest algorithm/data-structure template
  with stated operations and bounds. Do not trigger from a benchmark name alone,
  for theory-only explanations, or for routine application architecture without
  a concrete algorithmic objective.
---

# Algorithmic Problem Solving

Produce the strongest verified program that fits the actual contract. Route
between exact, constructive, heuristic, interactive, and online methods from
evidence rather than surface labels.

## Non-negotiable rules

1. Read the statement, bounds, config, toolchain, examples, and every legitimately
   available checker, scorer, interactor, or harness before designing.
2. Resolve prose/config/evaluator discrepancies explicitly. When performance is
   equal, prefer the intersection of their legal contracts; otherwise verify the
   exact dependency against the operational evaluator.
3. Keep a valid `champion` separate from experimental candidates. A failed or
   weaker challenger never replaces it; time-bounded search outputs the best
   verified legal state.
4. Unless only an explanation is requested, implement, compile, and test the
   solution. Never claim an experiment that was not run.

## Mandatory reference loading

When a condition below matches, **MUST open and read the complete named
reference before proceeding**. Matching rows are cumulative; never rely only on
this summary or prior memory.

| Condition | Required reference |
|---|---|
| Formalizing or choosing an algorithmic route | [modeling](references/modeling-and-exact-methods.md), [selection](references/technique-selection.md) |
| Mature literature or standard benchmark | [selection](references/technique-selection.md) |
| Scored, heuristic, randomized, or low-score work | [heuristics](references/heuristic-search.md) |
| Interactive, reactive, online, or noisy feedback | [interactive](references/interactive-and-online.md) |
| Checker absent/doubtful, early low score, or local/remote mismatch | [checker](references/checker-and-local-evaluation.md) |
| Testing, debugging, or comparing attempts | [validation](references/validation-and-experiments.md) |
| Implementing, optimizing, or preparing release code | [engineering](references/contest-engineering.md) |

## 1. Contract, research, and route

Build this compact internal contract before coding; do not ask for confirmation
when the supplied artifacts already determine it:

```text
decision/state | scale/distribution | hard validity
raw objective/direction | score transform/aggregation
batch/constructive/interactive/reactive protocol and budget
time/memory/language/toolchain | available evaluator tools
failure semantics | unknown assumptions
```

Separate legality from quality. For interactive work, model the literal state
machine, including flushes, counted queries, error responses, and EOF.

For a mature problem with established literature or standard
benchmarks, **MUST research before committing to a solver design**:

- find classical methods, surveys, current competitive approaches, benchmark
  rules, and legitimate public implementations;
- prefer papers, official documentation, and maintained source repositories;
- extract reusable formulations, bounds, neighborhoods, and engineering ideas;
- verify assumptions, license, dependencies, and compatibility with this exact
  contract; never import hidden outputs or benchmark-specific leakage.

Before choosing a heuristic, test whether direct structure, graph/flow/matching,
DP, algebra/geometry, decomposition, meet-in-the-middle, bounded search,
SAT/SMT/CP-SAT/ILP, or another exact method fits. Estimate actual state count,
transitions, bytes, test count, and constants rather than quoting a coarse
operations-per-second rule.

Choose one route:

- **Exact:** complete worst-case method fits with margin.
- **Constructive:** invariant-preserving legality is the main task.
- **Exact hybrid:** decomposition, bounds, or relaxations retain completeness.
- **Heuristic:** non-optimal output is allowed and exact work does not fit.
- **Scored hybrid:** heuristic global decisions plus bounded exact subproblems.

## 2. Independent validation and baseline

Create the smallest useful feedback loop:

1. compile a minimal program in the target environment;
2. run samples, tiny boundaries, degenerate cases, and a brute oracle where
   practical;
3. establish the simplest correct or guaranteed-valid baseline;
4. save its output, validity, raw objective, score, runtime, or query count.

An official checker will often be unavailable. If an early legal-looking
attempt receives an unexpectedly low or zero score, **MUST delegate a fresh
sub-agent to derive and implement an independent local checker/scorer**.

- Give it the statement, formats, bounds, samples, config, and legal submission
  feedback, but initially withhold solver code and solver-derived formulas.
- Require separate `parse`, `validate/replay`, `objective`, and optional `score`
  stages plus small hand-computed valid and invalid fixtures.
- It returns only the checker artifact, assumptions, first failing witness,
  raw-objective result, and concise discrepancies to the main thread.
- Treat it as a debugging oracle until samples, mutation tests, brute cases, or
  remote feedback corroborate it. Never expose hidden data to either agent.

## 3. Design the solver

### Exact or constructive

- Define state, invariant, recurrence/reduction, base cases, and reconstruction.
- Prove coverage and preservation; try to falsify assumptions by tiny exhaustive
  enumeration.
- Check overflow, equality/duplicates, indexing, recursion, allocation, and
  numerical predicates.
- Prefer the simplest method that meets the real bounds.

### Scored batch or hybrid

- Represent the optimized structure, not only serialized output tokens.
- Preserve feasibility by construction or use bounded repair plus the independent
  checker.
- Keep `current` and valid `best` separate; rank `best` by the true objective.
- Derive construction and small/medium/large moves before selecting hill
  climbing, SA, tabu, beam, LNS, evolutionary search, or a portfolio.
- At a local optimum, derive the smallest coordinated improving move from
  objective/constraint algebra before adding a generic escape mechanism.
- Verify incremental deltas against full recomputation and use a monotonic
  internal deadline with margin.

### Interactive, reactive, or uncertain

- Define hidden hypotheses and the literal response/transition function.
- Derive information or query requirements and separate remaining hypotheses.
- Isolate protocol I/O, budget counting, flushing, parsing, and termination.
- For online control, separate `observe -> update -> legal action -> act`, track
  uncertainty, and retain a legal fallback policy.
- Test exhaustive small states, adversarial transcripts, and real pipe behavior.

## 4. Implement and verify incrementally

Keep the program runnable:

```text
parser/protocol -> valid baseline -> checker/oracle -> one verified transition
-> full method -> profiling/optimization -> release hardening
```

After one material change, compile and run the smallest tests that can falsify
its hypothesis. Check apply/undo, cached deltas, reconstruction, serialization,
and boundary behavior. On failure, revert only the challenger and continue from
the champion. Use warnings and sanitizers when supported.

## 5. Lightweight iteration and escalation

Default to a short note, not a research platform:

```text
champion | hypothesis | one change | fixed cases/seeds
validity | score/runtime delta | keep/revert | next action
```

Use direct commands or a short problem-specific loop, a few fixed representative
cases/seeds, per-case deltas, and saved failures. Add larger seed sets, holdouts,
or statistical analysis only when feedback is noisy, candidates are close, or
the task genuinely requires long-horizon benchmarking.

- With no submission penalty, make controlled repeated submissions: one
  hypothesis per submission, record the artifact/result, and retain the best.
- If several valid submissions remain around `0-30` on a `0-100` scale, stop
  parameter tuning and change the algorithm family, representation,
  decomposition, construction, or neighborhood.
- When sustained controlled attempts show no meaningful improvement, **MUST
  invoke a fresh sub-agent** to independently summarize the trajectory and
  conduct thorough research into literature and public implementations. Give it
  the contract, champion, measurements, and failed hypotheses, but no required
  next method. It must return ranked alternative routes, sources or derivations,
  and small falsifiable experiments to the main thread.

Promote only a legal challenger that fits the budget with margin and improves
the relevant fixed evidence. Do not repeatedly optimize against one public set.

## 6. Diagnose and finish

Locate the failing layer before tuning:

- invalid/WA: contract, parser, protocol, invariant, reconstruction, checker;
- exact mismatch: model, proof assumption, overflow, brute counterexample;
- TLE/MLE: measured states, copying, allocation, rescoring, tail work;
- legal low score: objective direction, baseline gap, representation,
  construction, move reachability, proxy alignment;
- stalled search: true improving-event rate, delta bugs, weak compound moves;
- public-only gains: seed overfit, noise, outliers, or instance shift;
- interactive/online failure: state semantics, flush/EOF, belief update,
  exploration value, or horizon.

Before delivery, compile a clean release build, run targeted regressions and
small differential tests, validate the final output/transcript, check runtime
margin, and ensure the delivered artifact is the champion. Do not narrate the
internal worksheet or request extra confirmation when the task can be completed
from available evidence; report only the implementation, tests actually run,
and material residual risk.
