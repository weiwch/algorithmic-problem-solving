---
name: plateau-escape
description: >-
  Reference guide for understanding and escaping plateaus in scored,
  heuristic, and hybrid solvers. Covers plateau interpretations, structural
  redesign axes, trajectory review, independent idea generation, prior-art
  search, and evidence for comparing alternative method families. Use when
  local tuning has stalled or a broader optimization strategy map is useful.
---

# Escaping Search Plateaus

A plateau is an observation that additional effort under the current setup yields little useful improvement. Compare marginal gains with evaluator noise, seed variance, runtime variance, and the remaining distance to a meaningful target before deciding that the current method has saturated.

## What may look like a plateau

| Pattern | Possible interpretation |
|---|---|
| Repeated parameter changes have negligible effect | The parameter is insensitive, already calibrated, or dominated by another layer |
| Different seeds reach similar solutions | One strong basin, weak construction diversity, or restricted reachability |
| Proxy improves while official score does not | Surrogate or score-transform mismatch |
| More time produces no best updates | Neighborhood ceiling, disconnected state graph, or saturated representation |
| Local scores improve but remote scores do not | Overfit, distribution shift, evaluator mismatch, or noise |
| Large variance hides small gains | Comparison design is underpowered rather than the method being saturated |
| Every candidate is invalid | Legality, reconstruction, or evaluator semantics—not search quality—is central |

The measured trajectory, evaluator confidence, score scale, remaining budget, and user goal determine which interpretation matters. No universal percentages or submission counts establish a plateau.

## Structural redesign axes

Scalar tuning changes temperatures, weights, probabilities, thresholds, or iteration allocation inside a method. Structural alternatives change what the method can express or reach.

Useful structural axes include:

- **Representation:** routes, sequences, components, regions, schedules, or quotient states instead of low-level tokens.
- **Construction:** regret insertion, randomized greedy, relaxation rounding, exact-small extension, decomposition, or diverse warm starts.
- **Neighborhood:** compound moves, exchange chains, block operations, rerouting, destroy-and-repair, or temporary relaxation.
- **Evaluation:** exact incremental deltas, decomposed contributions, lexicographic objectives, bounds, or better-aligned surrogates.
- **Decomposition:** independent components, separators, time windows, spatial regions, clustering, or master/subproblem splits.
- **Hybridization:** heuristic outer decisions with DP, matching, flow, shortest path, CP-SAT, or bounded exact repair inside.
- **Planning horizon:** greedy, lookahead, rolling horizon, beam search, MCTS, or value approximation.
- **Portfolio:** complementary constructors, neighborhoods, solvers, or instance-conditioned policies.

[Quantified Heuristic Search](../../references/heuristic-search.md) contains concrete mechanics for these ideas. [Algorithm Technique Selection](../../references/technique-selection.md) helps identify exact inner subproblems.

## Reading the attempt trajectory

An attempt history can be organized by hypothesis rather than chronology:

```text
hypothesis | structural or scalar change | evaluator and budget
per-instance effect | validity/runtime effect | what the result distinguishes
```

Useful review questions include:

- Which assumptions have direct evidence and which are inherited folklore?
- Did changes alter reachable solutions or only search order?
- Does the representation encode the scoring object's natural structure?
- Is improvement limited by construction, move generation, repair, evaluation, or optimizer behavior?
- Are experiments comparable in cases, seeds, time, and score definition?
- Did a method family receive a faithful implementation, or only a weakened proxy?

Independent reconstruction can reduce anchoring; a shared-context review is cheaper and retains more trajectory detail. Choose the review setup according to which risk matters more.

## Independent idea generation

Looking at the problem without the incumbent method can reveal alternatives that a trajectory-centered review misses. Useful prompts are:

- What canonical combinatorial object matches the feasible solution?
- Which decisions become exact if most variables are fixed?
- Which constraint creates the useful decomposition or separator?
- What relaxation provides a bound, rounding scheme, or repair target?
- Which adjacent field uses the same objective under a different name?
- What do strong solutions optimize per unit time: transitions, repairs, rollouts, or subproblem calls?

Independence is a reasoning aid. The amount of context withheld or separated should match the risk of anchoring and the cost of reconstructing known facts.

## Prior-art search map

When external research is useful, search from the problem structure rather than only the current algorithm name:

```text
exact title or distinctive statement phrase
contest, task source, editorial, leaderboard, post-contest write-up
canonical problem name plus objective, constraints, and scale
mathematical structure plus heuristic, approximation, decomposition, or LNS
benchmark or dataset plus winning method or evaluation protocol
primary paper, survey, references, citations, and maintained implementation
```

For each source, extract the assumptions, defining mechanism, complexity, scale, objective, and evidence. A method can be source-backed yet incompatible with the actual contract; conversely, a source from an adjacent domain may transfer after the right reformulation.

## Comparing structural alternatives

Comparison dimensions include:

| Dimension | Questions |
|---|---|
| Contract fit | Does the method preserve legality and optimize the actual metric? |
| Reachability | Which barriers or coordinated changes become possible? |
| Information quality | Are deltas, bounds, rollouts, or surrogates informative? |
| Work efficiency | How many useful events occur per second or unit budget? |
| Scalability | What dominates time, memory, and variable-cost tails? |
| Robustness | How does performance vary across cases, seeds, and lower tails? |
| Integration | What state, reconstruction, or evaluator assumptions are exposed? |

Evidence may come from a proof, resource bound, small faithful prototype, reproducible local comparison, or official result. Match evidence strength to the consequence of the decision: cheap screening can use weaker signals, while replacement of a strong incumbent needs a comparable legality, score, and resource evaluation.

## Adjacent references

- Uncertain legality or score semantics: [Checker and Local Evaluation](../checker-and-local-evaluation/SKILL.md)
- Modeling or exact/hybrid feasibility: [Model and Route Algorithms](../model-and-route-algorithms/SKILL.md)
- Experiment and seed design: [Validation and Experiments](../validation-and-experiments/SKILL.md)
- Offline scored-search mechanics: [Quantified Heuristic Search](../../references/heuristic-search.md)
- Live feedback that changes later actions: [Reactive Online Decision Problem Solving](../reactive-online-decision-problem-solving/SKILL.md)
