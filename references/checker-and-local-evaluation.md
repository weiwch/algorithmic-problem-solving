# Independent Checker and Local Evaluation

Use this reference when an official checker/scorer is unavailable and zero or invalid feedback is unexplained, local and remote evaluation disagree, or solver-side validation may share the same bug as the implementation. The local checker is a debugging oracle, not presumed ground truth.

## 1. Independent checker isolation

After an early legal-looking solution receives zero/invalid feedback or a local/remote contradiction and no official checker is available, build the checker independently. Use a fresh sub-agent only when the user requests or applicable instructions permit delegation; otherwise keep a separate checker implementation path and avoid copying solver-derived formulas.

Give the independent checker path or checker agent:

```text
statement and exact I/O format
constraints, samples, config, and public scoring description
candidate input/output artifacts
legal remote feedback already observed
target language and available libraries
```

Initially withhold the solver implementation, cached-delta code, and the main thread's favored explanation. This reduces correlated modeling bugs. If later debugging requires solver details, expose them only after the checker has written its own contract and tests.

The checker should return:

```text
local_check.py or local_check.cpp
VALID/INVALID and the first failing rule
raw objective and direction
estimated score only when its transform is known
assumptions and unresolved discrepancies
small valid, invalid, and boundary fixtures
one concise report to the main thread
```

Keep checker implementation and exploratory logs separate from the solver. Retain the artifact path, contract summary, failing witnesses, and objective results rather than the entire exploration transcript.

## 2. Minimal checker architecture

Keep stages explicit and independently testable:

```text
instance = parse_input(...)
candidate = parse_output(...)
require_output_exhausted()
validate(instance, candidate)
raw = objective(instance, candidate)
score = normalize(raw, public_reference_data)  # optional
```

The command-line contract can remain simple:

```text
local_check INPUT OUTPUT
exit 0: valid; stdout contains raw objective and optional score
exit 1: invalid; stderr contains the first actionable failure
exit 2: checker/configuration failure
```

Do not require JSON, a database, or a generic experiment framework unless the task already needs them.

### Local simulator rule

- The first simulator may be simple, but cap it to small `n`.
- For `n >= 1000`, simulator responses must be near `O(query_size log n)` or the simulator must be written in C++.
- If a local stress run stalls twice, stop and submit a challenger or switch simulator implementation.

## 3. Parsing obligations

Validate the serialization before semantics:

- required token/line counts and premature EOF;
- integer, finite floating-point, character, and enum ranges;
- declared lengths before allocation or iteration;
- one-based versus zero-based indices;
- duplicates, missing objects, and forbidden extras;
- exact trailing-output policy;
- multiple submitted solutions and which one counts;
- overflow while parsing or transforming values.

For structured languages, write a real parser. Do not validate expressions, grammars, paths, or operation sequences with fragile substring checks.

In `testlib`, `ouf.seekEof()` returns a Boolean. Calling it without checking the result does not reject trailing output.

## 4. Validate by reconstruction or replay

Never trust a candidate's claimed score or derived state. Reconstruct the object or replay every operation from the input:

- constructive outputs: rebuild the witness and test every hard constraint;
- graphs: check indices, multiplicity, degrees, connectivity, cycles, paths, capacities, and direction;
- geometry: check bounds, orientation, contact semantics, overlap, and exact or tolerance-aware predicates;
- schedules/assignments: recompute resource use, order, coverage, and all cross-object constraints;
- action sequences: validate a whole action against the pre-action state, then mutate state;
- interactive tasks: use a process-level simulator rather than a batch checker.

Use wide integers for accumulated costs, products, squared distances, pair counts, and normalization differences. Reject `NaN` and infinity explicitly.

## 5. Objective and score

Separate three questions:

1. Is the output legal?
2. What is its raw objective?
3. How is that objective transformed into the displayed score?

Recompute the raw objective independently even if the exact remote score normalization is unknown. Determine:

- minimize or maximize;
- rounding point and integer versus floating arithmetic;
- clamps, thresholds, piecewise rules, ratios, and logarithms;
- per-case aggregation: sum, mean, minimum, product, or whole-run failure;
- reference-answer use and whether it supplies an optimum, baseline, or bound;
- behavior when `best == baseline` or another denominator is zero.

Label an unknown transform as unknown. Do not fabricate a normalized score from leaderboard intuition.

## 6. Validate the checker

A checker needs adversarial tests too:

- one hand-computed valid fixture;
- truncated, extra-token, duplicate, out-of-range, and overflow outputs;
- one fixture per hard constraint;
- exact boundary contact/equality cases;
- metamorphic cases whose legality or raw objective must be preserved;
- tiny instances compared with brute enumeration;
- mutation tests: corrupt one field of a valid output and require rejection;
- comparison with samples, an official visualizer, or later remote feedback.

When possible, implement the tiny brute oracle or objective calculator using a different representation from both solver and checker.

## 7. Lightweight use in the main loop

For each challenger:

```text
run solver -> run local checker -> inspect first failure or raw delta
```

Save only:

```text
candidate | valid/invalid | raw objective | runtime | first failure | keep/revert
```

If the local checker and remote feedback disagree, do not tune the solver yet. Ask the checker agent to isolate the smallest discrepancy, then test competing contract interpretations with a minimal witness.

If local validation passes but remote evaluation is zero, do not add more random tests under the unchanged validator; such tests cannot reveal a missing validator condition. Re-audit or independently rederive the validation contract, then change the validator or test a competing interpretation before generating more solver cases.
