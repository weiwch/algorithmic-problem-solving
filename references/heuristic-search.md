# Quantified Heuristic Search

Use this reference when a legal scored attempt has exposed a concrete weakness in construction, representation, moves, evaluation, or optimizer behavior. The central question is not which metaheuristic sounds sophisticated. It is whether the representation, moves, evaluation, and useful-event count form an informative reachable process within the time limit.

If the evidence already meets the admission criteria for [plateau escape](../sub-skills/plateau-escape/SKILL.md), stop scalar tuning and use that sub-skill before selecting another representation or method family. Use this reference for concrete search mechanics and for testing the structural hypotheses returned by the plateau review.

## 1. Search budget

Estimate costs before selecting an optimizer:

```text
C_init    construction/restart cost
C_full    full legality and true-score recomputation
C_move    proposal + precheck + delta + accept/undo
C_repair  repair or exact local solve cost and tail
T_search  safety_factor * official_limit - I/O - final validation - overhead
N_total   approximately (T_search - restarts * C_init) / mean(C_move)
```

Measure representative kernels. Include copying, allocation, cache misses, hashing, RNG, timer calls, logging, and variable-cost tails. If the timer is checked every `B` moves, bound overshoot by `B * worst_relevant_move_time`. Reserve more margin for slow judges, shared hosts, interactive I/O, recursion, repairs, or exact subsolves. Never consume the official limit exactly.

Raw search size alone is not actionable:

```text
S_raw = product of variable domain sizes
S_effective = S_raw after constraints, symmetry, decomposition, and reachability
```

Compare the number and diversity of states that can actually be evaluated with `S_effective` and with the basin structure implied by the moves.

## 2. Quantify useful events

For an independent per-trial target probability `p`:

```text
P(at least one hit in N trials) = 1 - (1-p)^N
N needed for probability q      = ceil(log(1-q) / log(1-p))
```

For small `p`, a 99% hit probability needs about `N*p >= 4.6`. Do not multiply probabilities blindly when events are dependent. Use conditional reasoning, bounds, or pilot measurements.

Instrument the transition funnel per move type:

```text
p_valid       = valid proposals / proposals
p_accept      = accepted / valid
p_structural  = objective-relevant changes / accepted
p_best        = best updates / structural changes
useful/s      = proposals/s * p_valid * p_accept * p_structural
gain/s        = proposals/s * p_valid * p_accept
                * E[max(true_delta, 0) | accepted]
```

`gain/s` intentionally does not multiply by `p_structural`: its expectation is over all accepted moves, including zero-gain non-structural moves. If the expectation is estimated only over structural accepted moves, multiply by `p_structural` and condition the expectation on both accepted and structural moves.

Also record representative move cost, delta distribution, distinct-state/hash rate, and best-update time. Interpret low rates causally:

- low validity: proposal ignores constraints or representation is wrong;
- high acceptance but little structural change: move edits irrelevant tokens;
- truth stalls while proxy rises: surrogate mismatch;
- more time gives no gain: neighborhood ceiling or disconnected reachability;
- occasional huge gain with bad median: risky heavy-tail policy needing explicit score-system justification.

For unknown hit rates, pilot `m` comparable trials. If zero hits occur, `3/m` is a common rough 95% upper bound for `p`; if even that gives `N_budget*p << 1`, redesign rather than hoping.

## 3. Representation and invariants

Map the objective to its natural combinatorial object:

```text
routes rather than visited flags
machine/task sequences rather than isolated start times
components/cuts/matchings rather than unrelated labels
blocks/segments rather than individual permutation tokens
regions/boundaries/spatial adjacency rather than full-grid rescans
```

Model constraints at their real granularity. An edge conflict need not forbid a whole vertex; one occupied interval need not block a full day; independent channels/capacities should remain separate.

Prefer:

```text
valid state -> validity-preserving move -> valid state
```

Temporary infeasibility is justified only when violation is cheap and informative, repair is reliable and bounded, adaptive penalties return to feasibility, or infeasible states connect otherwise separated feasible regions.

Maintain explicit roles:

```text
current_state, current_search_value, current_true_score
best_valid_state, best_true_score
candidate move, affected set, delta, rollback log
primary structure, inverse indices, local contributions, constraint counts
```

Remove equivalent states using canonical labels, fixed representatives, sorted interchangeable groups, symmetry-breaking directions, or hashes. Confirm that canonicalization preserves objective and reachability.

## 4. Fallback and construction

Retain the champion. If early termination can erase all value and no simple guaranteed-valid fallback exists, add one without replacing the champion. Construction experiments remain challengers. Candidate constructors include:

- deterministic or randomized greedy;
- restricted candidate lists / GRASP;
- regret, marginal-gain, or gain-density insertion;
- conditional/importance/stratified sampling;
- divide-and-construct or component assembly;
- relaxation rounding plus bounded repair;
- extension of exact small solutions;
- warm start from a legal prior incumbent;
- multiple deliberately diverse starts.

Measure construction time, validity, raw score, diversity, and score after a fixed improvement budget. The best raw start may not have the best improvement potential. Keep construction diversity tied to a hypothesis about different basins, not merely different RNG seeds.

## 5. Incremental evaluation

When the objective is a sum of local contributions and a move affects `A`:

```text
delta = sum over i in A of (new_local_i - old_local_i)
sample -> identify affected data -> precheck -> apply/delta -> accept or undo
```

Implement `apply`, `undo`, `delta`, `validate`, and full true-score recomputation. During development, periodically assert:

```text
cached score == full score
cached constraints == full constraints
undo(apply(state, move)) == original state
```

Target `O(1)`, `O(log n)`, or genuinely local `O(k)` transitions using inverse indices, prefix/Fenwick/segment structures, candidate lists, reusable buffers, delayed updates, and rollback logs. Avoid full-state copies, allocations, and full rescoring in the inner loop unless the state is demonstrably small.

## 6. Neighborhoods and reachability

A useful move should preserve or cheaply restore legality, change score-relevant structure, support fast delta/undo, and combine with other moves to reach meaningfully different states.

Use multiple scales when the problem requires them:

```text
small: change, swap, relocate, reverse, insert/remove, 2-opt
medium: block exchange, cycle/exchange chain, merge/split, subpath reroute
large: region rebuild, destroy-and-repair, restart, exact local reoptimization
```

Check reachability explicitly. Repeated legal moves may accidentally preserve parity, order, components, topology, or a hidden count. Simulated annealing cannot cross a boundary that no move can cross. Add the missing transition, perturbation, restart, temporary infeasibility, or exact local solve.

Once a simple neighborhood reaches a local optimum, derive the exact delta of the smallest coordinated move and necessary conditions for positive gain. Those conditions often reduce a nominal quadratic or combinatorial scan to a small structural candidate set. Benchmark that stronger, problem-specific neighborhood and its gain per second before using tabu, annealing, or random walks to compensate for weak moves.

For LNS:

```text
select region -> remove/relax -> randomized or exact repair ->
score complete state -> accept/reject -> update best
```

Bound ruin size and repair time. Schedule operators from measured gain/second, best-update rate, phase, and stagnation. Retain a rare barrier-crossing move when its long-term value is demonstrated, even if immediate delta is weak.

## 7. True objective, proxies, and penalties

The true checker score is authoritative for the best state, final output, and experiments. A proxy or penalty may guide `current` only.

Use a surrogate when the true score is sparse, discontinuous, or flat. Build it from interpretable partial progress such as completion, repair distance, connectivity, utilization, balance, or a bound gap. Then test alignment:

- sample transitions and compare proxy and true delta signs/ranks;
- inspect extrema and boundary behavior;
- verify that improving one component cannot silently destroy a strict priority;
- normalize terms by robust observed delta scale;
- prefer lexicographic/staged priorities to unexplained giant weights.

Constraint preference order:

```text
feasibility-preserving representation
bounded deterministic repair
adaptive penalty with measured feasible rate
fixed penalty only with a proven dominating scale
```

## 8. Optimizer selection

Choose after state, moves, evaluation, and budget are known.

| Method | Good fit | Warning signs |
|---|---|---|
| Uniform/biased sampling | Complete candidates are very cheap and useful mass is measurable | Rare coordinated structure, high rejection |
| Randomized greedy / GRASP | Local signal exists but deterministic construction locks in | Candidate list lacks diversity or repair dominates |
| Hill climbing | Improving moves are common and neighborhood is strong | Plateaus, deep basins, unreachable coordinated edits |
| Multi-start / iterated local search | Starts are cheap/diverse and local convergence is fast | Every start reaches the same basin |
| Threshold / late acceptance | Controlled worsening helps without temperature model | Scale drifts or search becomes random walk |
| Simulated annealing | Worsening moves connect basins and delta scale is measurable | Weak/disconnected moves, too few iterations, proxy mismatch |
| Tabu / guided local search | Immediate reversals/cycles or repeated costly features dominate | Tenure/penalty overhead restricts useful moves |
| VNS / LNS / adaptive LNS | Coordinated changes are needed and repair is effective | Unbounded or low-quality repair |
| Beam search | Sequential construction with predictive partial score | Beam collapse, duplicates, large branching/memory |
| Evolutionary/population | Feasible crossover preserves useful building blocks | Children need wholesale repair; diversity collapses |
| MCTS | Sequential decisions, informative rollouts, reusable states | High branching, noisy rollout, few simulations |
| Exact local solve | Fixing most variables yields a tractable subproblem | Calls are too large/frequent or boundaries mis-modeled |

Do not default to a population method when independent multi-start local search uses the same evaluations more effectively.

Evaluate stochastic optimizers under the scoring rule that will actually be used. If one program run may launch `N` independent starts and retain the best, compare the empirical best-of-`N` distribution, not only single-run means. For continuous parameters, use a small designed sweep, random/low-discrepancy search, racing, or a suitable optimizer; keep a locked set and count every configuration tried so tuning noise is not mistaken for progress.

## 9. Calibration

Examples of parameter derivation:

```text
SA accepts worsening delta -d with probability p at T:
T = -d / log(p)

Geometric schedule at progress r in [0,1]:
T(r) = T0 * (Tf/T0)^r

Restart target for per-run success p_s and K runs:
P(success) = 1 - (1-p_s)^K

Beam work approximately steps * width * branching
Population work approximately population * generations
MCTS work approximately search_time / rollout_cost
```

Estimate `d` from observed loss quantiles and choose desired early/late acceptance. Recalibrate when representation, score normalization, or move mix changes. Do not copy fixed temperatures, move percentages, or ruin sizes across instances without scale normalization.

## 10. Time policy and multiple instances

Use a monotonic clock and an internal deadline. Time controls both stopping and policy:

```text
early: diverse construction / larger moves / model learning
middle: main improvement process
late: intensification and safe local polish
final: validation and serialization of saved best
```

For several instances sharing a total budget, allocate by measured move cost, size, log search scale, gap to a bound or external scoring baseline, or pilot improvement rate. Retain I/O and worst-case slack. Define stagnation in both iterations and time when move costs vary.

## 11. Failure-directed iteration

| Evidence | First redesign |
|---|---|
| Invalid proposals or repair dominates | Constraint-aware proposal/state invariant |
| Full score/copy/allocation dominates | Local cache, delta, apply/undo, buffer reuse |
| Few true-score-changing moves | Natural representation or stronger compound move |
| Starts remain separated | Reachability move, perturbation, temporary infeasibility, restart |
| Proxy improves but truth does not | Rescale/rebuild proxy; stage or lexicographic objective |
| Normal acceptance but no best gain | Neighborhood or objective issue, not temperature |
| Best-only gains with worse median/tail | Risk policy, more robust construction, validation criterion |
| Runtime occasionally spikes | Cap repair/subsolve, reserve deadline, remove variable tail |
| Cache/rollback drift | Exact recomputation, property tests, simpler state update |

State, moves, and evaluation usually dominate the choice of metaheuristic.
