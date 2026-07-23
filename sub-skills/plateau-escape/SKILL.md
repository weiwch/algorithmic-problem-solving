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

## Freeze the champion and separate facts from conclusions

Do not mutate the champion during diagnosis. Preserve the original problem artifacts rather than replacing them with a summary. Build two records:

```text
primary problem packet
  original statement/title/URL and exact I/O
  constraints, objective, scoring and aggregation rules
  official evaluator/configuration, samples, public instances, benchmark or dataset
  target toolchain, resource limits, submission interface and remaining budget

verified fact ledger
  necessary fact | exact derivation/source/command | reproducing artifact
  champion identity, legality, final score/runtime and evaluator evidence
  saved counterexamples, profiles and fixed cases/seeds
```

A derived fact is admissible only when it follows from a checked proof, an official primary artifact, or a reproducible command or test whose artifact is included. Keep prior diagnoses, method preferences, estimated upside, risk labels, and reported results without raw evidence out of the fact ledger. Preserve an unresolved discrepancy as a question with its primary artifacts; never silently turn one interpretation into a fact.

Keep the prior attempt log separately as claims plus observations:

```text
claimed hypothesis -> exact material change -> command/submission -> observed result
```

Stop repeated changes to weights, thresholds, temperatures, tie-breaks, seeds, or runtime allocation. The next hypothesis must concern representation, objective/value model, neighborhood reachability, planning horizon, decomposition, relaxation, or algorithm family.

## Run two independent fresh-context reviews

Use two fresh isolated sub-agents when the environment supports delegation. Prefer the least inherited conversation context and give each a distinct writable path that does not yet exist. Do not let either reviewer see the other's preliminary conclusions, and do not select the next route before both results return.

Give each reviewer the primary problem packet, only the role-specific projection of the verified fact ledger described below, and this write and evaluation contract:

```text
You may create and edit files only under: <fresh_agent_output_path>
Do not modify, delete, rename, or overwrite any pre-existing file.
Treat every pre-existing artifact, including the champion, as read-only.
You may build, run, evaluate, and, within the delegated quota, submit challengers
created in your isolated path. The quota allocation is the submission authorization;
do not request main-agent approval again for each in-quota submission.
Return the path of every file you create.
```

Do not place either reviewer in a read-only environment or globally forbid file creation. If the user or enclosing workflow authorizes remote submissions and at least one submission remains, reserve an explicit quota for sub-agent evaluation; allocate at least one submission to the method researcher instead of retaining the entire budget for the main agent. State the evaluator, final metric, quota, deadline, and whether results are synchronous. A sub-agent may not exceed that quota or infer broader authorization. If only the main agent can access the submission transport, it must relay the sub-agent's exact artifact unchanged and return the raw evaluator response; it may not substitute a different candidate or make a method decision at that boundary.

Require compilable source in the target language, exact build/run commands, and portable C++17 when the target does not specify a language standard. New checkers, scorers, interactors, simulators, and evaluation harnesses should be C++ unless the verified interface makes that genuinely impractical.

Assign distinct roles:

### Trajectory critic

First reconstruct the objective, bottleneck, and likely failure layer from the primary problem packet and verified fact ledger. Freeze that reconstruction before receiving the separate attempt log. Then audit:

- model and evaluator fidelity;
- representation and invariants;
- neighborhood reachability and proxy alignment;
- assumptions rejected without evidence;
- experiment design, variance, and overfitting.

Return ranked root causes, the strongest competing explanation, and `3-5` structurally different remedies. For each leading remedy, specify the smallest falsifying experiment, success threshold, and abort condition. If one remedy can be tested faithfully within the assigned path and evaluation quota, implement and score it rather than returning advice alone.

### Method researcher

Research the original problem independently, not the incumbent solver or the main agent's diagnosis. Initially receive the complete primary problem packet and only verified facts necessary to understand the contract, hard budgets, target, and available evaluation interface. Do not receive the champion identity or score, attempt log, current method labels, rejected-method rationales, trajectory critic conclusions, or any unverified summary before freezing an independent research map. After that map is saved, the exact champion interface and score plus reproducible negative results may be revealed only to prevent duplicate implementation and to support a direct comparison.

Search from the problem outward:

- exact title, distinctive statement phrases, source contest, editorials, leaderboards, and strong solution write-ups;
- canonical problem names, aliases, mathematical structure, objective, constraints, and scale;
- primary papers, surveys, references and citation chains, including adjacent fields with the same optimization structure;
- relevant benchmarks, public datasets, instance generators, baselines, winning methods, and evaluation protocols;
- maintained implementations and repositories that clarify a method's defining mechanism.

Do not stop at the current solver family. Continue targeted searches until new queries only repeat already-mapped method families or the explicit research budget ends. Keep a source/evidence index; a concise decision memo may link to that index and must not discard useful sources merely to satisfy an arbitrary link cap.

Map every candidate to the exact contract. Distinguish source-backed facts, independently derived facts, measured results, and unresolved hypotheses. Rank the feasible candidates, select the highest-ranked discriminating structural method, and own it end to end:

```text
research -> faithful implementation -> compile -> local legality/objective checks
-> champion comparison -> authorized official submission -> final evaluator result
```

Return executable code and fixtures plus a completed evaluation record, not a recommendation for the main agent to judge. Use `EXACT_OFFICIAL` only for a terminal official result and `EXACT_LOCAL` only for a reproducible local result under an identified evaluator. Otherwise report `NOT_EVALUATED` and the concrete unavailable authorization, quota, interface, or resource; never replace a missing score with an estimate.

The evaluation record must include:

```text
candidate path and content hash
exact build, run, validation and submission commands
evaluator/version, instance set, seeds and resource budget
submission id and terminal timestamp when remote
validity/verdict, raw objective and final aggregate score
runtime, memory, per-case data and raw evaluator response when available
comparison with the frozen champion under the same final metric
```

If delegation is unavailable, perform the two roles as explicitly separate passes: write and seal the trajectory audit before starting a source-backed method search, and do not rewrite the first report to match the second. Preserve the same role-specific information barrier.

## Apply the independent results

After both reviews return:

1. Register every leading proposal and preserve each reviewer's ranking and evidence; do not rewrite the reports into a main-agent ranking before disposition.
2. Accept an already terminally evaluated sub-agent result as evidence. Do not send it back to the main agent for a second subjective method judgment.
3. Give the method researcher's primary eligible method its reserved implementation/evaluation slot. If it is not already evaluated, run or relay its declared discriminating experiment before any new same-family tuning.
4. Give every leading proposal exactly one disposition: `EVALUATED`, `TEST_REQUIRED`, `REJECTED_WITH_EVIDENCE`, or `BLOCKED_BY_SCOPE`. A label without its command, artifact, measurement, proof, primary contract citation, or exact immutable scope boundary is incomplete.
5. Permit at most one or two already-defined cheap same-family scalar experiments to finish before the protected structural trial. Do not begin another tuning sequence.

A renamed version of the current solver is not a structural prototype.

For this gate, *leading* includes each reviewer's rank-1 method and every challenger already returned as executable code or with a terminal evaluation. The main agent may add candidates but may not remove these mandatory dispositions. When evidence conflicts, prefer a comparable terminal official result, then a reproducible independent local result, then a checked proof or measured resource bound, then source-backed applicability evidence; an opinion never overrides a higher tier.

Use [model and route algorithms](../model-and-route-algorithms/SKILL.md#5-establish-the-operation-and-memory-envelope) to test whether a proposed exact or hybrid class fits the contract and resource envelope, [technique selection](../../references/technique-selection.md) to compare concrete exact or data-structure families, and [heuristic search](../../references/heuristic-search.md) to instantiate and measure scored-search alternatives. The reviews choose hypotheses; the model-and-route sub-skill supplies the feasibility gate, while the shared references supply algorithm-family and search mechanics.

## Bind main-agent discretion to evidence

Reject a researched method only when concrete evidence shows one of these:

- a cited assumption contradicts the verified original contract;
- a minimally faithful prototype fails its predeclared defining hypothesis or focused test;
- a derived bound or measured runtime or memory exceeds a hard budget without credible optimization margin;
- an identified reconstruction, serialization, or hard-validity condition fails after one focused repair cycle;
- an exact result is no better than a competing alternative under the same final evaluator and comparable budget.

`Risk too high`, unfamiliarity, restructuring cost, implementation size, disagreement with the current trajectory, or an unmeasured pessimistic estimate is not rejection evidence. Quantify any claimed risk as a violated hard constraint, a derived bound, a failed artifact, or a measured result. Repair trivial build or integration defects for one focused cycle before judging the method. If the main agent believes a prototype is unfaithful, it must name the missing defining mechanism and allow one focused repair instead of discarding the family.

When a user-imposed authorization, submission quota, deadline, language, or artifact boundary genuinely makes evaluation impossible, record `BLOCKED_BY_SCOPE` with the exact remaining budget and the measured or derived minimum requirement. This is not evidence that the method is weak and must not be rewritten as `REJECTED_WITH_EVIDENCE`.

When a reviewer returns code, keep it as a separate challenger and run:

```text
inspect -> compile -> repair trivial integration defects once
-> legality/objective/runtime validation -> sub-agent-owned authorized submission
-> record result -> promote or reject with evidence
```

The sub-agent owns the evaluation and terminal score of a challenger within its delegated quota; the main agent owns only champion-file mutation and mechanical promotion. Under the same established final evaluator, automatically promote a legal challenger whose exact terminal final score is better and whose hard resource/protocol constraints pass. Withhold promotion only for a concrete failing artifact or an explicit user-imposed release condition, and record it. Retain the champion when the challenger is invalid, worse, tied without another predeclared benefit, or not evaluated. Opinion cannot override an exact comparable result.

## Exit condition

End this escape cycle when a materially different route has been adopted or every leading structural alternative has a terminal evaluation, concrete rejection evidence, or an immutable user-scope block with quantified requirements. `Not evaluated`, `risky`, and an unevidenced main-agent veto do not satisfy the exit condition. Resume focused tuning only on the selected final method. Run this workflow again only after that materially new family also reaches a new plateau or new evidence invalidates the reviews.

Return:

```text
plateau evidence
role-specific input packets and reviewer rankings
source/evidence index, prototype and exact commands
submission ids and terminal champion-versus-challenger scores
promotion or evidence-backed rejection record
```
