# Lightweight Validation and Iteration

Use the smallest test loop that can falsify the current algorithmic hypothesis. Do not begin with a generic benchmark platform.

## 1. Validation ladder

Build evidence in this order:

1. target-language compile with the official flags;
2. samples and hand-computed examples;
3. empty/minimal, maximum, equality, duplicate, and overflow boundaries;
4. one case for each hard constraint and failure mode;
5. brute or independent oracle on tiny instances;
6. randomized or metamorphic tests;
7. representative runtime and memory probes;
8. official evaluator or legal remote feedback.

Stop and fix the first failing layer before running a larger campaign.

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

## 3. Scored solvers

Always keep:

```text
fallback: simplest guaranteed-valid emergency output
challenger/current: mutable experimental candidate
champion: best independently validated legal artifact
```

Before tuning quality:

- validate every output;
- recompute the true raw objective;
- compare incremental deltas with full recomputation;
- test apply/undo round trips;
- confirm score direction and any clamp, threshold, or normalization;
- verify the internal deadline includes a safety margin.

Read [checker-and-local-evaluation.md](checker-and-local-evaluation.md) when the official checker/scorer is absent or doubtful.

## 4. Minimal champion/challenger loop

Record one line per material attempt:

```text
champion | hypothesis | one change | fixed cases/seeds
validity | per-case score/runtime delta | keep/revert | next action
```

Use a direct shell command or a short task-specific script. Default to:

- a few representative development cases;
- the same small set of solver seeds for champion and challenger;
- one untouched boundary or holdout set;
- saved failing inputs and outputs;
- per-case results, not only an aggregate.

Promote only when the challenger stays legal, fits the budget with margin, and improves the relevant fixed evidence. Revert the challenger otherwise.

## 5. Randomness and holdouts

Keep randomness sources distinct:

```text
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

## 7. When heavier evidence is justified

Use larger seed sets, quantiles, confidence intervals, or formal benchmark automation only when:

- stochastic variance can reverse the decision;
- candidates differ by a small margin;
- failures have expensive lower-tail risk;
- the work is a long-running solver project rather than one problem;
- a formal report requires reproducible aggregate statistics.

Even then, begin with paired per-case deltas and explicit invalid/timeout counts. Do not let statistical machinery hide contract or checker errors.

## 8. Failure-directed next action

Map evidence to the next layer:

| Symptom | Next check |
|---|---|
| Invalid output | parser, bounds, reconstruction, checker assumptions |
| Exact mismatch | smallest brute counterexample and proof assumption |
| TLE/MLE | measured hot path, state count, copying, allocation |
| Legal but very low score | objective direction, external reference/bound gap, representation, construction |
| Local search plateau | delta correctness, reachability, compound moves |
| Seed-sensitive gain | per-instance deltas, outliers, overfit |
| Local/remote disagreement | checker contract and score transform |

If the current route appears structurally weak but the plateau criteria are not yet met, test one falsifiable structural challenger. Once the criteria are met, return to [the mandatory fresh-context plateau escape](../SKILL.md#mandatory-fresh-context-plateau-escape) before choosing another model, representation, or algorithm family. Do not answer every failure with another parameter or bypass the plateau gate by expanding the test campaign.

## 9. Release gate

Before delivery:

- rebuild the champion cleanly;
- rerun samples and saved regressions;
- run the smallest independent oracle/checker suite;
- validate the final output or transcript;
- probe worst or representative runtime and memory;
- confirm deterministic seeds and release flags;
- ensure the delivered artifact is the champion, not the latest edit.
