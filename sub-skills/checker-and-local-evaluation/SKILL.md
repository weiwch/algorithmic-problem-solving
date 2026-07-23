---
name: checker-and-local-evaluation
description: >-
  Design, implement, debug, and validate independent C++ checkers, scorers,
  interactors, simulators, and local evaluation workflows for algorithmic and
  competitive-programming tasks. Mandatory after the first non-full candidate
  for every interactive or scored heuristic/optimization task, including AHC,
  before tuning, Plateau Escape, submission, or final delivery. Also use when an
  official evaluator is absent or doubtful, zero or invalid feedback is
  unexplained, local and remote results disagree, output legality or score must
  be reconstructed, or an interactive or reactive solver needs an isolated
  process-level oracle. Pair with testlib-cpp-judging when the evaluator uses
  testlib.h.
---

# Checker and Local Evaluation

Build an evaluator that is independent of the solver, executable under the target toolchain, and adversarially tested. Treat it as a debugging oracle until official artifacts, hand-computed fixtures, or remote evidence corroborate it.

For an interactive or scored heuristic/optimization task, this is a mandatory post-first-candidate implementation gate, not optional tooling, unless terminal official evidence already verifies a full pass or full score. Do not continue tuning, promote a challenger, enter Plateau Escape, submit remotely, or deliver a final result until the local evaluator exists, runs end to end, and passes the validation requirements below.

## Assign ownership before coding

Keep evaluator mechanics separate from solver or policy design:

| Need | Owner |
|---|---|
| Parse a candidate, validate hard constraints, recompute an objective, or transform it into a score | This skill |
| Implement an interactor/simulator state machine, process wiring contract, transcript, or per-step reward replay | This skill |
| Design hidden-state queries or prove hypothesis separation | [interactive problem solving](../interactive-problem-solving/SKILL.md) |
| Design an estimator, planner, explorer, or reward-bearing online policy | [reactive online decision problem solving](../reactive-online-decision-problem-solving/SKILL.md) |
| Select concrete `testlib.h` registration functions, streams, verdicts, and build commands | [testlib C++ judging](../testlib-cpp-judging/SKILL.md) |
| Compare solver challengers, seeds, holdouts, and noisy experiments | [validation and experiments](../validation-and-experiments/SKILL.md) |

For a reactive task, let Reactive own action choice and let this skill own the independent transition/reward replayer or judge-facing harness. Development-time repeated scoring of complete offline outputs is not a live reactive process.

Assign the solver/challenger and evaluator to different owners whenever delegation is available. The solver owner may run the frozen evaluator but must not silently change its contract, fixtures, or acceptance logic to admit a candidate.

## 1. Recover the evaluator contract

Record the operational contract before implementing it:

```text
instance and candidate grammar
hard validity constraints and first-failure policy
raw objective, direction, numeric domain, and aggregation
displayed-score transform, reference data, rounding, and clamps
checker/scorer/interactor arguments, streams, exit statuses, and diagnostics
interactive state, legal actions, feedback order, flush points, budget, and termination
toolchain, time/memory limits, official artifacts, and unresolved discrepancies
```

Read every supplied checker, scorer, interactor, runner, configuration file, and sample that can determine this contract. Distinguish official executable behavior from prose and from assumptions. When they differ, document both and test the smallest witness that separates their interpretations.

Separate these outcomes explicitly:

```text
candidate invalid
candidate valid with raw objective
candidate valid with normalized or partial score
evaluator, configuration, or judge-data failure
```

Never infer a score transform merely from leaderboard behavior.

## 2. Isolate the evaluator from the solver

Implement the evaluator through a separate code path. Do not copy solver feasibility tests, cached-delta formulas, or the solver's favored interpretation before independently writing the evaluator contract and fixtures.

For the mandatory interactive/heuristic gate, delegate evaluator implementation to a dedicated fresh-context sub-agent whenever delegation is available. Do not assign that sub-agent to implement or tune the solver or challenger it will evaluate. Treat this executor-evaluator separation as the default implementation path, not an optional review.

The evaluator sub-agent's initial packet must contain the original problem evidence, not a main-agent reconstruction:

```text
verbatim original statement, title/URL, and exact I/O or protocol format
official constraints, samples, configs, visualizer, scorer, and public rules
original instance data or generator and official runner artifacts
target C++ standard, compiler, flags, and available libraries
only verified filesystem, process, resource, and submission-interface facts
```

Do not replace the original statement or official files with a summary. Initially withhold solver source, candidate identity, solver-derived formulas, attempted-method labels, main-agent reasoning, diagnoses, expected outcomes, claimed scores, and results that lack raw evaluator evidence. A main-agent statement is not a verified fact merely because it is confident or repeated.

Require the evaluator sub-agent to freeze a written contract, source artifact, and initial hand-computed/adversarial fixtures before exposing raw candidate outputs, transcripts, or raw official verdict/score responses. Reveal those later artifacts only when needed for differential validation, with their provenance intact and without the main agent's interpretation. Expose solver internals only after an independently reproduced discrepancy proves they are necessary.

Without delegation, preserve the same information barrier in a sealed pass with separate files, representations, derivations, and fixtures. Do not inspect solver formulas while deriving evaluator formulas. Solver-generated fixtures or agreement between two code paths copied from the same derivation are not independent evidence.

Strongly prefer C++ for every new checker, scorer, interactor, simulator, and process harness. Use the official standard and flags when specified; otherwise use portable C++17. Use another language only when the user requests it, an official artifact must be modified in place, or a required interface makes faithful C++ implementation impractical. Record the concrete exception.

Return executable artifacts rather than only pseudocode:

```text
source paths and component roles
exact build and run commands
small valid, invalid, boundary, and transcript fixtures
VALID/INVALID or protocol verdict with the first actionable failure
raw objective and direction
normalized score only when its transform is established
assumptions and unresolved official/local discrepancies
```

## 3. Choose the evaluator role and interface

Use the smallest role that matches the contract:

| Role | Required behavior |
|---|---|
| Checker | Parse one completed output, reject malformed or illegal candidates, and report the first failing rule |
| Scorer | Establish validity first, recompute the raw objective, then apply only a known score transform and aggregation |
| Interactor/simulator | Reproduce the literal judge state machine over bidirectional pipes and emit a verdict plus transcript |
| Reactive episode evaluator | Validate each action against the pre-action state, apply the transition, recompute reward, and aggregate the episode |
| Runner | Supervise processes, deadlines, exit status, streams, and artifacts without duplicating semantic checks |

For a batch checker or scorer, keep stages explicit and independently testable:

```text
instance = parse_input(...)
candidate = parse_output(...)
require_output_exhausted()
validate(instance, candidate)
raw = objective(instance, candidate)
score = normalize(raw, public_reference_data)  # optional
```

For a non-testlib local tool, a simple interface is sufficient:

```text
local_eval INPUT OUTPUT [ANSWER_OR_CONFIG]
exit 0: valid; stdout contains raw objective and optional established score
exit 1: invalid; stderr contains the first actionable candidate failure
exit 2: evaluator, configuration, or judge-data failure
```

Do not impose this generic exit contract on `testlib.h`. Follow [testlib C++ judging](../testlib-cpp-judging/SKILL.md) for its exact three-file invocation, streams, verdicts, points behavior, and interactor registration.

## 4. Parse and validate by reconstruction

Validate serialization before semantics:

- Check required token and line counts, premature EOF, and the exact trailing-output policy.
- Bound integers, finite floating-point values, characters, enums, and declared lengths before allocation or iteration.
- Check one-based versus zero-based indices, duplicates, missing objects, forbidden extras, and which submitted solution counts.
- Detect overflow while parsing, accumulating, multiplying, or normalizing.
- Use a real parser for expressions, grammars, paths, and operation sequences; avoid substring validation.

Never trust a candidate's claimed score or derived state. Reconstruct the submitted object or replay every operation from the original instance:

- For constructive output, rebuild the witness and test every hard constraint.
- For graphs, check indices, multiplicity, degrees, connectivity, cycles, paths, capacities, and direction as applicable.
- For geometry, check bounds, orientation, contact semantics, overlap, and exact or tolerance-aware predicates.
- For schedules and assignments, recompute resource use, order, coverage, and cross-object constraints.
- For action sequences, validate the whole action against the pre-action state before mutating state.

Use wide integer types for costs, products, squared distances, pair counts, and normalization differences. Reject `NaN` and infinity explicitly.

## 5. Recompute objectives and scores

Answer these questions independently:

1. Is the candidate legal?
2. What is its raw objective or episode reward?
3. How does the evaluator transform and aggregate that value into the displayed score?

Determine minimization versus maximization, integer versus floating arithmetic, the rounding point, clamps, thresholds, piecewise rules, ratios, logarithms, and per-case or per-round aggregation. Establish whether reference data is an optimum, baseline, bound, or jury objective. Define behavior for zero denominators and degenerate equal-reference cases.

Do not assume the displayed score is the fraction of fully passed test cases unless the operational evaluator establishes that equivalence. A case reported as `Wrong Answer` or otherwise non-perfect may still award partial points. Reconstruct each case's raw value or point contribution and the official cross-case weighting and aggregation. Once established, use the final official aggregate score as the solver-comparison and promotion target; use fully passed case count only as a diagnostic or as a proven-equivalent objective.

Keep the raw objective authoritative even when normalization remains unknown. For reactive episodes, recompute transition-local rewards and the final aggregation rather than accepting policy logs; use [reactive online decision problem solving](../reactive-online-decision-problem-solving/SKILL.md) to judge whether the policy itself estimates, plans, or explores well.

## 6. Implement process-level interactors and simulators

Model the same initial output, response order, state transitions, counted actions, special replies, final-answer rules, and termination conditions as the operational judge. Keep the solver unaware of simulator-only truth.

Connect solver stdout to evaluator stdin and evaluator stdout to solver stdin through real pipes or the official runner. Direct function calls cannot expose missing flushes, buffering, premature close, EOF, deadlock, or timeout behavior. Keep response generation, transcript capture, process supervision, and strategy assertions separable.

Test every legal hidden state for tiny sizes, boundary actions, exact budget exhaustion, early success, judge error, malformed messages, timeout, EOF, and transcript agreement with an official interactor when available. Cap deliberately exhaustive behavior to small instances; measure response complexity on large cases so the harness does not become the bottleneck.

Use [interactive problem solving](../interactive-problem-solving/SKILL.md) for query selection and adversarial hypothesis reasoning. Use [reactive online decision problem solving](../reactive-online-decision-problem-solving/SKILL.md) for reward-aware action choice. When `testlib.h` is present, use [testlib C++ judging](../testlib-cpp-judging/SKILL.md) for concrete interactor APIs and flush/wiring requirements.

## 7. Validate the evaluator itself

Create an adversarial evaluator suite:

- one hand-computed valid fixture;
- truncated, extra-token, duplicate, out-of-range, non-finite, and overflow candidates;
- one violating fixture per hard constraint;
- exact equality, contact, and other boundary cases;
- metamorphic cases whose legality or raw objective has a known relationship;
- tiny instances compared with brute enumeration;
- mutation tests that corrupt one field of a valid candidate and require rejection;
- official samples, visualizer output, official evaluator results, or remote feedback when available;
- interactive transcript and real-pipe failure cases for an interactor.

When possible, implement the tiny brute oracle or objective calculator with a different representation from both solver and evaluator. A checker that only agrees with solver-generated fixtures is not independent evidence.

## 8. Integrate the local loop

Compile the evaluator and exercise its command-line or process contract before relying on it. For each challenger, run:

```text
solver -> independent evaluator -> first failure or raw objective delta
```

Record only:

```text
candidate | valid/invalid | raw objective | established score
runtime | first failure | official/local discrepancy | keep/revert
```

If local and official results disagree, stop solver tuning. Minimize the discrepancy, test competing contract interpretations, and change the evaluator or documented assumption before generating more solver cases under the unchanged validator. If local validation passes but official evaluation remains zero, rederive the missing acceptance condition instead of adding random tests that the same incomplete validator will accept.

For the mandatory post-first-candidate gate, record completion only after all applicable items are present and actually run:

```text
independent owner or documented sealed-pass fallback
verbatim original problem packet and evaluator contract
evaluator source plus exact build/run command
hand-computed, malformed, boundary, mutation, and tiny-oracle fixtures
real-pipe transcript tests for interactive tasks
legality and raw-objective recomputation for scored heuristic tasks
official/local comparison where an official artifact or result exists
remaining unknown score transforms or protocol assumptions
```

Do not report the gate complete from pseudocode, a solver-authored validity function, sample-only agreement, or a score estimate.
