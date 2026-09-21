---
name: validation-and-experiments
description: >-
  Design the smallest falsifying test loop for an algorithmic solver, including
  brute oracles, differential and metamorphic tests, champion/challenger
  comparisons, paired seeds, holdouts, resource probes, and final checks. Use
  when the smallest counterexample is unclear, comparisons are noisy, a solver
  change needs evidence, or correctness, robustness, runtime, and memory need
  focused validation.
---

# Validation and Experiments

Use the smallest test loop that can falsify the current algorithmic hypothesis. Do not begin with a generic benchmark platform or a large campaign.

## Topic boundaries

This guide covers oracle choice, counterexample search, controlled candidate comparison, stochastic evidence, and release checks. Adjacent topics are:

- [checker and local evaluation](../checker-and-local-evaluation/SKILL.md) to implement or repair an independent checker, scorer, interactor, simulator, or process harness;
- [model and route algorithms](../model-and-route-algorithms/SKILL.md) when evidence contradicts the contract, state, reduction, proof, or route class;
- [contest solver engineering](../contest-solver-engineering/SKILL.md) for compiler, overflow, memory-layout, I/O, or measured deadline failures;
- the shared [heuristic search](../../references/heuristic-search.md) for representation, moves, deltas, reachability, and optimizer mechanics.

For generated inputs, define the intended coverage family and oracle relationship, parameterize the generator, validate intended-valid cases independently, and retain the command or seed needed to reproduce failures. [Checker and Local Evaluation](../checker-and-local-evaluation/SKILL.md#build-problem-finding-data-end-to-end) gives the complete local workflow; [Testlib C++ Judging](../testlib-cpp-judging/SKILL.md) gives Testlib generator and validator APIs.

## 1. Evidence ladder

A useful progression from cheap, local evidence to broader evidence is:

1. target-language compile with the official flags;
2. samples and hand-computed examples;
3. empty/minimal, maximum, equality, duplicate, and overflow boundaries;
4. one case for each hard constraint and failure mode;
5. brute or independent oracle on tiny instances;
6. randomized or metamorphic tests;
7. representative runtime and memory probes;
8. official evaluator or legal remote feedback.

The first failing layer usually provides more information than a larger campaign built on top of it.

## 2. Exact and constructive problems

For an exact solver, prefer a tiny obviously correct oracle over a second optimized implementation. Generate cases small enough for full enumeration and compare the complete answer or a canonical witness.

Check:

- empty/nonempty distinctions and impossible cases;
- equality, duplicates, disconnected components, and symmetric solutions;
- reconstruction independently from the optimum value;
- overflow before narrowing and exact division/modulo semantics;
- dense and sparse extremes;
- the first size where an optimization changes behavior.

For constructive output, validate syntax, hard constraints, and final serialization independently. A promised-feasible input does not prove the construction reaches a valid witness.

When a mismatch appears, minimize it and identify the first violated model or proof premise before changing downstream implementation details.

## 3. Scored solvers

Three artifact roles help prevent scored-solver regressions:

```
fallback: simplest guaranteed-valid emergency output
challenger/current: mutable experimental candidate
champion: best independently validated legal artifact
```

Before comparing scored candidates:

- validate every output;
- recompute the true raw objective;
- compare incremental deltas with full recomputation;
- test apply/undo round trips;
- confirm score direction and any clamp, threshold, or normalization;
- verify the internal deadline includes a safety margin.

Use Checker and Local Evaluation when the official checker/scorer is absent or doubtful and that uncertainty blocks the current decision. Do not compare solver quality under an evaluator whose contract is still contradicted by official or hand-computed evidence.

## 4. Champion/challenger comparison

A compact comparison record is:

```
champion | hypothesis | one change | fixed cases/seeds
validity | per-case score/runtime delta | keep/revert | next action
```

Useful comparison ingredients are:

- a few representative development cases;
- the same small set of solver seeds for champion and challenger;
- one untouched boundary or holdout set;
- saved failing inputs and outputs;
- the full generator parameter tuple, instance seed, validator result, and input hash for every generated case;
- per-case results, not only an aggregate.

Promote only when the challenger stays legal, fits the budget with margin, and improves the relevant fixed evidence. Revert the challenger otherwise.

## 5. Randomness and holdouts

Keep randomness sources distinct:

```
instance seed | solver seed | judge/noise seed | host/runtime noise
```

Pair champion and challenger on the same inputs and allowed noise seeds. Tune on development cases, then check an untouched set. Do not repeatedly select against one public set or one lucky solver seed.

Three to five fixed seeds are usually enough to reject a clearly weak idea. Expand only when results are close, variance is material, or remote scoring is noisy.

## 6. Metamorphic tests

When an exact oracle is hard, transform an instance in a way with a known effect:

- permute labels or input order when semantics are invariant;
- add an isolated or dominated object with predictable behavior;
- scale or translate geometry when the contract preserves it;
- duplicate independent components and combine their objectives;
- reverse, rotate, or complement only when the contract has that symmetry.

Verify the expected relationship, not necessarily a fixed output string.

## 7. Interactive and reactive evaluator validation

A process-level simulator, interactor, reactive episode replayer, scorer, real-pipe runner, or transcript checker can test protocol and transition semantics. [Checker and Local Evaluation](../checker-and-local-evaluation/SKILL.md) covers evaluator implementation and adversarial fixtures; [Testlib C++ Judging](../testlib-cpp-judging/SKILL.md) covers concrete `testlib.h` APIs.

Pin build/run commands and evaluator versions, compare strategies on the same hidden states and noise seeds, retain failing transcripts, and report protocol failure separately from legality, reward, runtime, and strategy quality. Corroborate a local simulator with hand-computed cases, official artifacts, or another independent source before treating its score as authoritative.

## 8. When heavier evidence is justified

Use larger seed sets, quantiles, confidence intervals, or formal benchmark automation only when:

- stochastic variance can reverse the decision;
- candidates differ by a small margin;
- failures have expensive lower-tail risk;
- the work is a long-running solver project rather than one problem;
- a formal report requires reproducible aggregate statistics.

Even then, begin with paired per-case deltas and explicit invalid/timeout counts. Do not let statistical machinery hide contract or checker errors.

## 9. Failure-directed next action

| Symptom | Next route or check |
|---|---|
| Invalid output | Parser, bounds, reconstruction, checker assumptions |
| Exact mismatch | Model-and-route: smallest brute counterexample and proof premise |
| TLE/MLE | Contest-solver-engineering: measured hot path, state count, copying, allocation |
| Legal but very low score | Objective direction, external reference/bound gap, representation, construction |
| Local search plateau | Delta correctness, reachability, compound moves |
| Seed-sensitive gain | Per-instance deltas, outliers, overfit |
| Local/remote disagreement | Checker contract and score transform |

For a structurally weak method, [Escaping Search Plateaus](../plateau-escape/SKILL.md) maps representation, neighborhood, decomposition, objective, and algorithm-family alternatives.

## 10. Final checks

Before delivery, check the applicable items:

- rebuild the champion cleanly;
- rerun samples and saved regressions;
- rerun the relevant independent oracle or checker suite;
- validate the final output or transcript;
- probe worst or representative runtime and memory;
- confirm deterministic seeds and release flags;
- ensure the delivered artifact is the champion, not the latest edit.
