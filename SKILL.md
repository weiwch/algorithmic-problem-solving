---
name: algorithmic-problem-solving
description: >-
  Recover a concrete bounded algorithmic or combinatorial solve after the agent
  has already independently designed, implemented, and tested the strongest
  plausible complete solution and that focused end-to-end attempt failed. Use
  for unresolved WA, TLE, MLE, crashes, proof or brute-force counterexamples,
  invalid output, checker/scorer disagreement, unexplained zero or low score,
  stalled heuristic search, interactive protocol or hidden-state inference
  failure, weak reactive/online decisions, or a contest template that fails its
  stated operations and bounds. Do not invoke for a fresh or straightforward
  problem that the model can solve directly: first aim to write the expected
  fully correct, complete, or realistically near-optimal solution without this
  recovery workflow.
---

# Algorithmic Problem-Solving Recovery

Diagnose the failed layer, preserve verified work, and make the smallest evidence-backed change that can recover a complete or materially stronger solution.

## Positioning

This is a recovery router, not the default way to solve every contest problem. The focused end-to-end solve is an activation prerequisite and happens outside this skill. If no concrete failure evidence exists, leave this workflow and solve the problem directly.

Once activated, do not restart reflexively. Reconstruct the actual contract and the failed attempt from artifacts, then reuse every verified component that is not implicated by evidence.

Use artifact terms consistently:

```text
fallback   simplest guaranteed-valid emergency output
challenger experimental candidate evaluated against the champion
champion   best independently validated legal artifact
baseline   external scoring/evaluation reference, never a solver artifact
```

## Recovery snapshot

Collect only the facts needed to reproduce and localize the failure:

```text
statement, bounds, and operational evaluator contract
attempted model, algorithm, proof assumptions, and complexity
source/build/run commands and target toolchain
smallest failing input, transcript, or scored instance
expected versus observed result, verdict, score, runtime, and memory
available checker, scorer, interactor, simulator, logs, and submission budget
champion/fallback/challenger paths and remaining uncertainty
```

Read every supplied artifact that is directly relevant to the failing layer. Resolve prose/config/evaluator discrepancies explicitly. Prefer their legal intersection when it has no material cost; otherwise record and test any dependency on the operational evaluator.

## Classify before changing code

| Evidence | Failing layer to test first | Route |
|---|---|---|
| Contract meaning, state, invariant, recurrence, reduction, or proof is questionable, or brute force finds a mismatch | Problem model and correctness | [model and route algorithms: model repair](sub-skills/model-and-route-algorithms/SKILL.md#1-normalize-the-operational-contract) |
| The model is trusted, but the exact/constructive/hybrid/heuristic boundary is unclear under the real bounds | Feasibility and route choice | [model and route algorithms: feasibility](sub-skills/model-and-route-algorithms/SKILL.md#5-establish-the-operation-and-memory-envelope) |
| The route is known, but complexity, technique assumptions, or data-structure behavior fails | Algorithm selection | [technique selection](references/technique-selection.md) |
| Invalid/zero feedback or local and remote evaluation disagree | Serialization, hard constraints, objective, checker fidelity | [checker and local evaluation](sub-skills/checker-and-local-evaluation/SKILL.md) |
| No evaluator contradiction, but the smallest counterexample is unclear or comparisons are noisy | Experiment design and oracle quality | [validation and experiments](sub-skills/validation-and-experiments/SKILL.md) |
| Compile failure, crash, sanitizer finding, overflow, TLE/MLE, buffering, or deadline failure | Implementation and environment | [contest solver engineering](sub-skills/contest-solver-engineering/SKILL.md) |
| A legal scored solver has weak construction, moves, deltas, reachability, or optimizer behavior | Search mechanics | [heuristic search](references/heuristic-search.md) |
| A verified legal champion has reached a measured quality plateau | Representation, value model, horizon, decomposition, or method family | [plateau escape](sub-skills/plateau-escape/SKILL.md) |
| The main value of an action is to obtain information and shrink hidden hypotheses | Protocol, query design, inference, adversarial feedback | [interactive problem solving](sub-skills/interactive-problem-solving/SKILL.md) |
| A reward-bearing decision occurs in a live sequential process and its feedback changes later choices | Estimation, planning, exploration, sequential feedback | [reactive online decision problem solving](sub-skills/reactive-online-decision-problem-solving/SKILL.md) |

Read only the routed document or sub-skill before acting. A symptom may move to another row after one falsifying test; update the diagnosis instead of stacking unrelated fixes.

Model and Route Algorithms has two internal entry points: begin with model repair when correctness evidence is unresolved, and enter at its feasibility envelope only when the model and proof obligations are already trusted.

The checker/local-evaluation sub-skill owns evaluator architecture, independent implementation, and validation. For concrete evaluator code based on `testlib.h`, pair it with [testlib C++ judging](sub-skills/testlib-cpp-judging/SKILL.md).

## Distinguish Interactive from Reactive

Use this ownership rule when a task has feedback:

```text
Interactive: understand protocol -> choose query -> update hypotheses -> recover a sufficiently determined hidden answer

Reactive:    observe -> estimate -> choose a legal action -> receive reward -> update policy until the horizon/task/budget ends
```

If an action primarily eliminates candidate hidden states, route to Interactive. If it must earn reward while learning from live feedback that changes later decisions in the same run or environment, route the overall policy to Reactive and use Interactive only for a separable identification subproblem. Route an offline output-only or scorer-only optimization task to [heuristic search](references/heuristic-search.md), even when development repeatedly evaluates candidates. An AHC label alone does not decide the route: use Reactive only for a reward-bearing sequential layer with live feedback.

## Evidence-driven recovery loop

1. Reproduce the failure with the smallest faithful command, input, transcript, or fixed seed available.
2. State one falsifiable diagnosis at the model, algorithm, implementation, evaluator, protocol, or policy layer.
3. Design the smallest discriminating test. Prefer a tiny brute oracle, hand-computed boundary, invariant assertion, transcript replay, profile, or paired champion/challenger comparison.
4. Change one implicated component. Keep the champion untouched and materialize risky work as a challenger.
5. Compile and run the targeted test, then the relevant regression set. Never claim an experiment that was not run.
6. Promote only a legal challenger that fits the resource/protocol budget and improves the required correctness or fixed scoring evidence.
7. Repeat from the new evidence; do not tune a downstream layer while an upstream contract or validity contradiction remains.

For exact work, demand a corrected proof obligation and try to falsify it on tiny exhaustive cases. For scored work, keep the true raw objective authoritative and verify cached deltas against full recomputation. For stochastic work, compare paired seeds/cases and expand the sample only when variance can reverse the decision.

## Guardrails

- Separate legality from quality and raw objective from displayed score.
- Never let a failed or weaker challenger replace the champion.
- Preserve a legal fallback when interruption, query limits, or deadlines could otherwise erase all value.
- Isolate checker/scorer/interactor logic from solver-derived formulas when evaluator fidelity is in doubt.
- Prefer the target language for solver changes. For a new checker, scorer, interactor, simulator, or evaluation harness, strongly prefer target-supported C++ and portable C++17 when unspecified; provide exact build and run commands.
- Use research when the diagnosed gap requires external facts or another method family. Treat findings as hypotheses that must pass the same contract, compile, validity, objective, and resource gates as local ideas.
- Do not submit, overwrite a champion, or spend scarce evaluation budget unless the requested workflow authorizes it.

## Finish

Rebuild the selected champion cleanly and rerun the focused regression, independent oracle/checker where practical, final serialization or transcript validation, and representative resource probe. Deliver the champion rather than the latest edit.

Report only:

```text
root cause and decisive evidence
implemented correction or promoted challenger
commands/tests actually run and their results
remaining assumptions or material risk
```
