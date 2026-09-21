---
name: interactive-problem-solving
description: >-
  Solve or debug information-acquisition tasks where queries or evaluator
  feedback must reduce uncertainty until a hidden state, answer, or contract is
  sufficiently determined. Use for interactive problems, output-only tasks
  with a checker, and custom-judge workflows when the central difficulty is the
  protocol contract, query design, hypothesis elimination, query budget,
  transcript behavior, noise, or adversarial feedback. Offline score tuning is
  covered by heuristic search; live reward-bearing decisions are covered by
  reactive-online-decision-problem-solving.
---

# Interactive Problem Solving

The information-acquisition cycle is:

```
understand the interaction contract
        -> design a query
        -> observe and update hypotheses
        -> recover the hidden state or final answer
```

The central question is: **What should be asked next?** The main value of an action is the information it reveals.

## Boundary with decision-making

Use this skill when termination means that the answer or relevant hidden state is known well enough. Use [reactive online decision problem solving](../reactive-online-decision-problem-solving/SKILL.md) when termination is a round horizon, task completion, or budget exhaustion and actions must earn immediate or future reward while controlling risk.

For an output-only task with a checker or a custom judge, treat an evaluated candidate as a query only when its verdict, score, or diagnostic is primarily being used to infer hidden acceptance conditions, state, or evaluator behavior. If the contract is already known and the goal is offline repeated score improvement, route to [heuristic search](../../references/heuristic-search.md). Route to [Reactive](../reactive-online-decision-problem-solving/SKILL.md) only when reward-bearing actions receive live feedback that changes later decisions.

## 1. Establish the literal contract

Read the statement and every supplied interactor, tester, checker, scorer, runner, and configuration file relevant to the information channel. Record:

```
initial judge output and ordering
legal query/candidate syntax and parameter bounds
exact deterministic response function or stochastic observation model
counted queries/evaluations/actions and total budget
required flush points and process wiring
special replies: error, -1, success, EOF, timeout
final-answer syntax and whether it consumes budget
state reset or persistence across cases/rounds/submissions
correctness and score dependence on answers, queries, actions, and time
```

Replay sequential updates, rounding, accumulators, and state transitions literally. When prose and executable behavior differ, document both, derive their legal intersection, and use the intersection when it preserves correctness and budget. If a stronger solution depends on executable-only behavior, name and directly test that dependency.

[Checker and Local Evaluation](../checker-and-local-evaluation/SKILL.md) explains independent reconstruction of checker, scorer, and process behavior when those semantics are part of the question.

## 2. Model hypotheses and identifiability

Formalize deterministic interaction as:

```
H_t       hidden states consistent with the transcript through time t
R(h, q)   exact response to query q in hidden state h
H_{t+1} = {h in H_t : R(h, q_t) matches the observed response}
```

For stochastic feedback, maintain likelihoods, posterior weights, or confidence sets rather than eliminating a hypothesis after one mismatch.

Before coding a strategy, test identifiability:

- Enumerate tiny hidden-state spaces and response partitions.
- Search for symmetric or globally indistinguishable states.
- Partition the hidden states into `K` final-answer classes so one same sufficient accepted answer works for every state in a class.
- If every response has at most `b > 1` outcomes, compare the budget with the necessary lower bound `ceil(log_b K)`; use `K = |H|` only when exact hidden-state recovery is required, and account for unbalanced partitions and indistinguishability.
- Prove that the planned transcript separates every pair of classes that require different final answers, not merely sampled states.

Exact state recovery is unnecessary when every remaining hypothesis implies the same accepted answer. State that equivalence explicitly.

## 3. Choose the next query

Derive candidate queries from the exact response semantics. Rank them by the criterion matching the judge:

- minimize the worst-case remaining hypothesis count;
- maximize justified expected entropy or uncertainty reduction;
- distinguish a targeted pair or equivalence class;
- exploit group testing, coding, parity, algebraic cancellation, balanced separators, or batched independent features;
- retain separation margin under noise or adversarial perturbation;
- include query cost when queries have unequal price.

For an adversarial but consistent judge, optimize the worst response partition and preserve the complete feasible set or an equivalent invariant. For known stochastic noise, use likelihood updates, repeated measurements, sequential tests, robust estimators, or error-correcting separation. For unknown noise, reserve calibration queries and avoid brittle hard elimination.

Do not multiply per-query success probabilities without establishing independence or a valid conditional bound.

## 4. Isolate protocol mechanics

Keep the solver's information logic behind a small interface:

```
read_initial()
ask(query) -> response
update(response)
answer(solution)
```

`ask` owns serialization, budget accounting, flushing, reply validation, and immediate termination on judge error or EOF. Keep diagnostics off stdout. Put buffering, deadline, numeric, and target-toolchain fixes in [contest solver engineering](../contest-solver-engineering/SKILL.md); do not mix them into the query-selection proof.

## 5. Validate with a simulator or transcript

A state-machine simulator checks response semantics and strategy invariants. A real-pipe runner additionally checks serialization, buffering, flushing, EOF, process termination, and timeouts. Use [Checker and Local Evaluation](../checker-and-local-evaluation/SKILL.md) for evaluator architecture, [Testlib C++ Judging](../testlib-cpp-judging/SKILL.md) for concrete `testlib.h` APIs and commands, and [Validation and Experiments](../validation-and-experiments/SKILL.md#7-interactive-and-reactive-evaluator-validation) for experiment design.

High-value simulator and transcript cases include:

- exhaustive tiny hidden states;
- exact budget exhaustion and final-answer accounting;
- a worst-case consistent response chooser;
- fixed-seed noisy transcripts when applicable;
- collision search over final remaining hypothesis classes;
- early success, error, EOF, and malformed-reply paths.

The solver must never observe simulator-only truth.

## 6. Safety and termination

- Check the budget before emitting each query or candidate evaluation.
- Keep a legal final answer for every reachable belief state when partial credit or early termination exists.
- Reserve enough budget and time to serialize the answer and complete mandatory protocol steps.
- Use deterministic tie-breaking while debugging.
- Terminate as soon as the remaining hypotheses imply one sufficient answer; do not spend queries merely to identify irrelevant hidden details.

## Diagnostics

| Symptom | Test first |
|---|---|
| Immediate protocol failure | Initial read order, syntax, indexing, flush, special reply |
| Correct tiny cases but query blow-up | Partition balance, symmetry, repeated information |
| Decoder collision | Literal response semantics and global hypothesis enumeration |
| Works in functions but hangs remotely | Real bidirectional pipes, buffering, EOF, timeout |
| Noise causes unstable elimination | Likelihood model, dependence, calibration, robust separation |
| Nonzero feedback but non-perfect result | Whether feedback measures correctness, query count, or quality |
