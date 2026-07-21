# Interactive, Reactive, and Uncertain Problems

Use this reference for hidden-state identification, query-limited tasks, online control, noisy feedback, and repeated-round learning. First distinguish the interaction type: a protocol bug, an inference bug, and a weak online policy need different fixes.

## 1. Interaction contract

Build a table from the statement and interactor/tester source where legitimate:

```text
initial judge output and order
legal query/action syntax and parameter bounds
exact response function or stochastic observation model
maximum queries/actions/rounds and what is counted
required flush points
special responses such as -1, error, success, or EOF
final-answer syntax and whether it consumes budget
state reset/persistence between rounds or cases
score as a function of correctness, queries, actions, and time
```

Replay the interactor semantics literally. Do not replace an accumulator, rounding rule, sequential operation, or state transition with an algebraically similar interpretation unless equivalence is proved. When prose and executable logic differ, document the discrepancy and implement against the authoritative evaluation contract. Also derive their legal intersection. Prefer an intersection-compliant query strategy when it reaches the same correctness, score, and budget; it is less brittle to evaluator changes and alternate local harnesses. When exploiting an executable-only allowance is materially better, name the dependency, exercise the exact guard in a test, and retain a fallback when practical.

Isolate protocol code:

```text
read_initial()
ask(query) -> response      // prints, flushes, increments count, validates reply
act(action) -> observation  // for reactive rounds
answer(solution)            // final serialization and termination
```

Every call checks budget and response validity. Avoid using `endl` everywhere for performance, but flush exactly when the protocol requires it. Exit immediately on judge-error/EOF rather than continuing with stale data.

## 2. Local simulator and transcript tests

Implement or use a simulator with the same state machine and response semantics. Strongly prefer a standalone C++ interactor/simulator for every new implementation, even when the solver is written in another language. Use the official C++ standard/toolchain when specified and portable C++17 otherwise; provide exact build and run commands. If an official interactor is in another language, retain it as an authority and compare the C++ implementation against it on generated transcripts. A delegated agent assigned this work must deliver the compilable `.cpp`, not only pseudocode, Python, or a report.

Test:

- every legal hidden state for tiny sizes;
- boundary query parameters and exact budget exhaustion;
- adversarial response choices when the judge may be adaptive;
- randomized noisy transcripts with fixed seeds;
- early error, EOF, and success termination;
- solver output captured through real pipes, not only direct function calls.

Keep the solver unaware of simulator-only truth. The simulator can inspect the hidden instance; the solver receives only legal observations. Compare simulator responses against the official interactor on generated cases when tools allow.

## 3. Interactive identification

Formalize:

```text
H_t = hidden hypotheses consistent with transcript through time t
R(h, q) = response for hidden state h and query q
H_{t+1} = {h in H_t : R(h, q_t) matches observed response}
```

For stochastic responses, maintain likelihood or a confidence set rather than eliminating on one mismatch.

### Information lower bounds

If there are `|H|` equally plausible hypotheses and each response has at most `b` outcomes, any decision tree needs at least `ceil(log_b |H|)` queries in the best balanced case. Symmetry and indistinguishable hypotheses may increase the requirement or make exact recovery impossible. Use the lower bound to detect a query-budget mismatch before coding.

### Query choice

Candidate criteria:

- worst-case remaining hypotheses: minimize `max_r |H(q,r)|`;
- expected entropy/uncertainty reduction under a justified prior;
- distinguish a targeted pair/class of hypotheses;
- group testing, coding, parity, or algebraic cancellation structure;
- divide-and-conquer or centroid/balanced separators;
- batch queries that recover several independent bits/features;
- robustness margin under noise or adversarial perturbation.

The query's value is defined by the exact response semantics. Validate claimed uniqueness globally across all relevant hypotheses, not only through local edits or a sampled codebook. Look for pairs that the entire planned transcript cannot distinguish.

### Adversarial versus stochastic judges

- For adversarial consistent responses, optimize worst-case partitions and maintain the full feasible set/invariants.
- For a known noise distribution, use repeated measurements, likelihood, error-correcting separation, sequential tests, or robust estimators.
- For unknown noise, reserve calibration queries and avoid brittle hard elimination.

Never multiply per-query success probabilities under hidden dependence without justification.

## 4. Reactive online control

Reactive tasks require useful actions now while learning for later rounds:

```text
observation/history -> belief or parameter model -> candidate actions ->
legal/risk filter -> action -> feedback -> model update
```

Separate three layers:

1. **Estimator:** what latent quantities are inferred, with what uncertainty?
2. **Planner:** given the current model, what exact/heuristic action is best?
3. **Explorer:** when is a less immediately rewarding action worth its information for future decisions?

This separation permits unit tests: synthetic truth for the estimator, known parameters for the planner, and controlled horizons for the exploration rule.

### Model design

Choose the simplest identifiable model that supports decisions:

- per-edge/item estimates with shrinkage toward group means;
- features/segments/low-rank factors when feedback is aggregate;
- Bayesian posterior or confidence intervals when uncertainty affects action;
- robust regression/median updates for noisy or heavy-tailed observations;
- change detection or recency weighting for drift;
- censored/delayed-feedback likelihood when the full outcome is not observed.

Aggregate feedback often leaves parameters non-identifiable. Optimize prediction of decision-relevant totals rather than pretending every latent variable is known. Add regularization and test rank/conditioning of the observation design.

### Value of information

An exploratory query/action is useful when its expected reduction in future decision loss exceeds its immediate cost and opportunity cost. Approximate:

```text
VOI(q) = expected future objective with posterior after q
         - future objective with current belief
         - direct cost(q)
```

Exact VOI is usually expensive; practical proxies include uncertainty along likely routes, disagreement between plausible models, expected partition balance, or confidence-bound optimism. Tie exploration to later decisions, not parameter uncertainty in irrelevant regions.

Use a horizon-aware schedule: earlier rounds can explore more; later or heavily-weighted rounds should exploit. Do not hard-code a switch solely from a benchmark round number when confidence and remaining opportunity provide a better signal.

## 5. Noisy scoring and repeated rounds

Distinguish randomness sources:

```text
instance seed
judge/transition noise
solver RNG
host/runtime noise
```

Control one source at a time. Fix solver RNG while diagnosing model updates, pair candidates on the same instances and judge seeds where allowed, and repeat measurements only when the observation noise warrants it.

Use robust summaries and per-round traces:

- cumulative reward and late-round reward;
- prediction residuals and calibration by uncertainty bucket;
- exploration cost versus later benefit;
- invalid action/query count and fallback use;
- score and runtime by instance feature;
- median/quantiles in addition to mean.

Feedback may be delayed or noisy; do not treat one higher scalar score as proof that the latest model is better.

## 6. Online planning patterns

Common hybrids:

- learned edge costs plus exact shortest path/MST/min-cost flow;
- learned processing times/skills plus list scheduling or matching;
- posterior samples plus robust/optimistic combinatorial optimization;
- active queries plus exact hypothesis decoding;
- rolling-horizon construction with bounded local repair;
- a portfolio selected by current confidence and remaining time.

When an action changes future feasibility, plan with the actual state transition and reserve recovery slack. A myopic objective can destroy reachability even if the immediate reward is high.

## 7. Safety and fallback

Maintain a legal response/action under every reachable internal state:

- a conservative default action when inference fails;
- bounded query/action generation that cannot exceed limits;
- a completion plan when little time/budget remains;
- deterministic tie-breaking for reproducibility;
- last-known-valid model/policy checkpoint;
- release behavior when input is malformed or judge terminates.

For scored identification, decide whether a partial but legal answer beats spending the remaining budget. For online control, reserve enough actions to finish mandatory coverage/return-to-start/output requirements.

## 8. Diagnostics

| Symptom | Likely layer | Check |
|---|---|---|
| Immediate protocol failure | I/O state machine | Initial read, flush, indexing, query syntax, special response |
| Correct small cases, query blow-up | Query design | Partition balance, symmetry, repeated information |
| Claimed decoder collisions | Model/semantics | Exact sequential response, global hypothesis enumeration |
| Good estimates, poor score | Planner/objective | Score transform, action feasibility, horizon, risk |
| Training residual falls, validation worsens | Estimator overfit | Rank, regularization, noise, seed split |
| Early score good, late score poor | Exploration/update | Bias accumulation, no drift handling, weak exploitation |
| Different RNG seeds diverge wildly | Robustness | Fragile early observations, unbounded update, missing fallback |
| Runtime spikes mid-protocol | Engineering | Replanning complexity, allocation, logging, worst-case query state |

Fix protocol and model semantics before tuning exploration constants.
