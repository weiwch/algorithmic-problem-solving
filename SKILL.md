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
| The route is known, but its complexity class, algorithm-family premise, or abstract data-structure choice is wrong | Algorithm selection | [technique selection](references/technique-selection.md) |
| The official evaluator is absent or doubtful; zero/invalid feedback is unexplained; local and remote results disagree; or legality, objective, score, simulator, or episode replay needs independent reconstruction | Evaluator contract and independent oracle | [checker and local evaluation](sub-skills/checker-and-local-evaluation/SKILL.md) |
| The evaluator is trusted, but oracle choice or counterexample search is unclear, comparisons are noisy, or a challenger needs paired, holdout, metamorphic, or other independent evidence before promotion | Experiment design and promotion evidence | [validation and experiments](sub-skills/validation-and-experiments/SKILL.md) |
| The route is justified, but compilation, crash/UB, overflow, memory layout, TLE/MLE, buffering, solver-side serialization, randomness, deadline handling, or an implemented data-structure invariant fails | Implementation and environment | [contest solver engineering](sub-skills/contest-solver-engineering/SKILL.md) |
| A legal scored solver has weak construction, moves, deltas, reachability, or optimizer behavior | Search mechanics | [heuristic search](references/heuristic-search.md) |
| A verified legal scored champion has stopped improving or remains severely below the meaningful score target | Structural quality escalation | [scored-recovery escalation](#escalate-weak-scored-recovery) |
| The main value of an action is to obtain information and shrink hidden hypotheses | Protocol, query design, inference, adversarial feedback | [interactive problem solving](sub-skills/interactive-problem-solving/SKILL.md) |
| A reward-bearing decision occurs in a live sequential process and its feedback changes later choices | Estimation, planning, exploration, sequential feedback | [reactive online decision problem solving](sub-skills/reactive-online-decision-problem-solving/SKILL.md) |

Read only the routed document or sub-skill before acting. A symptom may move to another row after one falsifying test; update the diagnosis instead of stacking unrelated fixes.

Model and Route Algorithms has two internal entry points: begin with model repair when correctness evidence is unresolved, and enter at its feasibility envelope only when the model and proof obligations are already trusted.

Technique Selection owns whether a data structure or algorithm family matches the required operations and bounds. Contest Solver Engineering owns whether the chosen implementation maintains its invariants. Checker and Local Evaluation owns what submitted output means and independently parses legality and score; Contest Solver Engineering owns solver-side formatting, index conversion, buffering, and flush behavior.

Use the recovery loop below for a correction with a known deterministic failing test and an obvious focused regression. Route to Validation and Experiments when choosing or constructing the evidence is itself material, stochastic variance can reverse promotion, or independent promotion/release evidence is still missing.

The checker/local-evaluation sub-skill owns evaluator architecture, independent implementation, and adversarial validation of the evaluator itself. For concrete evaluator code based on `testlib.h`, pair it with [testlib C++ judging](sub-skills/testlib-cpp-judging/SKILL.md).

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

## Escalate weak scored recovery

Apply this gate only after the champion is legal under a trusted evaluator and fits the resource budget. Read [Plateau Escape](sub-skills/plateau-escape/SKILL.md) when either signal holds:

- **Measured plateau:** at least two materially different legal challengers, or at least five controlled submissions, fail to improve the champion beyond a predeclared threshold above evaluator/seed/runtime noise. Use `1%` of a normalized score scale as the default threshold when such a scale exists.
- **Severe score gap:** after the focused recovery loop, the best legal champion is still below `50%` of a meaningful official maximum or explicit target.

A controlled comparison uses the same evaluator, comparable budgets, and paired fixed cases/seeds when applicable. A materially different challenger changes the representation, objective or value model, neighborhood reachability, planning horizon, decomposition, relaxation, or algorithm family; changing only a seed, scalar parameter, tie-break, operator percentage, or runtime allocation does not count.

A single low score is not by itself a measured plateau, but the below-half rule is an independent mandatory escalation because the remaining gap is too large to justify stopping at local tuning. After the focused recovery loop, this root-level severe-gap signal satisfies Plateau Escape's admission gate. If the score has no meaningful maximum or explicit target, do not invent a halfway point; use the measured-plateau signal instead. Route invalid or contradictory evaluation to Checker and Local Evaluation, implementation/resource failures to Contest Solver Engineering, unclear comparisons to Validation and Experiments, and ordinary search-mechanics weaknesses to Heuristic Search before escalating.

When escalation applies, preserve the champion and give Plateau Escape the recovery snapshot plus the controlled attempt log. Run its two fresh-context sub-agent reviews when delegation is available, then treat their recommendations as actionable evidence: rank them, implement the strongest credible structural challenger, and compile and test it against the champion. Do not stop after collecting reports or return to scalar tuning without testing that challenger. If delegation is unavailable, perform the two sealed independent passes required by that sub-skill.

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

Before accepting any scored result below half of a meaningful official maximum or explicit target, read and execute Plateau Escape. Use its fresh-context sub-agents to challenge the current trajectory and method family, then implement and test the strongest credible recommendation; do not finish with only their advice or with another round of minor parameter tuning.

Report only:

```text
root cause and decisive evidence
implemented correction or promoted challenger
commands/tests actually run and their results
remaining assumptions or material risk
```
