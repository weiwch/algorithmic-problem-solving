---
name: plateau-escape
description: >-
  Escape a measured quality plateau in a scored algorithmic, heuristic, or
  hybrid solver. Use only when a verified legal champion has stalled across
  controlled submissions or materially different challengers and the next
  step requires an independent trajectory audit, method research, a structural
  alternative, or an evidence-gated prototype. Do not use for one low score,
  invalid or zero output, checker disagreement, a crash, TLE/MLE, or an
  interactive protocol failure.
---

# Plateau Escape

Protect the verified champion, challenge the current problem representation, and test a structurally different method before resuming scalar tuning.

## Entry contract

Enter only when the root [scored-recovery escalation](../../SKILL.md#escalate-weak-scored-recovery) routes here. Treat that routing decision as authoritative instead of re-deriving plateau thresholds in this sub-skill. The handoff must identify the measured-plateau or severe-score-gap signal and include the trusted-evaluator, champion-legality, resource, and controlled-attempt evidence used by the root; if it does not, return to the root router.

## Freeze the baseline of evidence

Do not mutate the champion during diagnosis. Record a compact handoff:

```text
statement/evaluator/champion/experiment-log paths
verified contract, bounds, objective, budgets, and target
champion score/runtime and legality evidence
hypothesis -> material change -> measured result for prior attempts
saved counterexamples, profiles, fixed cases/seeds, and remaining budget
```

Stop repeated changes to weights, thresholds, temperatures, tie-breaks, seeds, or runtime allocation. The next hypothesis must concern representation, objective/value model, neighborhood reachability, planning horizon, decomposition, relaxation, or algorithm family.

## Run two independent fresh-context reviews

Use two fresh isolated sub-agents when the environment supports delegation. Prefer the least inherited conversation context and give each a distinct writable path that does not yet exist. Do not let either reviewer see the other's preliminary conclusions, and do not select the next route before both reports return.

Give both reviewers only the compact factual handoff above and this write contract:

```text
You may create and edit files only under: <fresh_agent_output_path>
Do not modify, delete, rename, or overwrite any pre-existing file.
Do not modify the champion and do not submit solutions.
Return the path of every file you create.
```

Do not place either reviewer in a read-only environment or globally forbid file creation. If executable work is in scope, require compilable source in the target language, exact build/run commands, and portable C++17 when the target does not specify a language standard. New checkers, scorers, interactors, simulators, and evaluation harnesses should be C++ unless the verified interface makes that genuinely impractical.

Assign distinct roles:

### Trajectory critic

Independently reconstruct the objective, bottleneck, and likely failure layer before reading the attempt history. Then audit:

- model and evaluator fidelity;
- representation and invariants;
- neighborhood reachability and proxy alignment;
- assumptions rejected without evidence;
- experiment design, variance, and overfitting.

Return ranked root causes, the strongest competing explanation, and `3-5` structurally different remedies. For each leading remedy, specify the smallest falsifying experiment, success threshold, and abort condition.

### Method researcher

Search by canonical problem names and variants and by distinctive mathematical structure, objective, constraints, and scale. Cover relevant editorials, strong solution write-ups, primary papers or surveys, benchmark documentation, competitive methods, and maintained implementations. Follow useful aliases and citation chains until targeted queries stop revealing credible new method families or the research budget ends.

Map every candidate to the exact contract. Return a ranked shortlist with direct sources, applicability gaps, expected upside, implementation risk, and one discriminating experiment per leading method. A self-contained challenger may be returned in the reviewer's isolated path.

Keep each decision memo concise: normally at most `900` words and `10` direct source links. Code and fixtures are separate from that limit.

If delegation is unavailable, perform the two roles as explicitly separate passes: write and seal the trajectory audit before starting a source-backed method search, and do not rewrite the first report to match the second.

## Select a structural challenger

After both reviews return:

1. Deduplicate proposals and rank them by contract fidelity, expected upside, implementation risk, and experiment cost.
2. Select the smallest experiment that discriminates among the leading structural explanations.
3. If a credible stronger method exists, begin a minimally faithful prototype that exercises its defining representation, relaxation, neighborhood, decomposition, planning, or inference mechanism.
4. Permit at most one or two already-defined cheap same-family scalar experiments to finish before that prototype. Do not begin another tuning sequence.
5. Compile and test the structural prototype against the champion before further parameter, seed, weight, threshold, tie-break, or runtime tuning.

A renamed version of the current solver is not a structural prototype.

Use [model and route algorithms](../model-and-route-algorithms/SKILL.md#5-establish-the-operation-and-memory-envelope) to test whether a proposed exact or hybrid class fits the contract and resource envelope, [technique selection](../../references/technique-selection.md) to compare concrete exact or data-structure families, and [heuristic search](../../references/heuristic-search.md) to instantiate and measure scored-search alternatives. The reviews choose hypotheses; the model-and-route sub-skill supplies the feasibility gate, while the shared references supply algorithm-family and search mechanics.

## Evidence gate for rejection or adoption

Reject a researched method only when concrete evidence shows one of these:

- its assumptions contradict the verified contract;
- a minimally faithful prototype fails its defining hypothesis or focused test;
- measured runtime or memory exceeds the budget without credible optimization margin;
- reconstruction, serialization, or hard validity fails;
- a competing alternative is clearly stronger under the same evaluator.

Unfamiliarity, restructuring cost, or an unmeasured pessimistic estimate is not rejection evidence. Repair trivial build or integration defects for one focused cycle before judging the method.

When a reviewer returns code, keep it as a separate challenger and run:

```text
inspect -> compile -> repair trivial integration defects once
-> legality/objective/runtime validation -> authorized submission if applicable
-> record result -> promote or reject with evidence
```

Only the main agent may modify the champion, submit a solution, or promote a challenger. Submit only when the user has authorized that workflow. Promote only a legal challenger that fits the budget with margin and improves fixed evidence; otherwise retain the champion and the rejection record.

## Exit condition

End this escape cycle when a materially different route has been adopted or the leading structural alternatives have concrete rejection evidence. Resume focused tuning only on the selected final method. Run this workflow again only after that materially new family also reaches a new plateau or new evidence invalidates the reviews.

Return:

```text
plateau evidence
ranked root causes and structural alternatives
prototype and exact commands
champion-versus-challenger measurements
promotion/rejection decision and residual risk
```
