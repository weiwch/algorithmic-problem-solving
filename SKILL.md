---
name: algorithmic-problem-solving
description: >-
  Solve, implement, test, debug, and improve concrete bounded algorithmic or
  combinatorial problems. Use whenever the user supplies a contest statement,
  checker/scorer/interactor, partial solver, or WA/TLE/MLE/low-score result and
  wants an exact, constructive, interactive, reactive, scored, randomized,
  heuristic, or hybrid solution, including concrete AHC, ALE-Bench,
  Frontier-CS, ICPC, and OI tasks. Audit the real contract, reason through and
  promptly produce the strongest plausible complete first solution, then
  escalate to exact, heuristic, interactive, checker, research, or engineering
  guidance only after evidence exposes a concrete problem. Also use for a
  contest algorithm/data-structure template with stated operations and bounds.
  Do not trigger from a benchmark name alone, for theory-only explanations, or
  for routine application architecture without a concrete algorithmic
  objective.
---

# Algorithmic Problem Solving

Produce the strongest verified program that fits the actual contract. Route
between exact, constructive, heuristic, interactive, and online methods from
evidence rather than surface labels.

## Non-negotiable rules

1. Read the statement, bounds, config, toolchain, examples, and every supplied
   or directly relevant checker, scorer, interactor, or harness before
   designing.
2. Resolve prose/config/evaluator discrepancies explicitly. When performance is
   equal, prefer the intersection of their legal contracts; otherwise verify the
   exact dependency against the operational evaluator.
3. Keep a valid `champion` separate from experimental candidates. A failed or
   weaker challenger never replaces it; time-bounded search outputs the best
   verified legal state.
4. Unless only an explanation is requested, implement, compile, and test the
   solution. Never claim an experiment that was not run.
5. Make one focused end-to-end solve attempt before optional literature
   research, broad benchmarking, or generic tooling. Think through the
   contract and algorithm, implement and test the strongest plausible complete
   solution, and, when submission is part of the requested workflow, submit it
   promptly. Aim for full correctness or the best realistically near-perfect
   score; do not intentionally stop at a disposable baseline merely to obtain a
   routing signal. Once it passes focused validation, save it as the initial
   `champion` before building auxiliary fallback or checker infrastructure.

## On-demand reference loading

Reference routing is a failure-escalation path, not the default solver-design
phase. Start with this file and the task artifacts, begin the focused first
attempt above, and do not preload references. Drive that attempt through a
complete implementation and prompt submission when it is part of the requested
workflow. Only when the attempt produces a routing signal—during reasoning,
implementation, validation, or remote evaluation—does the listed reference
become required: read it completely before the gated action. The signals are
representative rather than exhaustive. 

| Routing signal | Required reference | Gate before continuing |
|---|---|---|
| The contract, objective, state, or exact-versus-heuristic feasibility boundary remains ambiguous after one focused pass | [modeling](references/modeling-and-exact-methods.md) | Before choosing or implementing the route |
| The model is clear, but the chosen technique fails its complexity, proof, or brute test, or no credible replacement is known | [selection](references/technique-selection.md) | Before selecting the next algorithm |
| A verified legal scored champion stalls, proposals are mostly invalid, evaluation dominates runtime, or moves cannot reach better states | [heuristics](references/heuristic-search.md) | Before parameter tuning or adding another optimizer |
| The interaction state machine, response semantics, counted-query budget, inference, or online policy fails | [interactive](references/interactive-and-online.md) | Before changing protocol or query/action policy |
| Zero/invalid feedback is unexplained, local validation passes but remote evaluation is zero, or local and remote validity/objective disagree | [checker](references/checker-and-local-evaluation.md) | Before changing the solver or adding tests under the unchanged validator |
| With no evaluator contradiction, the smallest counterexample remains unclear, challenger comparisons are flaky, or seed/noise can reverse the decision | [validation](references/validation-and-experiments.md) | Before expanding the test campaign or comparing another challenger |
| Compile/toolchain failure, crash, sanitizer finding, overflow, TLE/MLE, serialization/buffering/deadline failure, or nonzero scored `Wrong Answer` in a query-scored task needs interpretation | [engineering](references/contest-engineering.md) | Before another implementation or submission attempt |

## 1. Contract, first route, and optional research

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

Choose the strongest plausible route that fits the verified contract and
bounds, preferring the simpler route when expected correctness or score is
comparable. State its complexity and critical assumptions, then promptly
implement and test a complete solution. The initial target is a fully solving
or realistically near-perfect champion candidate, not merely a minimal
baseline.

Research papers and public implementations are optional. Use them when the user
asks, a delicate external fact needs verification, or controlled evidence shows
the current algorithm family has stalled on a mature benchmark. Time-box the
research, extract a small number of testable solver changes, and verify every
borrowed assumption against this exact contract.

Before choosing a heuristic, test whether direct structure, graph/flow/matching,
DP, algebra/geometry, decomposition, meet-in-the-middle, bounded search,
SAT/SMT/CP-SAT/ILP, or another exact method fits. Estimate actual state count,
transitions, bytes, test count, and constants.

Choose one route:

- **Exact:** complete worst-case method fits with margin.
- **Constructive:** invariant-preserving legality is the main task.
- **Exact hybrid:** decomposition, bounds, or relaxations retain completeness.
- **Heuristic:** non-optimal output is allowed and exact work does not fit.
- **Scored hybrid:** heuristic global decisions plus bounded exact subproblems.

## 2. Design the solver

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

## 3. Implement and verify incrementally

Build and validate the complete first solver before auxiliary infrastructure:

```text
parser/protocol -> complete first method -> compile -> focused validation
-> first verified champion -> optional fallback/independent checker
-> challenger changes
-> profiling/optimization -> release hardening
```

1. Implement the complete chosen route, not a throwaway baseline.
2. Compile it in the target environment.
3. Run samples, tiny boundaries, degenerate cases, and a brute or independent
   oracle where practical.
4. Validate serialization, hard constraints, raw objective, runtime, and query
   count as applicable.
5. Save the first passing artifact and its evidence as the initial `champion`.

For scored, interactive, or deadline-sensitive tasks, add the simplest
guaranteed-valid fallback after the champion exists when early termination could
otherwise lose all value. For ordinary exact tasks, do not build a separate
baseline unless it materially helps debugging. Keep any fallback distinct from
the champion.

Do not build an independent checker merely because a legal attempt receives a
nonzero low score. When the checker routing row matches, read
[checker-and-local-evaluation.md](references/checker-and-local-evaluation.md)
completely before changing the solver. Treat the resulting checker as a
debugging oracle until independent evidence corroborates it.

After one material change, compile and run the smallest tests that can falsify
its hypothesis. Check apply/undo, cached deltas, reconstruction, serialization,
and boundary behavior. On failure, revert only the challenger and continue from
the champion. Use warnings and sanitizers when supported.

## 4. Lightweight iteration and escalation

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
- Declare a plateau when either:
  - `2-3` controlled submissions improve the champion by less than `1%` of the
    score scale; or
  - the champion remains more than `10%` of the score scale below the
    target after several submissions.
- On a plateau, stop scalar-weight, threshold, and tie-break tuning. Change the
  representation, value model, planning horizon, decomposition, or algorithm
  family. This rule applies at every absolute score.
- Until a verified full score is reached, protect the champion while using the
  available evaluation budget to test materially different methods. Prefer
  falsifiable changes in representation, objective modeling, construction,
  planning, decomposition, or algorithm family over repeatedly polishing one
  route.
- On a plateau, optionally time-box literature/public-code research or
  independent review when the user requests or permits it. Demand ranked
  alternative routes and small falsifiable experiments, not a survey.

Promote only a legal challenger that fits the budget with margin and improves
the relevant fixed evidence. Do not repeatedly optimize against one public set.

## 5. Diagnose and finish

Locate the failing layer before tuning:

- zero/invalid, crash, or protocol error: contract, parser, protocol, invariant,
  reconstruction, checker;
- nonzero score labeled `Wrong Answer` or non-perfect: treat first as legal but
  low-quality feedback unless evidence proves invalid; when the public score
  formula contains a query-count term, use the engineering reference for score
  inversion and query-count diagnosis;
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
