---
name: algorithmic-problem-solving
description: >-
  Route algorithmic-problem-solving questions to focused knowledge guides on
  problem modeling, algorithm-family selection, implementation engineering,
  checkers and Testlib, validation methods, heuristic search, interactive
  inference, reactive online decisions, and search plateaus. Use for contest
  problems, custom judges, scored or output-only tasks, AHC-style optimization,
  and general solver design when topic-specific reference material would help.
  This skill provides topic routing only; consult its references selectively
  according to the current question.
---

# Algorithmic Problem-Solving Knowledge Router

Use this file as an index. Select the topic that matches the current question and consult the relevant guide.

## Topic map

| Question or topic | Useful guide |
|---|---|
| What exactly is the state, objective, invariant, reduction, proof obligation, or feasible solution class? | [Model and Route Algorithms](sub-skills/model-and-route-algorithms/SKILL.md) |
| Which exact algorithm or data-structure family matches the operations and bounds? | [Algorithm Technique Selection](references/technique-selection.md) |
| Is the issue in compilation, overflow, memory, runtime, I/O, serialization, randomness, or a data-structure implementation? | [Contest Solver Engineering](sub-skills/contest-solver-engineering/SKILL.md) |
| How do checkers, scorers, validators, simulators, interactors, or local evaluators work? | [Checker and Local Evaluation](sub-skills/checker-and-local-evaluation/SKILL.md) |
| Which `testlib.h` API, stream, verdict, or executable role is relevant? | [Testlib C++ Judging](sub-skills/testlib-cpp-judging/SKILL.md) |
| Which oracle, differential test, metamorphic relation, seed design, holdout, or resource probe is informative? | [Validation and Experiments](sub-skills/validation-and-experiments/SKILL.md) |
| How should an offline scored/output-only solver represent states, construct solutions, define moves, compute deltas, or choose a metaheuristic? | [Quantified Heuristic Search](references/heuristic-search.md) |
| How can a stalled scored method be reconsidered through structural alternatives or prior-art search? | [Escaping Search Plateaus](sub-skills/plateau-escape/SKILL.md) |
| How should queries reduce uncertainty about a hidden state or answer? | [Interactive Problem Solving](sub-skills/interactive-problem-solving/SKILL.md) |
| How should live feedback affect later reward-bearing decisions? | [Reactive Online Decision Problem Solving](sub-skills/reactive-online-decision-problem-solving/SKILL.md) |

## Boundary hints

- **Interactive vs. reactive:** interactive reasoning values actions mainly for information; reactive reasoning values immediate reward, future opportunity, information, and risk together.
- **Reactive vs. offline heuristic:** a task is reactive only when feedback observed during the judged execution can change later actions. Repeated offline scoring does not make it reactive.
- **Evaluator vs. validation:** the evaluator guide explains what an output means and how judging components behave; the validation guide explains what evidence can distinguish solver hypotheses.
- **Algorithm selection vs. engineering:** technique selection concerns the abstract method and its preconditions; solver engineering concerns faithful implementation in the target environment.
- **Plateau analysis vs. heuristic mechanics:** plateau analysis broadens the structural search space; heuristic search explains concrete representations, neighborhoods, deltas, and optimizers.

## Finding a specific detail

Open the most relevant guide directly. For a long guide, search its headings or use a narrow keyword such as:

```bash
rg -n "state|reduction|complexity" sub-skills/model-and-route-algorithms/SKILL.md
rg -n "matching|flow|DP|segment tree" references/technique-selection.md
rg -n "delta|LNS|annealing|reachability" references/heuristic-search.md
rg -n "checker|scorer|interactor|validator" sub-skills/checker-and-local-evaluation/SKILL.md
```

Use the cross-links inside each guide to move to adjacent topics.
